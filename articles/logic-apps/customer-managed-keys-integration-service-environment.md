---
title: ISEs의 미사용 데이터를 암호화하도록 고객 관리 키 설정
description: 자체 암호화 키를 생성하고 관리하여 Azure Logic Apps에서 통합 서비스 환경(ISEs)에 대한 미사용 데이터를 보호합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127641"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Azure Logic Apps에서 통합 서비스 환경(ISEs)에 대한 미사용 데이터를 암호화하도록 고객 관리 키 설정

Azure 논리 앱은 Azure 저장소를 사용하여 [미사용 데이터를](../storage/common/storage-service-encryption.md)저장하고 자동으로 암호화합니다. 이 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. 기본적으로 Azure Storage는 Microsoft에서 관리하는 키를 사용하여 데이터를 암호화합니다. Azure Storage 암호화의 작동 방식에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 암호화](../storage/common/storage-service-encryption.md) 및 [미사용](../security/fundamentals/encryption-atrest.md)데이터 암호화 를 참조하십시오.

논리 앱을 호스팅하기 위한 [ISE(통합 서비스 환경)를](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 만들고 Azure Storage에서 사용하는 암호화 키를 보다 세게 제어하려는 경우 [Azure Key Vault를](../key-vault/key-vault-overview.md)사용하여 자체 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK(사용자 고유의 키 가져오기)라고도 하며 키를 "고객 관리 키"라고 합니다.

이 항목에서는 Logic Apps REST API를 사용하여 ISE를 만들 때 사용할 고유한 암호화 키를 설정하고 지정하는 방법을 보여 줍니다. 논리 앱 REST API를 통해 ISE를 만드는 일반적인 단계는 [논리 앱 REST API를 사용하여 ISE(통합 서비스 환경 만들기)를](../logic-apps/create-integration-service-environment-rest-api.md)참조하십시오.

## <a name="considerations"></a>고려 사항

* 현재 ISE에 대한 고객 관리 키 지원은 미국 서부 2, 미국 동부 및 미국 중남부와 같은 Azure 지역에서만 사용할 수 있습니다.

* 나중에가 아니라 *ISE를 만들 때만*고객 관리 키를 지정할 수 있습니다. ISE를 만든 후에는 이 키를 비활성화할 수 없습니다. 현재 ISE에 대한 고객 관리 키를 회전하는 지원은 없습니다.

* 고객 관리 키를 지원하려면 ISE에서 [시스템 할당된 관리 ID를](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) 사용하도록 설정해야 합니다. 이 ID를 사용하면 ISE가 다른 Azure Active Directory(Azure AD) 테넌트의 리소스에 대한 액세스를 인증하여 자격 증명으로 로그인할 필요가 없습니다.

* 현재 고객 관리 키를 지원하고 시스템 할당 ID를 사용하도록 설정한 ISE를 만들려면 HTTPS PUT 요청을 사용하여 논리 앱 REST API를 호출해야 합니다.

* ISE를 만드는 HTTPS PUT 요청을 보낸 후 *30분* 이내에 [ISE의 시스템 할당 ID에 대한 키 자격 증명 모음 액세스 권한을 부여해야](#identity-access-to-key-vault)합니다. 그렇지 않으면 ISE 생성에 실패하고 사용 권한 오류를 발생시면 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 포털에서 ISE를 만들 때와 동일한 [필수 구성 조건](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 요구 [사항으로 ISE에 대한 액세스를 사용하도록 설정합니다.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

* **소프트 삭제** 및 **지우기 안** 함 속성이 활성화된 Azure 키 자격 증명 모음

  이러한 속성을 사용하도록 설정하는 자세한 내용은 [Azure Key Vault 소프트 삭제 개요](../key-vault/key-vault-ovw-soft-delete.md) 및 Azure Key [Vault를 사용 하 여 고객 관리 키 구성](../storage/common/storage-encryption-keys-portal.md)을 참조 하십시오. Azure 키 볼트를 사용하는 경우 Azure 포털을 사용하거나 Azure PowerShell 명령인 [New-AzKeyVault를](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)사용하여 [키 자격 증명 모음을 만드는 방법을](../key-vault/quick-create-portal.md#create-a-vault) 알아봅니다.

* 키 자격 증명 모음에서 이러한 속성 값으로 만든 키:

  | 속성 | 값 |
  |----------|-------|
  | **키 유형** | RSA |
  | **RSA 키 크기** | 2048 |
  | **사용** | yes |
  |||

  ![고객 관리 암호화 키 만들기](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  자세한 내용은 [Azure 키 자격 증명 모음또는 Azure](../storage/common/storage-encryption-keys-portal.md) PowerShell 명령인 [Add-AzKeyVaultKey를](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)통해 고객 관리 키 구성을 참조하십시오.

* HTTPS PUT 요청으로 논리 앱 REST API를 호출하여 ISE를 만드는 데 사용할 수 있는 도구입니다. 예를 들어 [Postman을](https://www.getpostman.com/downloads/)사용하거나 이 작업을 수행하는 논리 앱을 빌드할 수 있습니다.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>키 자격 증명 모음 및 관리되는 ID 지원을 사용하여 ISE 만들기

논리 앱 REST API를 호출하여 ISE를 만들려면 다음 HTTPS PUT 요청을 수행하십시오.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> 로직 앱 REST API 2019-05-01 버전은 ISE 커넥터에 대한 HTTP PUT 요청을 직접 만들어야 합니다.

배포를 완료하는 데 일반적으로 2시간 이내걸립니다. 경우에 따라 배포에 최대 4시간이 걸릴 수 있습니다. Azure [포털](https://portal.azure.com)에서 배포 상태를 확인하려면 Azure 도구 모음에서 알림 창을 여는 알림 아이콘을 선택합니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이 지연은 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 기다려야 할 수 있음을 의미합니다.
>
> 가상 네트워크를 삭제하는 경우 Azure는 일반적으로 서브넷을 해제하기까지 최대 2시간이 걸리지만 이 작업은 더 오래 걸릴 수 있습니다. 
> 가상 네트워크를 삭제할 때는 여전히 연결된 리소스가 없는지 확인합니다. 
> [가상 네트워크 삭제를](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)참조하십시오.

### <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함합니다.

* `Content-type`: 이 속성 `application/json`값을 로 설정합니다.

* `Authorization`: 사용하려는 Azure 구독 또는 리소스 그룹에 액세스할 수 있는 고객에 대해 이 속성 값을 보유자 토큰으로 설정합니다.

### <a name="request-body"></a>요청 본문

요청 본문에서 ISE 정의에 해당 정보를 제공하여 이러한 추가 항목에 대한 지원을 사용하도록 설정합니다.

* ISE가 키 자격 증명 모음에 액세스하는 데 사용하는 시스템 할당된 관리 ID
* 키 자격 증명 모음 및 사용하려는 고객 관리 키

#### <a name="request-body-syntax"></a>요청 본문 구문

ISE를 만들 때 사용할 속성을 설명하는 요청 본문 구문은 다음과 같습니다.

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>요청 본문 예제

이 예제 요청 본문에는 샘플 값이 표시됩니다.

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

ISE를 만들기 위해 HTTP PUT 요청을 보낸 후 *30분* 이내에 ISE의 시스템 할당 ID에 대한 키 자격 증명 모음에 액세스 정책을 추가해야 합니다. 그렇지 않으면 ISE에 대한 생성이 실패하고 사용 권한 오류가 발생합니다. 

이 작업의 경우 Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 명령을 사용하거나 Azure 포털에서 다음 단계를 수행할 수 있습니다.

1. Azure [포털에서](https://portal.azure.com)Azure 키 자격 증명 모음을 엽니다.

1. 키 자격 증명 모음 메뉴에서 **액세스 정책** > **추가 정책(예:**

   ![시스템 할당된 관리되는 ID에 대한 액세스 정책 추가](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. 액세스 **추가 정책** 창이 열리면 다음 단계를 따르십시오.

   1. 다음 옵션을 선택합니다.

      | 설정 | 값 |
      |---------|--------|
      | **템플릿(선택 사항) 목록에서 구성** | 키 관리 |
      | **주요 권한** | - **키 관리 작업**: 받기, 목록 <p><p>- **암호화 작업**: 키 래핑 해제, 둘러싸기 키 |
      |||

      !["키 관리" > "키 권한" 선택](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. **선택 주체의**경우 **선택 없음을 선택합니다.** 보안 **창이** 열리면 검색 창에서 ISE를 찾아 선택합니다. 작업이 완료되면**추가** **선택을** > 선택합니다.

      ![ISE를 주 서버로 사용할 지 선택](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. **액세스 정책** 창이 완료되면 **저장을**선택합니다.

자세한 내용은 [관리되는 ID를 사용한 키 자격 증명 인증 제공을](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault](../key-vault/key-vault-overview.md)에 대해 자세히 알아봅니다.