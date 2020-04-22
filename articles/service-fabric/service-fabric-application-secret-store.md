---
title: Azure 서비스 패브릭 중앙 비밀 저장소
description: 이 문서에서는 Azure 서비스 패브릭에서 중앙 비밀 저장소를 사용하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770420"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure 서비스 패브릭의 중앙 비밀 저장소 
이 문서에서는 Azure 서비스 패브릭에서 CSS(중앙 비밀 저장소)를 사용하여 서비스 패브릭 응용 프로그램에서 비밀을 만드는 방법에 대해 설명합니다. CSS는 암호, 토큰 및 키와 같은 중요한 데이터를 메모리에서 암호화된 로컬 비밀 저장소 캐시로 유지합니다.

## <a name="enable-central-secrets-store"></a>중앙 비밀 저장소 사용
CSS를 사용하도록 설정하려면 `fabricSettings` 아래클러스터 구성에 다음 스크립트를 추가합니다. CSS에 대한 클러스터 인증서 이외의 인증서를 사용하는 것이 좋습니다. 암호화 인증서가 모든 노드에 설치되어 `NetworkService` 있고 인증서의 개인 키에 대한 읽기 권한이 있는지 확인합니다.
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
REST API를 사용하여 비밀 리소스를 만들 수 있습니다.
  > [!NOTE] 
  > 클러스터가 Windows 인증을 사용하는 경우 REST 요청이 보안되지 않은 HTTP 채널을 통해 전송됩니다. 보안 끝점이 있는 X509 기반 클러스터를 사용하는 것이 좋습니다.

REST API를 사용하여 비밀 리소스를 `supersecret` 만들려면 PUT `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`요청을 에 대해 확인합니다. 비밀 리소스를 만들려면 클러스터 인증서 또는 관리자 클라이언트 인증서가 필요합니다.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>비밀 값 설정

다음 스크립트를 사용하여 REST API를 사용하여 비밀 값을 설정합니다.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>비밀 값 검사
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>응용 프로그램의 비밀 사용

서비스 패브릭 응용 프로그램에서 비밀을 사용하려면 다음 단계를 따르십시오.

1. 다음 코드 조각이 있는 **settings.xml** 파일에 섹션을 추가합니다. 여기서 이 값은 {}형식입니다.`secretname:version`

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. **ApplicationManifest.xml**.
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

   환경 변수는 `SecretPath` 모든 암호가 저장되는 디렉터리를 가리킵니다. `testsecrets` 섹션 아래에 나열된 각 매개 변수는 별도의 파일에 저장됩니다. 이제 응용 프로그램은 다음과 같이 비밀을 사용할 수 있습니다.
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 비밀을 용기에 장착합니다. 컨테이너 내부의 비밀을 사용할 수 있도록 하는 `specify` 데 필요한 `<ConfigPackage>`유일한 변경 사항은 에서 마운트 지점입니다.
다음 코드 조각은 수정된 **ApplicationManifest.xml**입니다.  

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
   비밀은 컨테이너 내부의 마운트 포인트 에서 사용할 수 있습니다.

1. 을 지정하여 프로세스 환경 변수에 비밀을 `Type='SecretsStoreRef`바인딩할 수 있습니다. 다음 코드 조각은 **ServiceManifest.xml** `supersecret` 에서 `ver1` 환경을 변수에 `MySuperSecret` 바인딩하는 방법의 예입니다.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>다음 단계
[응용 프로그램 및 서비스 보안에](service-fabric-application-and-service-security.md)대해 자세히 알아보십시오.
