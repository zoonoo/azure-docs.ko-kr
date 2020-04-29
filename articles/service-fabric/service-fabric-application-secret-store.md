---
title: Azure Service Fabric 중앙 비밀 저장소
description: 이 문서에서는 Azure Service Fabric에서 중앙 비밀 저장소를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770420"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric의 중앙 비밀 저장소 
이 문서에서는 Azure Service Fabric에서 CSS (중앙 비밀 저장소)를 사용 하 여 Service Fabric 응용 프로그램에서 비밀을 만드는 방법을 설명 합니다. CSS는 중요 한 데이터 (예: 암호, 토큰, 키 등)를 메모리에 암호화 된 상태로 유지 하는 로컬 암호 저장소 캐시입니다.

## <a name="enable-central-secrets-store"></a>중앙 비밀 저장소 사용
다음 스크립트를의 클러스터 구성 `fabricSettings` 에 추가 하 여 CSS를 사용 하도록 설정 합니다. CSS에 대 한 클러스터 인증서 이외의 인증서를 사용 하는 것이 좋습니다. 암호화 인증서가 모든 노드에 설치 되어 있고 인증서의 개인 키 `NetworkService` 에 대 한 읽기 권한이 있는지 확인 합니다.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>비밀 리소스 선언
REST API를 사용 하 여 비밀 리소스를 만들 수 있습니다.
  > [!NOTE] 
  > 클러스터가 windows 인증을 사용 하는 경우 REST 요청은 보안 되지 않은 HTTP 채널을 통해 전송 됩니다. 보안 끝점이 포함 된 X509 기반 클러스터를 사용 하는 것이 좋습니다.

REST API를 사용 `supersecret` 하 여 비밀 리소스를 만들려면에 대 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`한 PUT 요청을 수행 합니다. 비밀 리소스를 만들려면 클러스터 인증서 또는 관리 클라이언트 인증서가 필요 합니다.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>비밀 값 설정

다음 스크립트를 사용 하 여 REST API 사용 하 여 비밀 값을 설정 합니다.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>비밀 값 검사
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>응용 프로그램에서 암호 사용

Service Fabric 응용 프로그램에서 암호를 사용 하려면 다음 단계를 수행 합니다.

1. 다음 코드 조각을 사용 하 여 **설정 .xml** 파일에 섹션을 추가 합니다. 이 값은 {`secretname:version`} 형식으로 되어 있습니다.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. **Applicationmanifest .xml**의 섹션을 가져옵니다.
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

   환경 변수 `SecretPath` 는 모든 비밀이 저장 된 디렉터리를 가리킵니다. `testsecrets` 섹션 아래에 나열 된 각 매개 변수는 별도의 파일에 저장 됩니다. 이제 응용 프로그램은 다음과 같이 암호를 사용할 수 있습니다.
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 컨테이너에 비밀을 탑재 합니다. 컨테이너 `specify` 내에서 암호를 사용할 수 있도록 설정 하는 데 필요한 유일한 변경 내용은의 `<ConfigPackage>`탑재 지점입니다.
다음 코드 조각은 수정 된 **Applicationmanifest .xml**입니다.  

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
   컨테이너 내부의 탑재 지점에서 암호를 사용할 수 있습니다.

1. 을 지정 `Type='SecretsStoreRef`하 여 암호를 프로세스 환경 변수에 바인딩할 수 있습니다. 다음 코드 `supersecret` 조각은 **servicemanifest.xml**의 환경 변수에 `ver1` `MySuperSecret` 버전을 바인딩하는 방법의 예입니다.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>다음 단계
[응용 프로그램 및 서비스 보안](service-fabric-application-and-service-security.md)에 대해 자세히 알아보세요.
