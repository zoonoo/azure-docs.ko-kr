---
title: Azure Service Fabric 중앙 비밀 서비스
description: 이 문서에서는 Azure Service Fabric에서 중앙 비밀 서비스를 사용하는 방법을 설명합니다.
author: amenarde
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: anmenard
ms.openlocfilehash: 1c920631d671ad2af9fa2e723fc98bf96462b007
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950110"
---
# <a name="central-secret-service-in-azure-service-fabric"></a>Azure Service Fabric 중앙 비밀 서비스 
중앙 비밀 저장소라고도 하는 CSS(중앙 비밀 서비스)는 클러스터 내에서 비밀을 보호하기 위한 Service Fabric 시스템 서비스입니다. CSS는 SF 애플리케이션의 비밀을 쉽게 관리하므로 암호화된 매개 변수를 사용할 필요가 없습니다.

중앙 비밀 서비스는 복제된 강력한 클러스터 내 비밀 캐시입니다. CSS에 저장된 비밀은 미사용 시 고객이 제공한 암호화 인증서로 암호화됩니다. CSS는 클러스터로 인증하는 엔터티 또는 클러스터 관리 사용자가 액세스할 수 있는 비밀 관리를 위한 클라이언트 API를 제공합니다. Service Fabric 런타임 애플리케이션 모델은 CSS와 통합되어 애플리케이션 매개 변수를 CSS 비밀 참조로 선언할 수 있습니다. 

또한 CSS는 [Azure 배포 Service Fabric 애플리케이션에 대한 관리 ID](concepts-managed-identity.md)와 함께 [KeyVault 비밀 URI](service-fabric-keyvault-references.md)로 선언된 애플리케이션 비밀을 프로비저닝하는 데 중요합니다.

