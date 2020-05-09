---
title: 자격 증명 모음을 다른 구독으로 이동 하는 Azure Key Vault | Microsoft Docs
description: 키 자격 증명 모음을 다른 구독으로 이동 하는 방법에 대 한 지침입니다.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: 4046d4ec5f62ffc4fab50e8c5a4a08fad326aa04
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796955"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>다른 구독으로 Azure Key Vault 이동

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

**키 자격 증명 모음을 다른 구독으로 이동 하면 환경에 대 한 주요 변경 내용이 발생 합니다.**

주요 자격 증명 모음을 새 구독으로 이동 하도록 결정 하기 전에이 변경 내용의 영향을 이해 하 고이 문서의 지침을 따라야 합니다.

키 자격 증명 모음을 만들면 해당 자격 증명 모음이 만들어진 구독에 대 한 기본 Azure Active Directory 테 넌 트 ID에 자동으로 연결 됩니다. 모든 액세스 정책 항목은 이 테넌트 ID에 연결됩니다. Azure 구독을 테 넌 트 A에서 테 넌 트 B로 이동 하는 경우 테 넌 트 B의 서비스 사용자 (사용자 및 응용 프로그램)가 기존 키 자격 증명 모음에 액세스할 수 없게 됩니다. 이 문제를 해결 하려면 다음을 수행 해야 합니다.

* 이 구독에 있는 모든 기존 Key Vaults와 연결된 테넌트 ID를 테넌트 B로 변경합니다.
* 모든 기존 액세스 정책 항목을 제거합니다.
* 테넌트 B와 연결된 새 액세스 정책 항목을 추가합니다.

## <a name="limitations"></a>제한 사항

일부 서비스 주체 (사용자 및 응용 프로그램)는 특정 테 넌 트에 바인딩됩니다. 키 자격 증명 모음을 다른 테 넌 트의 구독으로 이동 하면 특정 서비스 주체에 대 한 액세스를 복원할 수 없게 됩니다. 주요 자격 증명 모음을 이동 하는 테 넌 트에 모든 필수 서비스 사용자가 존재 하는지 확인 합니다.

## <a name="design-considerations"></a>디자인 고려 사항

조직에서 구독 수준에서 적용 또는 제외를 사용 하 Azure Policy를 구현 했을 수 있습니다. 키 자격 증명 모음이 현재 존재 하는 구독에는 다른 정책 할당 집합이 있을 수 있으며, 주요 자격 증명 모음을 이동 하는 구독이 있을 수 있습니다. 정책 요구 사항의 충돌로 인해 응용 프로그램이 중단 될 수 있습니다.

### <a name="example"></a>예

2 년 동안 유효한 인증서를 생성 하는 key vault에 연결 된 응용 프로그램이 있습니다. 키 자격 증명 모음을 이동 하려는 구독에는 1 년 이상 유효한 인증서 생성을 차단 하는 정책 할당이 있습니다. 키 자격 증명 모음을 새 구독으로 이동한 후 2 년 동안 유효한 인증서를 만드는 작업은 Azure 정책 할당에 의해 차단 됩니다.

### <a name="solution"></a>솔루션

Azure Portal에서 Azure Policy 페이지로 이동 하 고 현재 구독에 대 한 정책 할당과 이동 중인 구독을 확인 하 고 불일치 항목이 없는지 확인 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 주요 자격 증명 모음이 있는 현재 구독에 대 한 참가자 수준 액세스 이상
* 주요 자격 증명 모음을 이동 하려는 구독에 대 한 참가자 수준 액세스 이상
* 새 구독의 리소스 그룹입니다.

## <a name="procedure"></a>절차

### <a name="initial-steps-moving-key-vault"></a>초기 단계 (이동 Key Vault)

1. Azure Portal에 로그인
2. 주요 자격 증명 모음으로 이동
3. "개요" 탭을 클릭 합니다.
4. "이동" 단추를 선택 합니다.
5. 드롭다운 옵션에서 "다른 구독으로 이동" 선택
6. 키 자격 증명 모음을 이동 하려는 리소스 그룹을 선택 합니다.
7. 키 자격 증명 모음을 이동 하려는 리소스 그룹을 선택 합니다.
8. 리소스 이동과 관련 된 경고 승인
9. "확인" 선택

### <a name="additional-steps-post-move"></a>추가 단계 (사후 이동)

키 자격 증명 모음을 새 구독으로 이동 했으므로 테 넌 트 ID를 업데이트 하 고 이전 액세스 정책을 제거 해야 합니다. PowerShell 및 Azure CLI의 이러한 단계에 대 한 자습서는 다음과 같습니다.

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

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

이제 자격 증명 모음이 올바른 테넌트 ID와 연결되고 이전 액세스 정책 항목이 제거되었으므로 Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) cmdlet 또는 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령을 사용하여 새 액세스 정책 항목을 설정합니다.

Azure 리소스에 관리 ID를 사용하는 경우 새 Azure AD 테넌트로도 업데이트해야 합니다. 관리 ID에 대한 자세한 내용은 [관리 ID를 사용하여 Key Vault 인증 제공](managed-identity.md)을 참조하세요.

MSI를 사용하는 경우 이전 ID가 더 이상 올바른 AAD 테넌트에 있지 않으므로 MSI ID도 업데이트해야 합니다.


