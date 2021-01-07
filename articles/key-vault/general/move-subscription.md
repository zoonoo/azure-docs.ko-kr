---
title: 자격 증명 모음을 다른 구독으로 이동하는 Azure Key Vault | Microsoft Docs
description: 키 자격 증명 모음을 다른 구독으로 이동 하는 방법에 대한 지침입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: d881394391b7967fe602155eefc9844e013de34e
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724751"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>다른 구독으로 Azure Key Vault 이동

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

> [!IMPORTANT]
> **키 자격 증명 모음을 다른 구독으로 이동하면 환경에 대한 주요 변경 내용이 발생합니다.**
> 키 자격 증명 모음을 새 구독으로 이동하도록 결정하기 전에 이러한 변경의 영향을 이해하고 이 문서의 지침을 따라야 합니다.
> MSI (관리 서비스 Id)를 사용 하는 경우 문서 끝에 있는 이동 후 지침을 읽어 보세요. 

[Azure Key Vault](overview.md) 는 생성 된 구독의 기본 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 테 넌 트 ID에 자동으로 연결 됩니다. 이 [가이드](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)에 따라 구독과 연결 된 테 넌 트 ID를 찾을 수 있습니다. 모든 액세스 정책 항목 및 역할 할당도이 테 넌 트 ID에 연결 됩니다.  Azure 구독을 테넌트 A에서 테넌트 B로 이동하는 경우 기존 키 자격 증명 모음에는 테넌트 B의 서비스 주체(사용자 및 애플리케이션)가 액세스할 수 없게 됩니다. 이 문제를 해결하려면 다음을 수행해야 합니다.

* 이 구독에 있는 모든 기존 Key Vaults와 연결된 테넌트 ID를 테넌트 B로 변경합니다.
* 모든 기존 액세스 정책 항목을 제거합니다.
* 테넌트 B와 연결된 새 액세스 정책 항목을 추가합니다.

Azure Key Vault 및 Azure Active Directory에 대 한 자세한 내용은을 참조 하세요.
- [Azure Key Vault 정보](overview.md)
- [Azure Active Directory 정의](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [테넌트 ID를 찾는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-to-find-tenant)

## <a name="limitations"></a>제한 사항

> [!IMPORTANT]
> **디스크 암호화에 사용 되는 키 자격 증명 모음은 이동할 수 없습니다** . VM에 대 한 디스크 암호화를 사용 하는 key vault를 사용 하는 경우 디스크 암호화를 사용 하는 동안에는 key vault를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다. 키 자격 증명 모음을 새 리소스 그룹 또는 구독으로 이동 하기 전에 디스크 암호화를 사용 하지 않도록 설정 해야 합니다. 

일부 서비스 주체(사용자 및 애플리케이션)는 특정 테넌트에 바인딩됩니다. 키 자격 증명 모음을 다른 테넌트의 구독으로 이동하면 특정 서비스 주체에 대한 액세스를 복원할 수 없게 됩니다. 키 자격 증명 모음을 이동하는 테넌트에 모든 필수 서비스 주체가 존재하는지 확인합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 주요 자격 증명 모음이 있는 현재 구독에 대 한 [참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 수준 액세스 이상 [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)또는 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)을 사용 하 여 역할을 할당할 수 있습니다.
* 주요 자격 증명 모음을 이동 하려는 구독에 대 한 [참가자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 수준 액세스 이상 [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)또는 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)을 사용 하 여 역할을 할당할 수 있습니다.
* 새 구독의 리소스 그룹. [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)또는 [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli)를 사용 하 여 만들 수 있습니다.

[Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal), [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-cli)또는 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-rest)를 사용 하 여 기존 역할을 확인할 수 있습니다.


## <a name="moving-a-key-vault-to-a-new-subscription"></a>새 구독으로 키 자격 증명 모음 이동

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
2. [주요 자격 증명 모음](overview.md) 으로 이동
3. “개요” 탭을 클릭
4. “이동” 단추를 선택
5. 드롭다운 옵션에서 “다른 구독으로 이동” 선택
6. 키 자격 증명 모음을 이동할 리소스 그룹을 선택
7. 리소스 이동과 관련된 경고 승인
8. "확인" 선택

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>구독이 새 테 넌 트에 있는 경우 추가 단계

키 자격 증명 모음을 새 테 넌 트의 구독으로 이동한 경우 수동으로 테 넌 트 ID를 업데이트 하 고 이전 액세스 정책 및 역할 할당을 제거 해야 합니다. PowerShell 및 Azure CLI의 이러한 단계에 대한 자습서는 다음과 같습니다. PowerShell을 사용 하는 경우 아래에 설명 된 Clear-AzContext 명령을 실행 하 여 현재 선택 된 범위 외부의 리소스를 볼 수 있도록 해야 합니다. 

### <a name="update-tenant-id-in-a-key-vault"></a>주요 자격 증명 모음에서 테 넌 트 ID 업데이트

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
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>액세스 정책 및 역할 할당 업데이트

> [!NOTE]
> Key Vault [AZURE RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) 권한 모델을 사용 합니다. Key vault 역할 할당도 제거 해야 합니다. [Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)또는 [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)을 사용 하 여 역할 할당을 제거할 수 있습니다. 

이제 자격 증명 모음이 올바른 테 넌 트 ID와 연결 되어 있고 이전 액세스 정책 항목 또는 역할 할당이 제거 되었으므로 새 액세스 정책 항목 또는 역할 할당을 설정 합니다.

정책 할당에 대 한 자세한 내용은 다음을 참조 하세요.
- [포털을 사용 하 여 액세스 정책 할당](assign-access-policy-portal.md)
- [Azure CLI를 사용 하 여 액세스 정책 할당](assign-access-policy-cli.md)
- [PowerShell을 사용 하 여 액세스 정책 할당](assign-access-policy-powershell.md)

역할 할당을 추가 하려면 다음을 참조 하세요.
- [포털을 사용 하 여 역할 할당 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
- [Azure CLI를 사용 하 여 역할 할당 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)
- [PowerShell을 사용 하 여 역할 할당 추가](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)


### <a name="update-managed-identities"></a>관리 id 업데이트

전체 구독을 전송 하 고 Azure 리소스에 대 한 관리 되는 id를 사용 하는 경우 새 Azure Active Directory 테 넌 트로도 업데이트 해야 합니다. 관리 id에 대 한 자세한 내용은 [관리 id 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 확인 하세요.

관리 id를 사용 하는 경우 이전 id가 더 이상 올바른 Azure Active Directory 테 넌 트에 없으므로 id도 업데이트 해야 합니다. 이 문제를 해결 하려면 다음 문서를 참조 하세요. 

* [MSI 업데이트](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [새 디렉터리로 구독 전송](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>다음 단계

- [키, 비밀 및 인증서](about-keys-secrets-certificates.md)에 대한 자세한 정보
- Key Vault 로그를 해석 하는 방법을 비롯 한 개념 정보는 [Key Vault 로깅](logging.md) 을 참조 하세요.
- [Key Vault 개발자 가이드](../general/developers-guide.md)
- [Key Vault 보안 설정](secure-your-key-vault.md)
- [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)
