---
title: 고객 관리 키를 사용 하 여 구성 데이터 암호화
description: 고객 관리 키를 사용 하 여 구성 데이터 암호화
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 8942c93b7346613b8cfdc97d9afe09f1c473fb10
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384874"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>고객 관리 키를 사용 하 여 앱 구성 데이터 암호화
Azure 앱 구성은 [휴지 상태의 중요 한 정보를 암호화](../security/fundamentals/encryption-atrest.md)합니다. 고객 관리 키를 사용 하면 암호화 키를 관리 하도록 허용 하 여 향상 된 데이터 보호 기능을 제공 합니다.  관리 키 암호화를 사용 하는 경우 앱 구성의 모든 중요 한 정보는 사용자 제공 Azure Key Vault 키로 암호화 됩니다.  이를 통해 요청 시 암호화 키를 회전할 수 있습니다.  또한 키에 대 한 앱 구성 인스턴스의 액세스를 취소 하 여 중요 한 정보에 대 한 Azure 앱 구성의 액세스를 취소 하는 기능을 제공 합니다.

## <a name="overview"></a>개요 
Azure 앱 구성은 Microsoft에서 제공 하는 256 비트 AES 암호화 키를 사용 하 여 미사용의 중요 한 정보를 암호화 합니다. 모든 앱 구성 인스턴스에는 서비스에서 관리 하며 중요 한 정보를 암호화 하는 데 사용 되는 자체 암호화 키가 있습니다. 중요 한 정보에는 키-값 쌍에 있는 값이 포함 됩니다.  고객이 관리 하는 키 기능을 사용 하도록 설정 하면 앱 구성에서 앱 구성 인스턴스에 할당 된 관리 id를 사용 하 여 Azure Active Directory 인증 합니다. 그런 다음 관리 되는 id는 Azure Key Vault를 호출 하 고 앱 구성 인스턴스의 암호화 키를 래핑합니다. 그러면 래핑된 암호화 키가 저장 되 고 래핑 되지 않은 암호화 키가 1 시간 동안 앱 구성 내에 캐시 됩니다. 앱 구성에서는 앱 구성 인스턴스의 암호화 키를 매시간 래핑 해제 된 버전으로 새로 고칩니다. 이렇게 하면 정상적인 운영 상태에서 가용성을 보장 합니다. 

>[!IMPORTANT]
> 앱 구성 인스턴스에 할당 된 id에 더 이상 인스턴스의 암호화 키를 래핑 해제할 수 있는 권한이 없거나 관리 되는 키가 영구적으로 삭제 된 경우에는 앱 구성 인스턴스에 저장 된 중요 한 정보를 더 이상 해독할 수 없습니다. Azure Key Vault의 [일시 삭제](../key-vault/general/soft-delete-overview.md) 기능을 사용 하면 암호화 키를 실수로 삭제할 가능성이 줄어듭니다.

사용자가 Azure 앱 구성 인스턴스에서 고객 관리 키 기능을 사용 하도록 설정 하는 경우 서비스에서 중요 한 정보에 액세스 하는 기능을 제어 합니다. 관리 되는 키는 루트 암호화 키로 사용 됩니다. 사용자는 해당 키 자격 증명 모음 액세스 정책을 변경 하 여 해당 관리 키에 대 한 앱 구성 인스턴스의 액세스를 해지할 수 있습니다. 이 액세스가 취소 되 면 앱 구성에서 1 시간 이내에 사용자 데이터를 암호 해독할 수 없게 됩니다. 이 시점에서 앱 구성 인스턴스는 모든 액세스 시도를 금지할 것입니다. 이 상황은 서비스에 관리 키에 대 한 액세스 권한을 다시 부여 하 여 복구할 수 있습니다.  1 시간 내에 앱 구성은 사용자 데이터를 암호 해독 하 고 정상 상태에서 작동할 수 있습니다.

>[!NOTE]
>모든 Azure 앱 구성 데이터는 격리 된 백업에서 최대 24 시간 동안 저장 됩니다. 여기에는 래핑 해제 된 암호화 키가 포함 됩니다. 서비스 또는 서비스 팀에서이 데이터를 즉시 사용할 수 있는 것은 아닙니다. 응급 복원 시에는 Azure 앱 구성이 관리 되는 키 데이터에서 다시 해지 됩니다.

## <a name="requirements"></a>요구 사항
Azure 앱 구성에 대해 고객이 관리 하는 키 기능을 사용 하도록 설정 하려면 다음 구성 요소가 필요 합니다.
- 표준 계층 Azure 앱 구성 인스턴스
- 일시 삭제 및 제거 보호 기능을 사용 하는 Azure Key Vault
- Key Vault 내의 RSA 또는 RSA-HSM 키
    - 키가 만료 되지 않아야 하 고, 사용 하도록 설정 되어 있어야 하며, 래핑 및 래핑 해제 기능을 모두 사용 하도록 설정 해야 합니다.

