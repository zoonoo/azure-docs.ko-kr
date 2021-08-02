---
title: 자격 증명 모음을 다른 구독으로 이동하는 Azure Key Vault | Microsoft Docs
description: 키 자격 증명 모음을 다른 구독으로 이동 하는 방법에 대한 지침입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e1e4dd244045e86a0fb9e6d65f81a4149cf6659
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413324"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>다른 구독으로 Azure Key Vault 이동

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

> [!IMPORTANT]
> **키 자격 증명 모음을 다른 구독으로 이동하면 환경에 대한 주요 변경 내용이 발생합니다.**
> 키 자격 증명 모음을 새 구독으로 이동하도록 결정하기 전에 이러한 변경의 영향을 이해하고 이 문서의 지침을 따라야 합니다.
> MSI(관리 서비스 Id)를 사용하는 경우 문서 끝에 있는 이동 후 지침을 읽어보세요. 

[키 자격 증명 모음](overview.md)을 만들 때 생성된 해당 구독에 대한 기본 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) 테넌트 ID에 자동으로 연결됩니다. 이 [가이드](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)에 따라 구독과 연결된 테넌트 ID를 찾을 수 있습니다. 모든 액세스 정책 항목 및 역할 할당도 이 테넌트 ID에 연결됩니다.  Azure 구독을 테넌트 A에서 테넌트 B로 이동하는 경우 기존 키 자격 증명 모음에는 테넌트 B의 서비스 주체(사용자 및 애플리케이션)가 액세스할 수 없게 됩니다. 이 문제를 해결하려면 다음을 수행해야 합니다.

> [!NOTE]
> [Azure Lighthouse](../../lighthouse/overview.md)를 통해 Key Vault를 만든 경우에는 대신 테넌트 ID 관리에 연결됩니다. Azure Lighthouse는 자격 증명 모음 액세스 정책 권한 모델에서만 지원됩니다.
> Azure Lighthouse의 테넌트에 대한 자세한 내용은 [Azure Lighthouse의 테넌트, 사용자, 역할](../../lighthouse/concepts/tenants-users-roles.md)을 참조하세요.

* 이 구독에 있는 모든 기존 Key Vaults와 연결된 테넌트 ID를 테넌트 B로 변경합니다.
* 모든 기존 액세스 정책 항목을 제거합니다.
* 테넌트 B와 연결된 새 액세스 정책 항목을 추가합니다.