중앙 비밀 서비스는 Azure Key Vault와 같은 전용 외부 비밀 관리 서비스를 대신하기 위해 사용되지는 않습니다. 

  > [!NOTE] 
  > [7.1 CU3](service-fabric-versions.md#service-fabric-version-name-and-number-reference) 이전 버전을 실행하는 SF 클러스터에서 CSS를 활성화하는 경우 클러스터가 Windows 인증을 위해 구성되거나 `EncryptionCertificateThumbprint`가 잘못 선언되었거나 해당 인증서가 설치되지 않은 경우 정품 인증이 실패하고 CSS는 영구적으로 비정상 상태가 될 수 있습니다. 두 경우 모두 클러스터를 7.1보다 최신 SF 런타임 버전으로 업그레이드하는 것이 좋습니다. 계속하려면 7.1.CU3을 참조하세요. 
  
## <a name="enable-central-secrets-service"></a>중앙 비밀 서비스 사용
중앙 비밀 서비스를 사용하도록 설정하려면 아래 설명된 대로 클러스터 구성을 업데이트합니다. 클러스터 인증서와는 다른 암호화 인증서를 사용하는 것이 좋습니다. 이 인증서는 모든 디바이스에 설치해야 합니다.
```json
{ 
    "fabricSettings": [
        {
            "name":  "CentralSecretService",
            "parameters":  [
                {
                    "name":  "DeployedState",
                    "value":  "enabled"
                },
                {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
            ]
        }
    ]
}
```
  > [!NOTE] 
  > Service Fabric [버전 8.0](service-fabric-versions.md#service-fabric-version-name-and-number-reference)에 도입된 구성 설정 "DeployedState"는 CSS를 사용하거나 사용하지 않도록 설정하는 기본 메커니즘입니다. 이 기능이 이전 버전에서는 구성 설정 "IsEnabled"를 통해 제공되었습니다(현재는 사용되지 않음).

## <a name="central-secret-service-secret-model"></a>중앙 비밀 서비스 비밀 모델
중앙 비밀 서비스 API는 비밀 리소스 및 비밀 버전의 두 가지 유형으로 표시됩니다. 비밀 리소스 유형은 개념적으로 특정 용도로 사용되는 단일 비밀의 버전 제품군을 나타냅니다. 연결 문자열, 암호, 엔드포인트 인증서 등을 예로 들 수 있습니다. 비밀 리소스 유형의 개체에는 해당 비밀과 연결된 메타데이터, 특히 종류, 콘텐츠 형식 및 설명이 포함됩니다. 비밀 버전 유형은 연결된 비밀의 특정 인스턴스를 나타내며 비밀 일반 텍스트(암호화됨)를 저장합니다. 위의 예제에서 계속 살펴보면 비밀 버전에는 현재 암호 값, 월말까지 유효한 인증서 개체 등이 포함됩니다. 이러한 비밀을 갱신하면 새 비밀 버전이 생성되고 CSS에 추가되어야 합니다.

모델을 공식화하면 CSS 구현에서 다음과 같은 규칙이 구현되고 적용됩니다.

- 비밀 리소스에는 0개 이상의 버전이 있을 수 있습니다.
- 모든 비밀 버전은 특정 비밀 리소스의 자식입니다. 버전에는 부모 리소스가 하나만 있을 수 있습니다.
- 동일한 비밀의 다른 버전에 영향을 주지 않으면서 개별 비밀 버전을 삭제할 수 있습니다.
- 비밀 리소스를 삭제하면 해당 버전이 모두 삭제됩니다.  
- 비밀 버전의 값은 변경할 수 없습니다.
    
비밀 리소스에 대한 REST 관리 API의 전체 집합은 [여기](/rest/api/servicefabric/sfclient-index-meshsecrets)에서 찾을 수 있으며 비밀 버전의 경우 [여기](/rest/api/servicefabric/sfclient-index-meshsecretvalues)에서 찾을 수 있습니다.

### <a name="declare-a-secret-resource"></a>비밀 리소스 선언
REST API를 사용하여 비밀 리소스를 만들 수 있습니다.

  > [!NOTE] 
  > 클러스터가 Windows 인증을 사용하는 경우 REST 요청은 보안되지 않은 HTTP 채널을 통해 전송됩니다. 이 채널에 대해 TLS를 사용하도록 설정하려면 http 게이트웨이 서버 인증서를 지정하도록 클러스터 정의를 업데이트해야 합니다.

REST API를 사용하여 `supersecret` 비밀 리소스를 만들려면 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`에 PUT 요청을 만듭니다. 비밀 리소스를 만들려면 클러스터 인증서 또는 관리 클라이언트 인증서를 사용하여 인증해야 합니다.
```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

### <a name="set-the-secret-value"></a>비밀 값 설정
다음 스크립트를 사용하여 비밀 값을 설정하도록 REST API를 사용합니다.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```

### <a name="examine-the-secret-value"></a>비밀 값 검사
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```

## <a name="use-the-secret-in-your-application"></a>애플리케이션에서 비밀 사용
애플리케이션은 비밀을 환경 변수로 선언하거나 비밀 일반 텍스트를 직렬화할 경로를 지정하여 CSS의 비밀을 사용할 수 있습니다. CSS 비밀을 참조하려면 다음 단계를 수행합니다.

1. 다음 코드 조각을 사용하여 **settings.xml** 파일에 섹션을 추가합니다. 이 값은 {`secretname:version`} 형식으로 되어 있습니다.
```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
```

2. **ApplicationManifest.xml** 에 있는 섹션을 가져옵니다.
```xml
<ServiceManifestImport>
<ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
<ConfigOverrides />
<Policies>
 <ConfigPackagePolicies CodePackageRef="Code">
   <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
   </ConfigPackagePolicies>
</Policies>
</ServiceManifestImport>
```
   
예제 1: 컨테이너에 비밀을 탑재합니다. 컨테이너 내에서 비밀을 사용할 수 있도록 설정하는 데 필요한 유일한 변경 내용은 탑재 지점을 `<ConfigPackage>`에 `specify`하는 것입니다.
다음 코드 조각은 수정된 **ApplicationManifest.xml** 입니다.  
```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
```
컨테이너 내부의 탑재 지점에서 비밀을 사용할 수 있습니다.

예제 2: `Type='SecretsStoreRef`를 지정하여 프로세스 환경 변수에 비밀을 바인딩합니다. 다음 코드 조각은 `supersecret` 버전 `ver1`을 **ServiceManifest.xml** 의 환경 변수 `MySuperSecret`에 바인딩하는 방법의 예제입니다.

```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
```

환경 변수 `SecretPath`는 모든 비밀이 저장된 디렉터리를 가리킵니다. `testsecrets` 섹션 아래에 나열된 각 매개 변수는 별도의 파일에 저장됩니다. 이제 애플리케이션은 다음과 같이 비밀을 사용할 수 있습니다.
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
   
## <a name="rotating-the-central-secret-service-encryption-certificate"></a>중앙 비밀 서비스 암호화 인증서 순환
인증서가 만료된 후 암호 해독에 유효한 상태로 유지된다는 점에 유의해야 합니다. 이때 잠금 가능성을 줄이기 위해 순환 후에도 이전 암호화 인증서를 계속 프로비저닝하는 것이 좋습니다. CSS 암호화 인증서를 순환하려면 다음 단계가 필요합니다.

1. 클러스터의 각 노드에 새 인증서를 프로비저닝합니다. 이때 이전 암호화 인증서를 제거/계속 프로비저닝하지 않도록 합니다.
2. 클러스터 구성 업그레이드를 시작하여 `EncryptionCertificateThumbprint` 값을 새 인증서의 SHA-1 지문으로 변경합니다.
업그레이드가 완료되면 CSS에서 기존 콘텐츠를 새 암호화 인증서로 다시 암호화하기 시작합니다. 이 시점 이후에 CSS에 추가된 모든 비밀은 새 암호화 인증서에 직접 암호화됩니다. 모든 비밀이 새 인증서로 보호되도록 하는 것은 비동기적이므로 이전 암호화 인증서는 모든 노드에 설치되어 있고 CSS에서 사용할 수 있는 상태로 유지되는 것이 중요합니다.

## <a name="removing-central-secret-service-from-your-cluster"></a>클러스터에서 중앙 비밀 서비스 제거
클러스터에서 중앙 비밀 서비스를 안전하게 제거하려면 두 가지 업그레이드가 필요합니다. 첫 번째 업그레이드는 CSS를 기능적으로 사용하지 않도록 설정하지만, 두 번째 업그레이드는 클러스터 정의에서 서비스를 제거하고 해당 콘텐츠를 영구적으로 삭제합니다. 이 두 단계 프로세스는 실수로 서비스가 삭제되는 것을 방지하고 제거 프로세스 중에 CSS에 대한 분리된 종속성이 발생하지 않도록 합니다. 이 기능은 SF 버전 8.0부터 사용할 수 있습니다.

### <a name="step-1-update-css-deployedstate-to-removing"></a>1단계: CSS DeployedState를 removing으로 업데이트
클러스터 정의를 `"IsEnabled" = "true"`에서 또는 `"DeployedState" = "enabled"`에서 다음으로 업그레이드합니다.
```json
{
    "name":  "DeployedState",
    "value":  "removing"
}
```
중앙 비밀 서비스가 배포된 상태 `Removing`이 되면 SecretStoreRefs 또는 KeyVaultReferences를 포함하는 서비스의 활성화를 통해 직접 REST 호출을 비롯한 들어오는 모든 비밀 API 호출을 거부합니다. 이 시점에서 여전히 CSS에 의존하는 클러스터의 애플리케이션 또는 구성 요소는 Warning 상태가 됩니다. 이 경우 배포된 상태 `Removing`로의 업그레이드를 롤백해야 합니다. 업그레이드가 이미 성공한 경우 CSS를 DeployedState = `Enabled`로 다시 변경하기 위해 새 업그레이드를 시작해야 합니다. 중앙 비밀 서비스가 배포된 상태 `Removing`인 동안 요청을 받으면 HTTP 코드 401(권한 없음)을 반환하고 경고 상태가 됩니다.

### <a name="step-2-update-css-deployedstate-to-disabled"></a>2단계: CSS DeployedState를 disabled로 업데이트
클러스터 정의를 `"DeployedState" = "removing"`에서 다음으로 업그레이드합니다.
```json
{
    "name":  "DeployedState",
    "value":  "disabled"
}
```
중앙 비밀 서비스는 클러스터에서 더 이상 실행되지 않아야 하며 시스템 서비스 목록에 표시되지 않습니다. CSS의 콘텐츠는 복구할 수 없게 손실됩니다. 

## <a name="next-steps"></a>다음 단계

- [애플리케이션 및 서비스 보안](service-fabric-application-and-service-security.md)에 대해 자세히 알아봅니다.
- [Service Fabric 애플리케이션에 대한 관리 ID](concepts-managed-identity.md)에 대해 알아봅니다.
- [KeyVaultReferences](service-fabric-keyvault-references.md)를 사용하여 CSS 기능 확장