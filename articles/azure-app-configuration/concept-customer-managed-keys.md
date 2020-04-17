---
title: 고객 관리 키를 사용하여 구성 데이터 암호화
description: 고객 관리 키를 사용하여 구성 데이터 암호화
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: ace34cf4a72b871ba6646b279007b8ce21c03e9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457436"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>고객 관리 키를 사용하여 앱 구성 데이터 암호화
Azure 앱 [구성은 미사용 정보를 암호화합니다.](../security/fundamentals/encryption-atrest.md) 고객이 관리하는 키를 사용하면 암호화 키를 관리할 수 있으므로 향상된 데이터 보호 기능을 사용할 수 있습니다.  관리키 암호화를 사용하는 경우 앱 구성의 모든 중요한 정보는 사용자가 제공한 Azure Key Vault 키로 암호화됩니다.  이렇게 하면 필요에 따라 암호화 키를 회전할 수 있습니다.  또한 앱 구성 인스턴스의 키에 대한 액세스를 취소하여 중요한 정보에 대한 Azure 앱 구성의 액세스를 취소하는 기능도 제공합니다.

## <a name="overview"></a>개요 
Azure 앱 구성은 Microsoft에서 제공하는 256비트 AES 암호화 키를 사용하여 미사용 정보를 암호화합니다. 모든 앱 구성 인스턴스에는 서비스에서 관리되고 중요한 정보를 암호화하는 데 사용되는 자체 암호화 키가 있습니다. 중요한 정보에는 키-값 쌍에 있는 값이 포함됩니다.  고객 관리 키 기능을 사용하도록 설정하면 앱 구성은 앱 구성 인스턴스에 할당된 관리되는 ID를 사용하여 Azure Active Directory로 인증합니다. 그런 다음 관리되는 ID는 Azure Key Vault를 호출하고 앱 구성 인스턴스의 암호화 키를 래핑합니다. 래핑된 암호화 키가 저장되고 래핑되지 않은 암호화 키가 앱 구성 내에서 1시간 동안 캐시됩니다. 앱 구성은 매시간 앱 구성 인스턴스의 암호화 키의 래핑되지 않은 버전을 새로 고칩니다. 이를 통해 정상적인 작동 조건에서 가용성을 확보할 수 있습니다. 

>[!IMPORTANT]
> 앱 구성 인스턴스에 할당된 ID가 더 이상 인스턴스의 암호화 키의 래핑을 해제할 권한이 없거나 관리 키가 영구적으로 삭제된 경우 앱 구성 인스턴스에 저장된 중요한 정보를 더 이상 해독할 수 없습니다. Azure Key Vault의 [소프트 삭제](../key-vault/general/overview-soft-delete.md) 기능을 사용하면 실수로 암호화 키가 삭제될 가능성이 완화됩니다.

사용자가 Azure 앱 구성 인스턴스에서 고객이 관리하는 키 기능을 사용하도록 설정하면 서비스의 중요한 정보에 액세스하는 기능을 제어합니다. 관리되는 키는 루트 암호화 키역할을 합니다. 사용자는 키 자격 증명 모음 액세스 정책을 변경하여 관리키에 대한 앱 구성 인스턴스의 액세스를 취소할 수 있습니다. 이 액세스가 취소되면 앱 구성은 1시간 이내에 사용자 데이터를 해독할 수 없게 됩니다. 이 시점에서 앱 구성 인스턴스는 모든 액세스 시도를 금지합니다. 이 상황은 관리되는 키에 대한 서비스 액세스 권한을 다시 한 번 부여하여 복구할 수 있습니다.  1시간 이내에 앱 구성은 사용자 데이터를 해독하고 정상적인 조건에서 작동할 수 있습니다.

>[!NOTE]
>모든 Azure 앱 구성 데이터는 격리된 백업에서 최대 24시간 동안 저장됩니다. 여기에는 래핑되지 않은 암호화 키가 포함됩니다. 이 데이터는 서비스 또는 서비스 팀에서 즉시 사용할 수 없습니다. 긴급 복원이 있는 경우 Azure 앱 구성은 관리되는 키 데이터에서 자체적으로 해지됩니다.

## <a name="requirements"></a>요구 사항
Azure 앱 구성에 대한 고객 관리 키 기능을 성공적으로 활성화하려면 다음 구성 요소가 필요합니다.
- 표준 계층 Azure 앱 구성 인스턴스
- 소프트 삭제 및 퍼지 보호 기능이 활성화된 Azure 키 볼트
- 키 볼트 내의 RSA 또는 RSA-HSM 키
    - 키가 만료되지 않아야 하며, 활성화되어야 하며 랩 핑 및 래핑 해제 기능이 모두 활성화되어 있어야 합니다.

