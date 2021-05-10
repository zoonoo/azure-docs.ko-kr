---
title: Azure Key Vault 액세스 정책 할당
description: Azure Portal, Azure CLI, Azure PowerShell을 사용하여 보안 주체 또는 애플리케이션 ID에 Key Vault 액세스 정책을 할당하는 방법.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751421"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Azure PowerShell을 사용하여 Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 사용자, 애플리케이션, 사용자 그룹 등의 특정 보안 주체가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml), [인증서](../certificates/index.yml)에 대해 서로 다른 작업을 수행할 수 있는지 여부를 결정합니다. [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md), Azure PowerShell(이 문서에서 설명)을 사용하여 액세스 정책을 할당할 수 있습니다.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure PowerShell을 사용하여 Azure Active Directory에서 그룹을 만드는 방법에 대한 자세한 내용은 [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) 및 [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)를 참조하세요.

## <a name="configure-powershell-and-sign-in"></a>PowerShell 및 로그인 구성

1. 명령을 로컬에서 실행하려면 [Azure PowerShell](/powershell/azure/)을 설치합니다(아직 설치하지 않은 경우).

    클라우드에서 직접 명령을 실행하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다.

1. 로컬 PowerShell만 해당:

    1. [Azure Active Directory PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD)을 설치합니다.

    1. Azure에 로그인:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>개체 ID 가져오기

액세스 정책을 할당하려는 애플리케이션, 그룹, 사용자의 개체 ID를 결정합니다.

- 애플리케이션 및 기타 서비스 주체: `-SearchString` 매개 변수가 있는 [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) cmdlet을 사용하여 원하는 서비스 주체 이름으로 결과를 필터링합니다.

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- 그룹: `-SearchString` 매개 변수가 있는 [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) cmdlet을 사용하여 원하는 그룹 이름으로 결과를 필터링합니다.

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    출력에서 개체 ID는 `Id`로 나열됩니다.

- 사용자: [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용하여 사용자의 메일 주소를 `-UserPrincipalName` 매개 변수에 전달합니다.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    출력에서 개체 ID는 `Id`로 나열됩니다.

## <a name="assign-the-access-policy"></a>액세스 정책 할당

[Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 사용하여 액세스 정책을 할당합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

이러한 특정 유형에 권한을 할당할 때는 `-PermissionsToSecrets`, `-PermissionsToKeys`, `-PermissionsToCertificates`만 포함해야 합니다. `<secret-permissions>`, `<key-permissions>`, `<certificate-permissions>`에 허용되는 값은 [Set-AzKeyVaultAccessPolicy - 매개 변수](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) 설명서에 제공됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](security-overview.md#identity-management)
- [키 자격 증명 모음 보안](security-overview.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)