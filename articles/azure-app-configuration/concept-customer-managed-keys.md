---
title: 고객 관리형 키를 사용하여 구성 데이터 암호화
description: 고객 관리형 키를 사용하여 구성 데이터 암호화
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930503"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>고객 관리형 키를 사용하여 App Configuration 데이터 암호화
Azure App Configuration은 [중요한 미사용 정보를 암호화](../security/fundamentals/encryption-atrest.md)합니다. 고객 관리형 키를 사용하면 암호화 키를 관리할 수 있으므로 데이터 보호가 향상됩니다.  관리형 키 암호화를 사용하는 경우 App Configuration의 모든 중요한 정보는 사용자 제공 Azure Key Vault 키로 암호화됩니다.  이를 통해 주문형 방식으로 암호화 키를 순환할 수 있습니다.  또한 키에 대한 App Configuration 인스턴스의 액세스 권한을 해지하여 중요한 정보에 대한 Azure App Configuration의 액세스를 취소하는 기능도 제공합니다.

## <a name="overview"></a>개요 
Azure App Configuration은 Microsoft에서 제공하는 256비트 AES 암호화 키를 사용하여 중요한 미사용 정보를 암호화합니다. 모든 App Configuration 인스턴스에는 서비스에서 관리하며 중요한 정보를 암호화하는 데 사용되는 자체 암호화 키가 있습니다. 중요한 정보에는 키-값 쌍의 값이 포함됩니다.  고객 관리형 기능을 사용하도록 설정하면 App Configuration에서 App Configuration 인스턴스에 할당된 관리 ID를 사용하여 Azure Active Directory로 인증합니다. 그런 다음, 관리 ID는 Azure Key Vault를 호출하고 App Configuration 인스턴스의 암호화 키를 래핑합니다. 그러면 래핑된 암호화 키는 저장되고 래핑되지 않은 암호화 키는 1시간 동안 App Configuration 내에 캐시됩니다. App Configuration에서는 App Configuration 인스턴스 암호화 키의 래핑 해제된 버전을 매시간 새로 고칩니다. 이를 통해 정상적인 운영 상태에서 가용성이 보장됩니다. 

>[!IMPORTANT]
> App Configuration 인스턴스에 할당된 ID에 더 이상 인스턴스의 암호화 키를 래핑 해제할 수 있는 권한이 없거나 관리형 키가 영구적으로 삭제된 경우에는 App Configuration 인스턴스에 저장된 중요한 정보를 더 이상 해독할 수 없습니다. Azure Key Vault의 [일시 삭제](../key-vault/general/soft-delete-overview.md) 기능을 사용하면 암호화 키를 실수로 삭제할 가능성이 줄어듭니다.

사용자는 Azure App Configuration 인스턴스에서 고객 관리형 키 기능을 사용하도록 설정하여 중요한 정보에 액세스하는 서비스의 기능을 제어할 수 있습니다. 관리형 키는 루트 암호화 키로 사용됩니다. 사용자는 Key Vault 액세스 정책을 변경하여 관리형 키에 대한 App Configuration 인스턴스의 액세스 권한을 해지할 수 있습니다. 이 액세스 권한이 해지되면 App Configuration은 1시간 이내에 사용자 데이터의 암호를 해독할 수 없게 됩니다. 이때 App Configuration 인스턴스는 모든 액세스 시도를 금지합니다. 이 상황은 관리형 키에 대한 액세스 권한을 서비스에 다시 한번 부여하여 복구할 수 있습니다.  1시간 이내에 App Configuration은 사용자 데이터의 암호를 해독하고 정상적인 조건에서 작동할 수 있습니다.

>[!NOTE]
>모든 Azure App Configuration 데이터는 격리된 백업에 최대 24시간 동안 저장됩니다. 여기에는 래핑되지 않은 암호화 키가 포함됩니다. 이 데이터는 서비스 또는 서비스 팀에서 즉시 사용할 수 없습니다. 응급 복원 시 Azure App Configuration은 관리형 키 데이터에서 자체적으로 다시 해지됩니다.

## <a name="requirements"></a>요구 사항
Azure App Configuration의 고객 관리형 키 기능을 사용하도록 설정하려면 다음 구성 요소가 필요합니다.
- 표준 계층 Azure App Configuration 인스턴스
- 일시 삭제 및 제거 보호 기능이 사용하도록 설정된 Azure Key Vault
- Key Vault 내의 RSA 또는 RSA-HSM 키
    - 키는 만료되지 않아야 하고, 사용하도록 설정해야 하며, 래핑 및 래핑 해제 기능을 모두 사용하도록 설정해야 합니다.