이러한 리소스가 구성되면 Azure 앱 구성에서 키 자격 증명 모음 키를 사용할 수 있도록 하는 두 단계가 남아 있습니다.
1. Azure 앱 구성 인스턴스에 관리되는 ID 할당
2. 대상 Key `GET` `WRAP`Vault의 `UNWRAP` 액세스 정책에서 ID 및 권한을 부여합니다.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Azure 앱 구성 인스턴스에 대해 고객 관리 키 암호화 사용
시작하려면 제대로 구성된 Azure 앱 구성 인스턴스가 필요합니다. 아직 사용할 수 있는 앱 구성 인스턴스가 없는 경우 다음 빠른 시작 중 하나를 수행하여 설정합니다.
- [App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)
- [Azure 앱 구성을 사용하여 .NET 코어 앱 만들기](quickstart-dotnet-core-app.md)
- [Azure App Configuration을 사용하여 .NET Framework 앱 만들기](quickstart-dotnet-app.md)
- [Azure App Configuration을 사용하여 Java Spring 앱 만들기](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell은 이 문서의 명령줄을 실행하는 데 사용할 수 있는 무료 대화형 셸입니다.  여기에는 .NET Core SDK를 비롯한 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

### <a name="create-and-configure-an-azure-key-vault"></a>Azure 키 볼트 만들기 및 구성
1. Azure CLI를 사용하여 Azure 키 자격 증명 모음을 만듭니다.  둘 다 `vault-name` `resource-group-name` 사용자 제공 및 고유 해야 합니다.  우리는 `contoso-vault` 이러한 `contoso-resource-group` 예제에서 사용합니다.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. 키 볼트에 대해 소프트 삭제 및 제거 방지 보호를 사용하도록 설정합니다. 1단계에서 만든 키 자격`contoso-vault`증명 모음`contoso-resource-group`() 및 리소스 그룹() 이름을 대체합니다.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. 키 볼트 키를 만듭니다. 이 키에 대한 고유 `key-name` 정보를 제공하고 1단계에서`contoso-vault`만든 키 높이() 이름을 대신합니다. 원하는지 `RSA` `RSA-HSM` 암호화할지 지정합니다.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    이 명령의 출력에는 생성된 키에 대한 키 ID("kid")가 표시됩니다.  이 연습의 후반부에서 사용할 키 ID를 기록합니다.  키 ID에는 다음과 `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`같은 형식이 있습니다.  키 ID에는 세 가지 중요한 구성 요소가 있습니다.
    1. 키 볼트 URI: 'https://{my 키 볼트}.vault.azure.net
    1. 키 볼트 키 이름: {키 이름}
    1. 키 볼트 키 버전: {키 버전}

1. Azure CLI를 사용하여 관리되는 ID를 할당된 시스템을 만들어 이전 단계에서 사용된 앱 구성 인스턴스 및 리소스 그룹의 이름을 대체합니다. 관리되는 ID는 관리키에 액세스하는 데 사용됩니다. 앱 `contoso-app-config` 구성 인스턴스의 이름을 설명하는 데 사용합니다.
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    이 명령의 출력에는 ID가 할당된 시스템의 주 ID("principalId") 및 테넌트 ID("tenandId")가 포함됩니다.  관리되는 키에 대한 ID 액세스 권한을 부여하는 데 사용됩니다.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. Azure 앱 구성 인스턴스의 관리되는 ID는 키 유효성 검사, 암호화 및 암호 해독을 수행하기 위해 키에 액세스해야 합니다. 액세스가 필요한 특정 작업 집합에는 `GET`의 `WRAP`한 `UNWRAP` 및 키에 대한 내용이 포함됩니다.  액세스 권한을 부여하려면 앱 구성 인스턴스의 관리되는 ID의 주 ID가 필요합니다. 이 값은 이전 단계에서 얻었다. 아래 와 같이 `contoso-principalId`표시됩니다. 명령줄을 사용하여 관리되는 키에 권한을 부여합니다.

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure 앱 구성 인스턴스가 관리 키에 액세스할 수 있게 되면 Azure CLI를 사용하여 서비스에서 고객 관리 키 기능을 활성화할 수 있습니다. 키 만들기 단계에서 `key name` `key vault URI`기록된 다음 속성을 다시 한번 기억하십시오.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

이제 Azure 앱 구성 인스턴스가 Azure Key Vault에 저장된 고객 관리 키를 사용하도록 구성됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 암호화를 위해 고객 관리 키를 사용하도록 Azure 앱 구성 인스턴스를 구성했습니다.  [서비스를 Azure 관리 ID와 통합하는](howto-integrate-azure-managed-service-identity.md)방법에 대해 알아봅니다.