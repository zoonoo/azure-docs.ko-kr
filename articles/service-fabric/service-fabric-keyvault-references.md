---
title: Azure 서비스 패브릭 - 서비스 패브릭 응용 프로그램 KeyVault 참조 사용
description: 이 문서에서는 응용 프로그램 암호에 대한 서비스 패브릭 KeyVaultReference 지원을 사용하는 방법을 설명합니다.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545486"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>서비스 패브릭 응용 프로그램에 대한 KeyVaultReference 지원(미리 보기)

클라우드 응용 프로그램을 빌드할 때 일반적인 과제는 응용 프로그램에 필요한 기밀을 안전하게 저장하는 방법입니다. 예를 들어 컨테이너 리포지토리 자격 증명을 keyvault에 저장하고 응용 프로그램 매니페스트에서 참조할 수 있습니다. 서비스 패브릭 KeyVaultReference는 서비스 패브릭 관리 ID를 사용하며 키볼트 비밀을 쉽게 참조할 수 있습니다. 이 문서의 나머지 부분에서는 서비스 패브릭 KeyVaultReference를 사용하는 방법을 자세히 설명하며 몇 가지 일반적인 사용을 포함합니다.

## <a name="prerequisites"></a>사전 요구 사항

- MIT(응용 프로그램용 관리 ID)
    
    서비스 패브릭 KeyVaultReference 지원은 응용 프로그램의 관리 되는 ID를 사용 하 고 따라서 KeyVault참조를 사용 하 여 계획 하는 응용 프로그램 관리 ID를 사용 해야 합니다. 이 [문서에](concepts-managed-identity.md) 따라 응용 프로그램에 대한 관리되는 ID를 사용하도록 설정합니다.

- 중앙 비밀 저장소 (CSS).

    중앙 비밀 저장소(CSS)는 서비스 패브릭의 암호화된 로컬 비밀 캐시입니다. CSS는 암호, 토큰 및 키와 같은 중요한 데이터를 메모리에서 암호화된 로컬 비밀 저장소 캐시로 유지합니다. 키볼트참조는 일단 인출되면 CSS에 캐시됩니다.

    KeyVaultReference 지원에 필요한 `fabricSettings` 모든 기능을 활성화하려면 아래의 클러스터 구성에 아래를 추가합니다.

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
                }
                ]
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
    > CSS에 대해 별도의 암호화 인증서를 사용하는 것이 좋습니다. "CentralSecretService" 섹션 아래에 추가할 수 있습니다.
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
변경 내용이 적용되도록 업그레이드 정책을 변경하여 업그레이드가 클러스터를 통해 진행됨에 따라 각 노드에서 Service Fabric 런타임을 강제로 다시 시작하도록 지정해야 합니다. 이렇게 다시 시작하면 각 노드에서 새로 활성화된 시스템 서비스가 시작되고 실행됩니다. 아래 스니펫에서 강제재시작은 필수 설정입니다. 설정의 나머지 부분에 대한 기존 값을 사용합니다.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- 키 볼트에 응용 프로그램의 관리되는 ID 액세스 권한을 부여합니다.

    키 볼트에 관리되는 ID 액세스를 부여하는 방법을 보려면 이 [문서를](how-to-grant-access-other-resources.md) 참조하세요. 또한 시스템 할당된 관리 되는 ID를 사용 하는 경우 관리되는 ID는 응용 프로그램 배포 후에만 만들어집니다.

## <a name="keyvault-secret-as-application-parameter"></a>키볼트 비밀을 응용 프로그램 매개 변수로 사용
응용 프로그램이 키 볼트에 저장된 백 엔드 데이터베이스 암호를 읽어야한다고 가정 해 봅시다, 서비스 패브릭 KeyVaultReference 지원은 쉽게. 아래 예제는 서비스 패브릭 KeyVaultReference 지원을 사용하여 키볼트의 비밀을 읽습니다. `DBPassword`

- settings.xml에 섹션 추가

    유형 `DBPassword` `KeyVaultReference` 및 값으로 매개변수 정의`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- 응용 프로그램 매니페스트.xml에서 새 섹션을 참조`<ConfigPackagePolicies>`

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

    서비스 인스턴스화시 서비스 패브릭은 응용 프로그램의 관리되는 ID를 사용하여 KeyVaultReference 매개 변수를 해결합니다. 아래에 `<Section  Name=dbsecrets>` 나열된 각 매개 변수는 환경 변수 SecretPath가 가리키는 폴더 아래에 있는 파일입니다. C # 코드 스니펫 아래 응용 프로그램에서 DBPassword를 읽는 방법을 보여줍니다.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > 컨테이너 시나리오의 경우 MountPoint를 사용하여 `secrets` 탑재할 위치를 제어할 수 있습니다.

## <a name="keyvault-secret-as-environment-variable"></a>환경 변수로 키볼트 비밀

서비스 패브릭 환경 변수는 이제 KeyVaultReference 유형을 지원하며, 아래 예제에서는 환경 변수를 KeyVault에 저장된 비밀에 바인딩하는 방법을 보여 주며 있습니다.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>컨테이너 리포지토리 암호로 키볼트 비밀
KeyVaultReference는 컨테이너 저장소 자격 증명에 대 한 지원 되는 형식, 아래 예제 컨테이너 저장소 암호로 keyvault 참조를 사용 하는 방법을 보여 줍니다.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- KeyVaultReference 지원에 대해 관리되는 ID를 사용하도록 설정해야 하며, 관리되는 ID를 사용하지 않고 KeyVaultReference를 사용하는 경우 응용 프로그램 활성화가 실패합니다.

- 시스템 할당 된 ID를 사용 하는 경우 응용 프로그램을 배포 하 고 순환 종속성을 만듭니다 후에 만들어집니다. 응용 프로그램이 배포되면 키볼트에 ID 액세스 권한을 할당한 시스템에 권한을 부여할 수 있습니다. {cluster}/{응용 프로그램 이름}/{서비스 이름}으로 ID를 할당받은 시스템을 찾을 수 있습니다.

- 키 볼트는 서비스 패브릭 클러스터와 동일한 구독에 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

* [Azure 키볼트 문서](https://docs.microsoft.com/azure/key-vault/)