일단 이러한 리소스가 구성된 후에는 두 단계를 더 진행하면 Azure App Configuration에서 Key Vault 키를 사용할 수 있게 됩니다.
1. Azure App Configuration 인스턴스에 관리 ID 할당
2. 대상 Key Vault 액세스 정책에 ID `GET`, `WRAP` 및 `UNWRAP` 권한을 부여합니다.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Azure App Configuration 인스턴스에 대해 고객 관리형 키 암호화 사용
시작하려면 올바르게 구성된 Azure App Configuration 인스턴스가 필요합니다. App Configuration 인스턴스를 아직 사용할 수 없는 경우 다음 빠른 시작 중 하나를 수행하여 설정합니다.
- [Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)
- [Azure App Configuration으로 .NET Core 앱 만들기](quickstart-dotnet-core-app.md)
- [Azure App Configuration을 사용하여 .NET Framework 앱 만들기](quickstart-dotnet-app.md)
- [Azure App Configuration을 사용하여 Java Spring 앱 만들기](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell은 이 문서의 명령줄을 실행하는 데 사용할 수 있는 무료 대화형 셸입니다.  여기에는 .NET Core SDK를 비롯한 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

### <a name="create-and-configure-an-azure-key-vault"></a>Azure Key Vault 만들기 및 구성
1. Azure CLI를 사용하여 Azure Key Vault를 만듭니다.  `vault-name` 및 `resource-group-name`은 모두 사용자가 제공하며 고유해야 합니다.  이러한 예제에서는 `contoso-vault` 및 `contoso-resource-group`을 사용합니다.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Key Vault에 대해 일시 삭제 및 제거 보호를 사용하도록 설정합니다. 1단계에서 만든 Key Vault(`contoso-vault`) 및 리소스 그룹(`contoso-resource-group`)의 이름을 대체합니다.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Key Vault 키를 만듭니다. 이 키에 대해 고유한 `key-name`을 제공하고 1단계에서 만든 Key Vault(`contoso-vault`)의 이름을 대체합니다. `RSA` 또는 `RSA-HSM` 암호화 중 어떤 방식을 선호하는지 지정합니다.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    이 명령의 출력은 생성된 키에 대한 키 ID("kid")를 표시합니다.  이 연습의 뒷부분에서 사용할 수 있도록 결과를 적어 두세요.  키 ID의 형식은 `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`입니다.  키 ID에는 다음과 같은 세 가지 중요한 구성 요소가 있습니다.
    1. Key Vault URI: `https://{my key vault}.vault.azure.net
    1. Key Vault 키 이름: {Key Name}
    1. Key Vault 키 버전: {Key version}

1. Azure CLI를 사용하여 시스템 할당 관리 ID를 만들고, 이전 단계에서 사용한 App Configuration 인스턴스 및 리소스 그룹의 이름을 대체합니다. 관리 ID는 관리형 키에 액세스하는 데 사용됩니다. 여기서는 App Configuration 인스턴스의 이름을 나타내기 위해 `contoso-app-config`를 사용하고 있습니다.
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    이 명령의 출력에는 시스템 할당 ID의 보안 주체 ID("principalId") 및 테넌트 ID("tenandId")가 포함됩니다.  이러한 ID는 관리형 키에 대한 ID 액세스 권한을 부여하는 데 사용됩니다.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. 키 유효성 검사, 암호화 및 암호 해독을 수행하려면 Azure App Configuration 인스턴스의 관리 ID에 키에 대한 액세스 권한이 있어야 합니다. 액세스가 필요한 특정 작업 세트에는 키에 대한 `GET`, `WRAP` 및 `UNWRAP`이 포함됩니다.  액세스 권한을 부여하려면 App Configuration 인스턴스 관리 ID의 보안 주체 ID가 필요합니다. 이 값은 이전 단계에서 가져온 것입니다. 아래에 `contoso-principalId`와 같이 표시됩니다. 명령줄을 사용하여 관리형 키에 대한 사용 권한을 부여합니다.

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure App Configuration 인스턴스가 관리형 키에 액세스할 수 있게 되면 Azure CLI를 사용하여 서비스에서 고객 관리형 키 기능을 사용하도록 설정할 수 있습니다. 키 만들기 단계 중에 기록해 둔 `key name` `key vault URI` 속성을 사용합니다.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

이제 Azure App Configuration 인스턴스가 Azure Key Vault에 저장된 고객 관리형 키를 사용하도록 구성됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 암호화에 고객 관리형 키를 사용하도록 Azure App Configuration 인스턴스를 구성했습니다.  [Azure 관리 ID와 서비스를 통합](howto-integrate-azure-managed-service-identity.md)하는 방법을 알아봅니다.
