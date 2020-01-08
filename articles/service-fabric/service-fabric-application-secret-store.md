---
title: 비밀 저장소 Service Fabric
description: 이 문서에서는 Service Fabric 비밀 저장소를 사용 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 16608d9eaf12fc9abc535ef316d7b5e8b74a8b37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457514"
---
#  <a name="service-fabric-secrets-store"></a>비밀 저장소 Service Fabric
이 문서에서는 CSS (Service Fabric 암호 저장소)를 사용 하 여 Service Fabric 응용 프로그램에서 암호를 만들고 사용 하는 방법을 설명 합니다. CSS는 메모리에서 암호화 된 암호, 토큰 및 키와 같은 중요 한 데이터를 유지 하는 데 사용 되는 로컬 암호 저장소 캐시입니다.

## <a name="enabling-secrets-store"></a>비밀 저장소 사용
 CSS를 사용 하도록 설정 하려면 `fabricSettings` 아래의 클러스터 구성에 아래를 추가 합니다. CSS에 대 한 클러스터 인증서와 다른 인증서를 사용 하는 것이 좋습니다. 암호화 인증서가 모든 노드에 설치 되어 있고 `NetworkService`에 인증서의 개인 키에 대 한 읽기 권한이 있는지 확인 하십시오.
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
## <a name="declare-secret-resource"></a>비밀 리소스 선언
리소스 관리자 템플릿을 사용 하거나 REST API를 사용 하 여 비밀 리소스를 만들 수 있습니다.

* Resource Manager 템플릿 사용
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
위의 템플릿에서 비밀 리소스 `supersecret` 만들지만 비밀 리소스에 대 한 값은 아직 설정 되지 않았습니다.

* REST API 사용

비밀 리소스를 만들려면 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`에 대 한 PUT 요청을 `supersecret` 합니다. 비밀을 만들려면 클러스터 인증서 또는 관리 클라이언트 인증서가 필요 합니다.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>비밀 값 설정
* Resource Manager 템플릿 사용

아래 리소스 관리자 템플릿에서는 버전 `ver1`를 사용 하 여 암호 `supersecret` 값을 만들고 설정 합니다.
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
* REST API 사용

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>응용 프로그램에서 비밀 사용

1.  다음 콘텐츠를 사용 하 여 설정 .xml 파일에 섹션을 추가 합니다. 참고 여기서 값은 {`secretname:version`} 형식입니다.

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. 이제 ApplicationManifest .xml에서 섹션을 가져옵니다.
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

환경 변수 ' SecretPath '은 모든 비밀이 저장 된 디렉터리를 가리킵니다. `testsecrets` 섹션 아래에 나열 된 각 매개 변수는 별도의 파일에 저장 됩니다. 이제 응용 프로그램은 아래와 같이 암호를 사용할 수 있습니다.
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. 컨테이너에 비밀 탑재

컨테이너 내에서 암호를 사용할 수 있도록 설정 하는 데 필요한 변경만 `<ConfigPackage>`의 탑재 지점을 지정 하는 것입니다.
다음은 수정 된 ApplicationManifest. xml입니다.  

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

4. 환경 변수에 비밀 바인딩 

Type = ' SecretsStoreRef '를 지정 하 여 암호를 프로세스 환경 변수에 바인딩할 수 있습니다. `supersecret` 버전 `ver1`을 Servicemanifest.xml의 환경 변수 `MySuperSecret`에 바인딩하는 방법의 예는 다음과 같습니다.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>다음 단계
[애플리케이션 및 서비스 보안](service-fabric-application-and-service-security.md)에 대한 자세한 정보