이러한 리소스를 구성한 후에는 Azure 앱 구성에서 Key Vault 키를 사용할 수 있도록 두 단계가 남아 있습니다.
1. 관리 id를 Azure 앱 구성 인스턴스에 할당 합니다.
2. `GET` `WRAP` `UNWRAP` 대상 Key Vault의 액세스 정책에서 id, 및 사용 권한을 부여 합니다.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Azure 앱 구성 인스턴스에 대해 고객이 관리 하는 키 암호화 사용
시작 하려면 올바르게 구성 된 Azure 앱 구성 인스턴스가 필요 합니다. 앱 구성 인스턴스를 아직 사용할 수 없는 경우 다음 빠른 시작 중 하나를 수행 하 여 설정 합니다.
- [Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)
- [Azure 앱 구성을 사용 하 여 .NET Core 앱 만들기](quickstart-dotnet-core-app.md)
- [Azure App Configuration을 사용하여 .NET Framework 앱 만들기](quickstart-dotnet-app.md)
- [Azure App Configuration을 사용하여 Java Spring 앱 만들기](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell은 이 문서의 명령줄을 실행하는 데 사용할 수 있는 무료 대화형 셸입니다.  여기에는 .NET Core SDK를 비롯한 일반적인 Azure 도구가 미리 설치되어 있습니다. Azure 구독에 로그인한 경우 shell.azure.com에서 [Azure Cloud Shell](https://shell.azure.com)을 시작합니다.  Azure Cloud Shell에 대한 자세한 내용은 [설명서를 참조하세요](../cloud-shell/overview.md).

### <a name="create-and-configure-an-azure-key-vault"></a>Azure Key Vault 만들기 및 구성
1. Azure CLI를 사용 하 여 Azure Key Vault를 만듭니다.  및는 모두 `vault-name` `resource-group-name` 사용자가 제공 하며 고유 해야 합니다.  `contoso-vault` `contoso-resource-group` 이러한 예제에서는 및를 사용 합니다.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Key Vault에 대해 일시 삭제 및 제거 보호를 사용 하도록 설정 합니다. `contoso-vault`1 단계에서 만든 Key Vault () 및 리소스 그룹 ()의 이름을 대체 합니다 `contoso-resource-group` .

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Key Vault 키를 만듭니다. `key-name`이 키에 대해 고유한를 제공 하 고 `contoso-vault` 1 단계에서 만든 Key Vault ()의 이름을 대체 합니다. 선호 하는지 `RSA` 아니면 암호화를 지정 `RSA-HSM` 합니다.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    이 명령의 출력은 생성 된 키에 대 한 키 ID ("kid")를 표시 합니다.  이 연습 뒷부분에서 사용할 키 ID를 적어 둡니다.  키 ID의 형식은 `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` 입니다.  키 ID에는 세 가지 중요 한 구성 요소가 있습니다.
    1. Key Vault URI: ' https://{my key vault}. 자격 증명 모음. azure .net
    1. Key Vault 키 이름: {키 이름}
    1. Key Vault 키 버전: {Key version}

1. Azure CLI를 사용 하 여 시스템 할당 관리 id를 만들고, 이전 단계에서 사용한 앱 구성 인스턴스 및 리소스 그룹의 이름을 대체 합니다. 관리 되는 id는 관리 되는 키에 액세스 하는 데 사용 됩니다. `contoso-app-config`앱 구성 인스턴스의 이름을 보여 주기 위해를 사용 합니다.
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    이 명령의 출력에는 시스템 할당 id의 보안 주체 ID ("principalId") 및 테 넌 트 ID ("tenandId")가 포함 됩니다.  이러한 Id는 관리 되는 키에 대 한 id 액세스 권한을 부여 하는 데 사용 됩니다.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. 키 유효성 검사, 암호화 및 암호 해독을 수행 하려면 Azure 앱 구성 인스턴스의 관리 되는 id에 키에 대 한 액세스 권한이 있어야 합니다. 액세스 해야 하는 특정 작업 집합에는 `GET` , `WRAP` 및 `UNWRAP` 키에 대 한가 포함 됩니다.  액세스 권한을 부여 하려면 앱 구성 인스턴스의 관리 id의 보안 주체 ID가 필요 합니다. 이 값은 이전 단계에서 가져온 것입니다. 다음과 같이 표시 됩니다 `contoso-principalId` . 명령줄을 사용 하 여 관리 되는 키에 대 한 사용 권한을 부여 합니다.

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure 앱 구성 인스턴스가 관리 키에 액세스할 수 있으면 Azure CLI를 사용 하 여 서비스에서 고객이 관리 하는 키 기능을 사용 하도록 설정할 수 있습니다. 키 생성 단계에서 기록 된 다음 속성을 회수 `key name` `key vault URI` 합니다.

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

이제 Azure 앱 구성 인스턴스가 Azure Key Vault에 저장 된 고객 관리 키를 사용 하도록 구성 되었습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 암호화에 고객이 관리 하는 키를 사용 하도록 Azure 앱 구성 인스턴스를 구성 했습니다.  [Azure 관리 id와 서비스를 통합](howto-integrate-azure-managed-service-identity.md)하는 방법에 대해 알아봅니다.
