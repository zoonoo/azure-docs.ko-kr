---
title: 구독 이동 후에 키 자격 증명 모음 테넌트 ID 변경 - Azure Key Vault | Microsoft Docs
description: 다른 테넌트에 구독을 이동한 후에 주요 자격 증명 모음에 대한 테넌트 ID를 전환하는 방법을 알아봅니다.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: bc2e73d037b05c012002d7a07e2a2af2431423fa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428915"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>구독 이동 후에 주요 자격 증명 모음 테넌트 ID 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


구독에 새 주요 자격 증명 모음을 만들 때 해당 구독에 대한 기본 Azure Active Directory 테넌트 ID에 자동으로 연결됩니다. 모든 액세스 정책 항목은 이 테넌트 ID에 연결됩니다. 

Azure 구독을 테넌트 A에서 테넌트 B로 이동하는 경우 기존 Key Vaults에는 테넌트 B의 주체(사용자 및 애플리케이션)가 액세스할 수 없게 됩니다. 이 문제를 해결하려면 다음을 수행합니다.

* 이 구독에 있는 모든 기존 Key Vaults와 연결된 테넌트 ID를 테넌트 B로 변경합니다.
* 모든 기존 액세스 정책 항목을 제거합니다.
* 테넌트 B와 연결된 새 액세스 정책 항목을 추가합니다.

예를 들어 테넌트 A에서 테넌트 B로 이동한 구독에 Key Vault 'myvault'가 있는 경우 Azure PowerShell을 사용하여 테넌트 ID를 변경하고 이전 액세스 정책을 제거할 수 있습니다.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

또는 Azure CLI를 사용할 수 있습니다.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

이제 자격 증명 모음이 올바른 테넌트 ID와 연결되고 이전 액세스 정책 항목이 제거되었으므로 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet 또는 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령을 사용하여 새 액세스 정책 항목을 설정합니다.

Azure 리소스에 관리 ID를 사용하는 경우 새 Azure AD 테넌트로도 업데이트해야 합니다. 관리 ID에 대한 자세한 내용은 [관리 ID를 사용하여 Key Vault 인증 제공](managed-identity.md)을 참조하세요.


MSI를 사용하는 경우 이전 ID가 더 이상 올바른 AAD 테넌트에 있지 않으므로 MSI ID도 업데이트해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Key Vault에 대한 질문이 있으면 [Azure Key Vault 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)으로 이동하세요.
