---
title: Azure Service Fabric-Service Fabric 응용 프로그램 KeyVault 참조 사용
description: 이 문서에서는 응용 프로그램 암호에 대 한 service fabric KeyVaultReference 지원을 사용 하는 방법을 설명 합니다.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898599"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Azure 배포 Service Fabric 응용 프로그램에 대 한 KeyVaultReference 지원

클라우드 응용 프로그램을 빌드할 때 일반적인 문제는 응용 프로그램에 비밀을 안전 하 게 배포 하는 방법입니다. 예를 들어 파이프라인 또는 연산자 중에 키를 노출 하지 않고 데이터베이스 키를 응용 프로그램에 배포할 수 있습니다. Service Fabric KeyVaultReference 지원을 사용 하면 Key Vault에 저장 된 암호의 URL을 참조 하 여 응용 프로그램에 쉽게 비밀을 배포할 수 있습니다. Service Fabric는 응용 프로그램의 관리 되는 Id를 대신 하 여 해당 비밀 가져오기를 처리 하 고 암호를 사용 하 여 응용 프로그램을 활성화 합니다.

> [!NOTE]
> Service Fabric 응용 프로그램에 대 한 KeyVaultReference 지원은 Service Fabric 버전 7.2 CU5부터 GA (미리 보기)입니다. 이 기능을 사용 하기 전에이 버전으로 업그레이드 하는 것이 좋습니다.

> [!NOTE]
> Service Fabric 응용 프로그램에 대 한 KeyVaultReference 지원은 [버전이](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) 지정 된 암호를 지원 합니다. Versionless 비밀은 지원 되지 않습니다. Key Vault은 service fabric 클러스터와 동일한 구독에 있어야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- Service Fabric 응용 프로그램에 대 한 관리 Id

    Service Fabric KeyVaultReference 지원에서는 응용 프로그램의 관리 되는 Id를 사용 하 여 응용 프로그램을 대신 하 여 비밀을 페치 하므로 응용 프로그램을를 통해 배포 하 고 관리 되는 id를 할당 해야 합니다. 이 [문서](concepts-managed-identity.md) 에 따라 응용 프로그램에 관리 되는 id를 사용 하도록 설정 합니다.

- 중앙 비밀 저장소 (CSS).

    CSS (중앙 비밀 저장소)는 Service Fabric의 암호화 된 로컬 비밀 캐시입니다. 이 기능은 Key Vault에서 가져온 후에 CSS를 사용 하 여 비밀을 보호 하 고 유지 합니다. 이 기능을 사용 하려면이 선택적 시스템 서비스를 사용 해야 합니다. CSS를 사용 하도록 설정 하 고 구성 하려면이 [문서](service-fabric-application-secret-store.md) 를 따르세요.

- Keyvault에 응용 프로그램의 관리 되는 id 액세스 권한 부여

    이 [문서](how-to-grant-access-other-resources.md) 를 참조 하 여 keyvault에 관리 id 액세스 권한을 부여 하는 방법을 확인 합니다. 또한 시스템 할당 관리 id를 사용 하는 경우 관리 id는 응용 프로그램 배포 후에만 생성 됩니다. 이를 통해 자격 증명 모음에 대 한 액세스 권한을 id에 부여 하기 전에 응용 프로그램이 비밀에 액세스를 시도 하는 경합 상태를 만들 수 있습니다. 시스템에서 할당 한 id의 이름은 `{cluster name}/{application name}/{service name}` 입니다.
    
## <a name="use-keyvaultreferences-in-your-application"></a>응용 프로그램에서 KeyVaultReferences 사용
KeyVaultReferences는 여러 가지 방법으로 사용 될 수 있습니다.
- [환경 변수로](#as-an-environment-variable)
- [파일로 탑재 된 컨테이너](#mounted-as-a-file-into-your-container)
- [컨테이너 리포지토리 암호에 대 한 참조로](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>환경 변수로

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>파일로 탑재 된 컨테이너

- 섹션을 추가 settings.xml

    `MySecret`형식 `KeyVaultReference` 및 값을 사용 하 여 매개 변수 정의`<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- ApplicationManifest.xml의 새 섹션을 참조 하십시오. `<ConfigPackagePolicies>`

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

    아래에 나열 된 각 매개 변수 `<Section  Name=MySecrets>` 는 고 environmentvariable SecretPath가 가리키는 폴더 아래에 있는 파일입니다. 아래 c # 코드 조각에서는 응용 프로그램에서 MySecret를 읽는 방법을 보여 줍니다.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > 탑재는 비밀 값을 포함 하는 파일이 탑재 될 폴더를 제어 합니다.

### <a name="as-a-reference-to-a-container-repository-password"></a>컨테이너 리포지토리 암호에 대 한 참조로

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>다음 단계

* [Azure KeyVault 설명서](../key-vault/index.yml)
* [중앙 비밀 저장소에 대 한 자세한 정보](service-fabric-application-secret-store.md)
* [Service Fabric 응용 프로그램에 대 한 관리 id에 대해 알아보기](concepts-managed-identity.md)
