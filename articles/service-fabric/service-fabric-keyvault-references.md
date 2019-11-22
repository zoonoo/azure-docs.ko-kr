---
title: Azure Service Fabric-Service Fabric 응용 프로그램 KeyVault 참조 사용 | Microsoft Docs
description: 이 문서에서는 응용 프로그램 암호에 대 한 service fabric KeyVaultReference 지원을 사용 하는 방법을 설명 합니다.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 09/20/2019
ms.author: atsenthi
ms.openlocfilehash: 96da89a00b054767553b0ed3d8debf30c344dd62
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307332"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Service Fabric 응용 프로그램에 대 한 KeyVaultReference 지원 (미리 보기)

클라우드 응용 프로그램을 빌드할 때 일반적인 문제는 응용 프로그램에 필요한 비밀을 안전 하 게 저장 하는 방법입니다. 예를 들어, 컨테이너 리포지토리 자격 증명을 keyvault에 저장 하 고 응용 프로그램 매니페스트에서 참조할 수 있습니다. Service Fabric KeyVaultReference는 관리 Id Service Fabric를 사용 하며 keyvault 암호를 쉽게 참조할 수 있습니다. 이 문서의 나머지 부분에서는 Service Fabric KeyVaultReference를 사용 하는 방법에 대해 자세히 설명 하 고 몇 가지 일반적인 사용법을 포함 합니다.

## <a name="prerequisites"></a>선행 조건

- 응용 프로그램에 대 한 관리 Id (MIT)
    
    Service Fabric KeyVaultReference support는 응용 프로그램의 관리 되는 Id를 사용 하므로 KeyVaultReferences를 사용 하는 응용 프로그램은 관리 되는 Id를 사용 해야 합니다. 이 [문서](concepts-managed-identity.md) 에 따라 응용 프로그램에 관리 되는 id를 사용 하도록 설정 합니다.

- 중앙 비밀 저장소 (CSS).

    CSS (중앙 비밀 저장소)는 서비스 패브릭의 암호화 된 로컬 비밀 캐시 이며 인출 되 면 KeyVaultReference는 CSS로 캐시 됩니다.

    KeyVaultReference 지원에 필요한 모든 기능을 사용 하도록 설정 하려면 `fabricSettings` 아래의 클러스터 구성에 아래를 추가 합니다.

    ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService",
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
                }
                ],
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > CSS에 대 한 별도의 암호화 인증서를 사용 하는 것이 좋습니다. "CentralSecretService" 섹션 아래에 추가할 수 있습니다.

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```

- Keyvault에 응용 프로그램의 관리 되는 id 액세스 권한 부여

    이 [문서](how-to-grant-access-other-resources.md) 를 참조 하 여 keyvault에 관리 id 액세스 권한을 부여 하는 방법을 확인 합니다. 또한 시스템 할당 관리 Id를 사용 하는 경우 관리 id는 응용 프로그램 배포 후에만 생성 됩니다.

## <a name="keyvault-secret-as-application-parameter"></a>응용 프로그램 매개 변수로 keyvault 비밀
응용 프로그램이 keyvault에 저장 된 백 엔드 데이터베이스 암호를 읽어야 하는 Service Fabric KeyVaultReference support를 사용 하면 쉽게 할 수 있습니다. 아래 예제에서는 Service Fabric KeyVaultReference 지원을 사용 하 여 keyvault에서 `DBPassword` 비밀을 읽습니다.

- 설정 .xml에 섹션을 추가 합니다.

    유형 `KeyVaultReference` 및 값을 사용 하 여 `DBPassword` 매개 변수를 정의 `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- `<ConfigPackagePolicies>`에서 ApplicationManifest의 새 섹션을 참조 합니다.

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 응용 프로그램에서 KeyVaultReference 사용

    서비스 인스턴스화에 대 한 Service Fabric는 응용 프로그램의 관리 되는 id를 사용 하 여 KeyVaultReference 매개 변수를 확인 합니다. `<Section  Name=dbsecrets>` 아래에 나열 된 각 매개 변수는 고 environmentvariable SecretPath가 가리키는 폴더 아래에 있는 파일입니다. 아래 C# 코드 조각에서는 응용 프로그램에서 dbpassword를 읽는 방법을 보여 줍니다.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 컨테이너 시나리오의 경우 탑재 지점을 사용 하 여 `secrets` 탑재 되는 위치를 제어할 수 있습니다.

## <a name="keyvault-secret-as-environment-variable"></a>환경 변수로 keyvault 비밀

Service Fabric 환경 변수는 이제 KeyVaultReference 유형을 지원 합니다. 아래 예제에서는 환경 변수를 KeyVault에 저장 된 비밀에 바인딩하는 방법을 보여 줍니다.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>컨테이너 리포지토리 암호로 keyvault 비밀
KeyVaultReference는 컨테이너 RepositoryCredentials에 대해 지원 되는 형식이 며, 아래 예제에서는 keyvault 참조를 컨테이너 리포지토리 암호로 사용 하는 방법을 보여 줍니다.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- 관리 id를 사용 하도록 설정 하지 않고 KeyVaultReference 지원에 대해 관리 id를 사용 하도록 설정 해야 합니다. KeyVaultReference를 사용 하는 경우 응용 프로그램 활성화가 실패 합니다.

- 시스템 할당 id를 사용 하는 경우 응용 프로그램이 배포 된 후에만 생성 되며 순환 종속성이 생성 됩니다. 응용 프로그램이 배포 되 면 keyvault에 할당 된 id 액세스 권한을 시스템에 부여할 수 있습니다. 이름 {cluster}/{application name}/{servicename} 시스템 할당 id를 찾을 수 있습니다.

- Keyvault는 service fabric 클러스터와 동일한 구독에 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure KeyVault 설명서](https://docs.microsoft.com/azure/key-vault/)
