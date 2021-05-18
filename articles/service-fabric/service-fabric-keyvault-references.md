---
title: Azure Service Fabric - Service Fabric 애플리케이션 KeyVault 참조 사용
description: 이 문서에서는 애플리케이션 비밀에 대한 Service Fabric KeyVaultReference 지원을 사용하는 방법에 대해 설명합니다.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898599"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Azure 배포 Service Fabric 애플리케이션에 대한 KeyVaultReference 지원

클라우드 애플리케이션을 빌드할 때 일반적인 문제는 어떻게 비밀을 애플리케이션에 안전하게 배포하는냐 하는 것입니다. 예를 들어 파이프라인 중 또는 운영자에게 키를 노출하지 않고 데이터베이스 키를 애플리케이션에 배포하려고 할 수 있습니다. Service Fabric KeyVaultReference 지원을 사용하면 Key Vault에 저장된 비밀의 URL을 참조하여 애플리케이션에 쉽게 비밀을 배포할 수 있습니다. Service Fabric은 애플리케이션의 관리 ID를 대신하여 해당 비밀 가져오기를 처리하고 비밀을 사용하여 애플리케이션을 활성화합니다.

> [!NOTE]
> Service Fabric 애플리케이션에 대한 KeyVaultReference 지원은 Service Fabric 버전 7.2 CU5부터 GA(미리 보기 제외)입니다. 이 기능을 사용하기 전에 이 버전으로 업그레이드하는 것이 좋습니다.

> [!NOTE]
> Service Fabric 애플리케이션에 대한 KeyVaultReference 지원은 [버전이 지정된](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) 비밀만 지원합니다. 버전이 없는 비밀은 지원되지 않습니다. Key Vault는 Service Fabric 클러스터와 동일한 구독에 있어야 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Service Fabric 애플리케이션에 대한 관리 ID

    Service Fabric KeyVaultReference 지원은 애플리케이션의 관리 ID를 사용하여 애플리케이션을 대신하여 비밀을 가져오므로 애플리케이션이 관리 ID를 통해 배포되고 할당되어야 합니다. 이 [문서](concepts-managed-identity.md)에 따라 애플리케이션에 관리 ID를 사용하도록 설정합니다.

- CSS(중앙 비밀 저장소).

    CSS(중앙 비밀 저장소)는 Service Fabric의 암호화된 로컬 비밀 캐시입니다. 이 기능은 비밀을 Key Vault에서 가져온 후 보호하고 유지하기 위해 CSS를 사용합니다. 이 기능을 사용하려면 이 선택적 시스템 서비스를 사용하도록 설정해야 합니다. 이 [문서](service-fabric-application-secret-store.md)에 따라 CSS를 사용하도록 설정하고 구성합니다.

- Keyvault에 애플리케이션의 관리 ID 액세스 권한 부여

    이 [문서](how-to-grant-access-other-resources.md)를 참조하여 keyvault에 관리 ID 액세스 권한을 부여하는 방법을 참조하세요. 또한 시스템 할당 관리 ID를 사용하는 경우 관리 ID는 애플리케이션 배포 후에만 만들어집니다. 이로 인해 ID에 자격 증명 모음에 대한 액세스 권한을 부여하기 전에 애플리케이션이 비밀 액세스를 시도하는 경쟁 조건이 만들어질 수 있습니다. 시스템에서 할당한 ID의 이름은 `{cluster name}/{application name}/{service name}`입니다.
    
## <a name="use-keyvaultreferences-in-your-application"></a>애플리케이션에서 KeyVaultReferences 사용
KeyVaultReferences는 여러 가지 방법으로 사용될 수 있습니다.
- [환경 변수로](#as-an-environment-variable)
- [컨테이너에 파일로 탑재](#mounted-as-a-file-into-your-container)
- [컨테이너 리포지토리 암호에 대한 참조로](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>환경 변수로

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>컨테이너에 파일로 탑재

- settings.xml에 섹션 추가

    형식 `KeyVaultReference` 및 값 `<KeyVaultURL>`로 `MySecret` 매개 변수 정의

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- ApplicationManifest.xml의 `<ConfigPackagePolicies>`에서 새 섹션 참조

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 서비스 코드에서 비밀 사용

    `<Section  Name=MySecrets>` 아래에 나열된 각 매개 변수는 EnvironmentVariable SecretPath가 가리키는 폴더 아래의 파일입니다. 아래 C# 코드 조각은 애플리케이션에서 MySecret를 읽는 방법을 보여 줍니다.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint는 비밀 값을 포함하는 파일이 탑재될 폴더를 제어합니다.

### <a name="as-a-reference-to-a-container-repository-password"></a>컨테이너 리포지토리 암호에 대한 참조로

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>다음 단계

* [Azure KeyVault 설명서](../key-vault/index.yml)
* [중앙 비밀 저장소에 대해 알아보기](service-fabric-application-secret-store.md)
* [Service Fabric 애플리케이션의 관리 ID에 대해 알아보기](concepts-managed-identity.md)