Azure Key Vault 및 Azure Active Directory에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Key Vault 정보](overview.md)
- [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)
- [테넌트 ID를 찾는 방법](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>제한 사항

> [!IMPORTANT]
> **디스크 암호화에 사용한 키 자격 증명 모음은 옮길 수 없습니다** VM용 디스크 암호화와 함께 키 자격 증명 모음을 사용한다면, 디스크 암호화를 사용 설정한 상태에서는 키 자격 증명 모음을 다른 리소스 그룹이나 구독으로 옮길 수 없습니다. 키 자격 증명 모음을 새 리소스 그룹이나 구독으로 옮기기 전에 먼저 디스크 암호화를 사용 중지해야 합니다. 

일부 서비스 주체(사용자 및 애플리케이션)는 특정 테넌트에 바인딩됩니다. 키 자격 증명 모음을 다른 테넌트의 구독으로 이동하면 특정 서비스 주체에 대한 액세스를 복원할 수 없게 됩니다. 키 자격 증명 모음을 이동하는 테넌트에 모든 필수 서비스 주체가 존재하는지 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 키 자격 증명 모음이 있는 현재 구독에 대한 [contributor](../../role-based-access-control/built-in-roles.md#contributor) 수준 액세스 이상. [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md), 또는 [PowerShell](../../role-based-access-control/role-assignments-powershell.md)을 사용하여 역할을 할당할 수 있습니다.
* 키 자격 증명 모음을 이동하려는 구독에 대한 [contributor](../../role-based-access-control/built-in-roles.md#contributor) 수준 액세스 이상. [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)또는 [PowerShell](../../role-based-access-control/role-assignments-powershell.md)을 사용하여 역할을 할당할 수 있습니다.
* 새 구독의 리소스 그룹. [Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md), [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md) 또는 [Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md)를 사용하여 한 그룹을 만들수 있습니다.

[Azure Portal](../../role-based-access-control/role-assignments-list-portal.md), [PowerShell](../../role-based-access-control/role-assignments-list-powershell.md), [Azure CLI](../../role-based-access-control/role-assignments-list-cli.md)또는 [REST API](../../role-based-access-control/role-assignments-list-rest.md)를 사용하여 기존 역할을 확인할 수 있습니다.


## <a name="moving-a-key-vault-to-a-new-subscription"></a>키 자격 증명 모음을 새 구독으로 이동합니다.

1. https://portal.azure.com 에서 Azure Portal에 로그인합니다.
2. [키 자격 증명 모음](overview.md)으로 이동
3. “개요” 탭을 클릭
4. “이동” 단추를 선택
5. 드롭다운 옵션에서 “다른 구독으로 이동” 선택
6. 키 자격 증명 모음을 이동할 리소스 그룹을 선택
7. 리소스 이동과 관련된 경고 승인
8. "확인" 선택

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>구독이 새 테넌트에 있는 경우 추가 단계

키 자격 증명 모음을 새 테넌트의 구독으로 이동한 경우 수동으로 테넌트 ID를 업데이트하고 이전 액세스 정책 및 역할 할당을 제거해야 합니다. PowerShell 및 Azure CLI의 이러한 단계에 대한 자습서는 다음과 같습니다. PowerShell을 사용하는 경우 아래에 설명된 Clear-AzContext 명령을 실행하여 현재 선택된 범위 외부의 리소스를 볼 수 있도록 해야 합니다. 

### <a name="update-tenant-id-in-a-key-vault"></a>주요 자격 증명 모음에서 테넌트  ID 업데이트

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
$tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>액세스 정책 및 역할 할당 업데이트

> [!NOTE]
> Key Vault이 [AZURE RBAC](../../role-based-access-control/overview.md) 권한 모델을 사용하는 경우. 키 자격 증명 모음 역할 할당도 제거해야 합니다. [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)또는 [PowerShell](../../role-based-access-control/role-assignments-powershell.md)을 사용하여 역할 할당을 제거할 수 있습니다. 

이제 자격 증명 모음이 올바른 테넌트 ID와 연결되고 오래된 액세스 정책 항목 또는 역할 할당이 삭제되므로 새 액세스 정책 항목 또는 역할 할당을 설정합니다.

할당 정책은 다음을 참고하여 주십시오.
- [포털을 사용하여 액세스 정책 할당](assign-access-policy-portal.md)
- [Azure CLI을 사용하여 액세스 정책 할당](assign-access-policy-cli.md)
- [PowerShell을 사용하여 액세스 정책 할당](assign-access-policy-powershell.md)

역할 할당을 추가하려면, 다음을 참조하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-cli.md)
- [PowerShell을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>관리 ID를 업데이트합니다.

전체 구독을 전송하고 Azure 리소스에 대한 관리 ID를 사용하는 경우 새 Azure Active Directory 테넌트로도 업데이트해야 합니다. 관리 ID에 대한 자세한 내용은 [관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 확인하세요.

관리 ID를 사용하는 경우 이전 ID가 더 이상 올바른 Azure Active Directory 테넌트에 있지 않으므로 ID도 업데이트해야 합니다. 이 문제를 해결하려면 다음 문서를 참조하세요. 

* [MSI 업데이트](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [새 디렉터리로 구독 전송](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>다음 단계

- [키, 비밀 및 인증서](about-keys-secrets-certificates.md)에 대한 자세한 정보
- Key Vault 로그 해석 방법을 비롯한 개념 정보는 [Key Vault 로깅](logging.md)을 참조하세요.
- [Key Vault 개발자 가이드](../general/developers-guide.md)
- [Azure Key Vault 보안 기능](security-features.md)
- [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)
