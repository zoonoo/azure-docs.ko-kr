---
title: Azure Service Fabric 중앙 비밀 저장소
description: 이 문서에서는 Azure Service Fabric에서 중앙 비밀 저장소를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589167"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Azure Service Fabric의 중앙 비밀 저장소 
이 문서에서는 Azure Service Fabric에서 CSS (중앙 비밀 저장소)를 사용 하 여 Service Fabric 응용 프로그램에서 비밀을 만드는 방법을 설명 합니다. CSS는 중요 한 데이터 (예: 암호, 토큰, 키 등)를 메모리에 암호화 된 상태로 유지 하는 로컬 암호 저장소 캐시입니다.

## <a name="enable-central-secrets-store"></a>중앙 비밀 저장소 사용
CSS를 사용 하도록 설정 하려면 `fabricSettings` 아래의 클러스터 구성에 다음 스크립트를 추가 합니다. CSS에 대 한 클러스터 인증서 이외의 인증서를 사용 하는 것이 좋습니다. 암호화 인증서가 모든 노드에 설치 되어 있고 `NetworkService`에 인증서의 개인 키에 대 한 읽기 권한이 있는지 확인 하십시오.
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
Azure Resource Manager 템플릿 또는 REST API를 사용 하 여 비밀 리소스를 만들 수 있습니다.

### <a name="use-resource-manager"></a>리소스 관리자 사용

다음 템플릿을 사용 하 여 리소스 관리자 사용 하 여 비밀 리소스를 만듭니다. 템플릿이 `supersecret` 비밀 리소스를 만들지만 비밀 리소스에 대해 아직 값이 설정 되지 않았습니다.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>REST API 사용

REST API를 사용 하 여 `supersecret` 비밀 리소스를 만들려면 PUT 요청을 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`합니다. 비밀 리소스를 만들려면 클러스터 인증서 또는 관리 클라이언트 인증서가 필요 합니다.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>비밀 값 설정

### <a name="use-the-resource-manager-template"></a>리소스 관리자 템플릿 사용

다음 리소스 관리자 템플릿을 사용 하 여 비밀 값을 만들고 설정 합니다. 이 템플릿은 `supersecret` 비밀 리소스에 대 한 비밀 값을 버전 `ver1`으로 설정 합니다.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>REST API 사용

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

   환경 변수 `SecretPath`은 모든 비밀이 저장 된 디렉터리를 가리킵니다. `testsecrets` 섹션 아래에 나열 된 각 매개 변수는 별도의 파일에 저장 됩니다. 이제 응용 프로그램은 다음과 같이 암호를 사용할 수 있습니다.
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. 컨테이너에 비밀을 탑재 합니다. 컨테이너 내에서 암호를 사용할 수 있도록 설정 하는 데 필요한 유일한 변경 내용은 `<ConfigPackage>`탑재 지점을 `specify` 하는 것입니다.
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

1. `Type='SecretsStoreRef`를 지정 하 여 보안을 프로세스 환경 변수에 바인딩할 수 있습니다. 다음 코드 조각은 `supersecret` 버전 `ver1`을 **servicemanifest.xml**의 환경 변수 `MySuperSecret`에 바인딩하는 방법의 예입니다.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>다음 단계
[응용 프로그램 및 서비스 보안](service-fabric-application-and-service-security.md)에 대해 자세히 알아보세요.
