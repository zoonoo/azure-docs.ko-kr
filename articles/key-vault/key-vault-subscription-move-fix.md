---
title: 구독 이동 후에 키 자격 증명 모음 테넌트 ID 변경 - Azure Key Vault | Microsoft Docs
description: 다른 테넌트에 구독을 이동한 후에 주요 자격 증명 모음에 대한 테넌트 ID를 전환하는 방법을 알아봅니다.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: a83bff5a494ce338f43b6e967df5fe67cacfab01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112192"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>구독 이동 후에 주요 자격 증명 모음 테넌트 ID 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Q: 내 구독이 테넌트 A에서 테넌트 B로 이동했습니다. 내 기존 주요 자격 증명 모음에 대한 테넌트 ID를 변경하고 테넌트 B에서 주체에 대한 올바른 ACL을 설정하려면 어떻게 해야 하나요?

구독에 새 주요 자격 증명 모음을 만들 때 해당 구독에 대한 기본 Azure Active Directory 테넌트 ID에 자동으로 연결됩니다. 모든 액세스 정책 항목은 이 테넌트 ID에 연결됩니다. Azure 구독을 테넌트 A에서 테넌트 B로 이동할 때 기존 주요 자격 증명 모음에는 테넌트 B의 주체(사용자 및 애플리케이션)가 액세스할 수 없게 됩니다. 이 문제를 해결하려면 다음을 수행합니다.

* 이 구독에 있는 모든 기존 주요 자격 증명 모음과 연결된 테넌트 ID를 테넌트 B로 변경합니다.
* 모든 기존 액세스 정책 항목을 제거합니다.
* 테넌트 B와 연결된 새 액세스 정책 항목을 추가합니다.

예를 들어 이 구독에서 주요 자격 증명 모음 'myvault'가 테넌트 A에서 테넌트 B로 이동된 경우 이 주요 자격 증명 모음에 대한 테넌트 ID를 변경하고 오래된 액세스 정책을 제거하는 방법은 다음과 같습니다.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

이동 전에 이 자격 증명 모음이 테넌트 A였기 때문에 **(Get-AzContext).Tenant.TenantId**가 테넌트 B인 반면 **$vault.Properties.TenantId**의 원래 값이 테넌트 A입니다.

이제 자격 증명 모음이 올바른 테넌트 ID와 연결되고 오래된 액세스 정책 항목이 삭제되므로 새 액세스 정책 항목을 [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy)로 설정합니다.

## <a name="next-steps"></a>다음 단계

Azure Key Vault에 대한 질문이 있으면 [Azure Key Vault 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)으로 이동하세요.
