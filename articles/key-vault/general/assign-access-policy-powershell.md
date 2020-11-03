---
title: Azure Key Vault 액세스 정책 할당
description: Azure Portal, Azure CLI 또는 Azure PowerShell를 사용 하 여 서비스 주체 또는 응용 프로그램 id에 Key Vault 액세스 정책을 할당 하는 방법입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: fb28b4b678b37f69331b2ecff6272fd7aa64d191
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287612"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Key Vault 액세스 정책 할당

Key Vault 액세스 정책은 지정 된 서비스 주체 (즉, 응용 프로그램 또는 사용자 그룹)가 Key Vault [비밀](../secrets/index.yml), [키](../keys/index.yml)및 [인증서](../certificates/index.yml)에 대해 다른 작업을 수행할 수 있는지 여부를 결정 합니다. [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md)또는 Azure PowerShell를 사용 하 여 액세스 정책을 할당할 수 있습니다 (이 문서).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Azure PowerShell를 사용 하 여 Azure Active Directory에서 그룹을 만드는 방법에 대 한 자세한 내용은 [AzureADGroup](/powershell/module/azuread/new-azureadgroup) 및 [AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)를 참조 하세요.

## <a name="configure-powershell-and-sign-in"></a>PowerShell 및 로그인 구성

1. 명령을 로컬로 실행 하려면 [Azure PowerShell](/powershell/azure/) 를 설치 합니다 (아직 설치 하지 않은 경우).

    클라우드에서 직접 명령을 실행 하려면 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용 합니다.

1. 로컬 PowerShell에만 해당:

    1. [Azure Active Directory PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD)을 설치합니다.

    1. Azure에 로그인:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>개체 ID 가져오기

액세스 정책을 할당 하려는 응용 프로그램, 그룹 또는 사용자의 개체 ID를 확인 합니다.

- 응용 프로그램 및 기타 서비스 사용자: [AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) cmdlet을 매개 변수와 함께 사용 `-SearchString` 하 여 원하는 서비스 주체의 이름으로 결과를 필터링 할 수 있습니다.

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- 그룹: [AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) cmdlet을 `-SearchString` 매개 변수와 함께 사용 하 여 원하는 그룹의 이름으로 결과를 필터링 합니다.

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    출력에서 개체 ID는로 나열 됩니다 `Id` .

- 사용자: [AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용 하 여 사용자의 전자 메일 주소를 `-UserPrincipalName` 매개 변수로 전달 합니다.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    출력에서 개체 ID는로 나열 됩니다 `Id` .

## <a name="assign-the-access-policy"></a>액세스 정책 할당

[AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet을 사용 하 여 액세스 정책을 할당 합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

`-PermissionsToSecrets` `-PermissionsToKeys` 특정 형식에 대 한 사용 권한을 할당 하는 경우, 및만 포함 해야 `-PermissionsToCertificates` 합니다. , 및에 허용 되는 값은 `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` [AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) 설명서에 제공 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안: ID 및 액세스 관리](overview-security.md#identity-and-access-management)
- [키 자격 증명 모음을 보호](secure-your-key-vault.md)합니다.
- [Azure Key Vault 개발자 가이드](developers-guide.md)
- [Azure Key Vault 모범 사례](best-practices.md)