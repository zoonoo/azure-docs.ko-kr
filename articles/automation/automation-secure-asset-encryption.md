---
title: Azure Automation에서 보안 자산 암호화
description: Azure 자동화는 여러 수준의 암호화를 사용하여 안전한 자산을 보호합니다. 기본적으로 암호화는 Microsoft에서 관리하는 키를 사용하여 수행됩니다. 고객은 암호화를 위해 고객 관리 키를 사용하도록 자동화 계정을 구성할 수 있습니다. 이 문서에서는 두 암호화 모드의 세부 정보와 둘 사이를 전환하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: a82d2b6d9521ba7dd5e7b194c26ff8fe5a100871
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457487"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Azure Automation에서 보안 자산 암호화

Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 여러 수준의 암호화를 사용하여 Azure 자동화에서 보호됩니다. 암호화에 사용되는 최상위 키를 기반으로 암호화에 대한 두 가지 모델이 있습니다.
-    Microsoft 에서 관리하는 키 사용
-    고객 관리 키 사용

## <a name="microsoft-managed-keys"></a>마이크로소프트가 관리하는 키

기본적으로 Azure 자동화 계정은 Microsoft에서 관리하는 키를 사용합니다.

각 보안 자산은 각 자동화 계정에 대해 생성되는 고유 키(데이터 암호화 키)를 사용하여 Azure 자동화에 암호화되고 저장됩니다. 이러한 키 자체는 AEK(계정 암호화 키)라고 하는 각 계정에 대해 생성되는 또 다른 고유한 키를 사용하여 Azure 자동화에 암호화되고 저장됩니다. 이러한 계정 암호화 키는 암호화되어 Microsoft 관리 키를 사용하여 Azure 자동화에 저장됩니다. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>키 볼트가 있는 고객 관리 키(미리 보기)

자체 키로 자동화 계정에 대한 보안 자산의 암호화를 관리할 수 있습니다. 자동화 계정 수준에서 고객 관리 키를 지정하면 해당 키가 자동화 계정의 계정 암호화 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 이것은 차례로 모든 보안 자산을 암호화하고 해독하는 데 사용됩니다. 고객 관리 키는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 유연성을 제공합니다. 보안 자산을 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

Azure 키 자격 증명 모음을 사용하여 고객 관리 키를 저장합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault API를 사용하여 키를 생성할 수 있습니다.  Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>자동화 계정에 대한 고객 관리 키 사용

자동화 계정에 대한 고객 관리 키로 암호화를 사용하도록 설정하면 Azure Automation은 연결된 키 자격 증명 모음에서 고객 관리 키로 계정 암호화 키를 래핑합니다. 고객 관리 키를 사용하도록 설정해도 성능에 영향을 미치지 않으며 계정은 지체 없이 새 키로 즉시 암호화됩니다.

새 자동화 계정은 항상 Microsoft에서 관리하는 키를 사용하여 암호화됩니다. 계정이 생성될 때 고객 관리 키를 사용하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장되며 키 자격 증명 모음에는 자동화 계정과 연결된 관리되는 ID에 키 권한을 부여하는 액세스 정책이 제공되어야 합니다. 관리되는 ID는 저장소 계정을 만든 후에만 사용할 수 있습니다.

Azure Automation보안 자산 암호화에 사용되는 키를 수정할 때 고객 관리 키를 사용 또는 비활성화하거나, 키 버전을 업데이트하거나, 다른 키를 지정하여 계정 암호화 키를 변경하지만 Azure Automation 계정의 보안 자산을 다시 암호화할 필요가 없습니다.

다음 세 섹션에서는 자동화 계정에 대해 고객 관리 키를 사용하도록 설정하는 메커니즘을 설명합니다. 

> [!NOTE] 
> 고객 관리 키를 사용하려면 API 버전 2020-01-13 미리 보기를 사용하여 Azure 자동화 REST 호출을 수행해야 합니다.

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Azure 자동화에서 고객 관리 키를 사용하기 위한 필수 구성 조건

자동화 계정에 대해 고객 관리 키를 사용하도록 설정하기 전에 다음 필수 구성 조건이 충족되었는지 확인해야 합니다.

 - 고객 관리 키는 Azure 키 자격 증명 모음에 저장됩니다. 
 - 키 자격 증명 모음에서 **소프트 삭제** 및 삭제 **안 건을** 모두 사용하도록 설정합니다. 이러한 기능은 실수로 삭제된 경우 키를 복구할 수 있도록 해야 합니다.
 - AZURE 자동화 암호화를 통해 RSA 키만 지원됩니다. 키에 대한 자세한 내용은 [Azure 키 볼트 정보 키, 암호 및 인증서 를](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)참조하십시오.
- 자동화 계정과 키 자격 증명 모음은 다른 구독에 있을 수 있지만 동일한 Azure Active Directory 테넌트에 있어야 합니다.

### <a name="assign-an-identity-to-the-automation-account"></a>자동화 계정에 ID 할당

자동화 계정으로 고객 관리 키를 사용하려면 자동화 계정이 고객 관리 키를 저장하는 키 자격 증명 모음에 대해 인증해야 합니다. Azure Automation은 관리되는 ID를 할당된 시스템을 사용하여 Azure Key Vault를 사용하여 계정을 인증합니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스용 관리 ID란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)을 참조하세요.

다음 REST API 호출을 사용하여 자동화 계정에 관리되는 ID를 할당된 시스템을 구성합니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

본문 요청:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

자동화 계정에 대해 할당된 시스템 ID는 다음과 유사한 응답으로 반환됩니다.

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>키 볼트 액세스 정책 구성

관리되는 ID가 자동화 계정에 할당되면 고객 관리 키를 저장하는 키 자격 증명 모음에 대한 액세스를 구성합니다. Azure 자동화에는 고객 관리 **키에서** **[** **복구**, **wrapKey**]

이러한 액세스 정책은 다음 REST API 호출을 사용하여 설정할 수 있습니다.

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

본문 요청:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> **테넌트Id** 및 **objectId** 필드는 자동화 계정에 대한 관리ID 응답에서 각각 **identity.tenantId** 및 **identity.principalId의** 값을 제공해야 합니다.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>고객 관리 키를 사용하도록 자동화 계정의 구성 변경

마지막으로 다음 REST API 호출을 사용하여 자동화 계정을 Microsft 관리 키에서 고객 관리 키로 전환할 수 있습니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

본문 요청:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

샘플 응답

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>고객 관리 키 수명 주기 관리

### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리 키를 회전할 수 있습니다. 키가 회전되면 새 키 URI를 사용하려면 자동화 계정을 업데이트해야 합니다.

키를 회전해도 자동화 계정에서 보안 자산의 재암호화가 트리거되지 않습니다. 추가 작업이 필요하지 않습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

고객 관리 키에 대한 액세스를 취소하려면 PowerShell 또는 Azure CLI를 사용합니다. 자세한 내용은 [Azure 키 볼트 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) 또는 Azure 키 볼트 [CLI를](https://docs.microsoft.com/cli/azure/keyvault)참조하십시오. Azure Automation에서 암호화 키에 액세스할 수 없기 때문에 액세스를 취소하면 자동화 계정의 모든 보안 자산에 대한 액세스가 효과적으로 차단됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../key-vault/general/overview.md)

- [Azure Automation의 인증서 자산](shared-resources/certificates.md)

- [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)

- [Azure Automation의 변수 자산](shared-resources/variables.md)
