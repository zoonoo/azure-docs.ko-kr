---
title: Azure Key Vault 복구 개요 | Microsoft Docs
description: Key Vault 복구 기능은 키 자격 증명 모음과 키 자격 증명 모음에 저장된 비밀, 키, 인증서를 실수나 고의로 삭제하지 못하도록 하기 위해 설계되었습니다.
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.author: mbaldwin
author: msmbaldwin
ms.date: 09/30/2020
ms.openlocfilehash: 45f56812f0d6645c9ac75ce62f9f0659acc358fe
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970073"
---
# <a name="azure-key-vault-recovery-management-with-soft-delete-and-purge-protection"></a>일시 삭제 및 제거 방지를 통한 Azure Key Vault 복구 관리

이 문서에서는 Azure Key Vault의 두 가지 복구 기능인 일시 삭제와 제거 방지에 관해 설명합니다. 이 문서에서는 이러한 기능의 개요를 제공하고 Azure Portal, Azure CLI, Azure PowerShell을 통해 해당 기능을 관리하는 방법을 설명합니다.

Key Vault에 대한 자세한 내용은 다음을 참조하세요.
- [Key Vault 개요](overview.md)
- [Azure Key Vault 키, 비밀 및 인증서 개요](about-keys-secrets-certificates.md)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/dotnet)
* [PowerShell 모듈](/powershell/azure/install-az-ps)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault - [Azure Portal](../general/quick-create-portal.md), [Azure CLI](../general/quick-create-cli.md) 또는 [Azure PowerShell](../general/quick-create-powershell.md)을 사용하여 만들 수 있습니다.
* 일시 삭제된 자격 증명 모음에 대한 작업을 수행하려면 사용자에게 구독 수준의 다음 사용 권한이 필요합니다.

  | 사용 권한 | Description |
  |---|---|
  |Microsoft.KeyVault/locations/deletedVaults/read|일시 삭제된 한 Key Vault의 속성을 봅니다.|
  |Microsoft.KeyVault/locations/deletedVaults/purge/action|일시 삭제된 Key Vault를 제거합니다.|
  |Microsoft.KeyVault/locations/operationResults/read| 자격 증명 모음의 제거 상태를 확인하려면|
  |[키 자격 증명 모음 기여자](../../role-based-access-control/built-in-roles.md#key-vault-contributor)|일시 삭제된 자격 증명 모음을 복구하려면|


## <a name="what-are-soft-delete-and-purge-protection"></a>일시 삭제와 제거 방지 소개

[일시 삭제](soft-delete-overview.md)와 제거 방지는 두 가지 키 자격 증명 모음 복구 기능입니다.

> [!IMPORTANT]
> 키 자격 증명 모음과 인증서를 실수로 삭제하지 못하게 하려면 일시 삭제를 설정하는 것이 중요합니다. 그러나 일시 삭제를 설정하는 것은 애플리케이션 로직을 변경하거나 서비스 주체에 추가 권한을 부여해야 할 수 있으므로 호환성이 손상되는 변경으로 간주됩니다. 아래 지침에 따라 일시 삭제를 설정하기 전에 [**여기** 에서 이 문서를 참조하여 애플리케이션이 변경 내용과 호환되는지 확인하세요.](soft-delete-change.md)

**일시 삭제** 는 키 자격 증명 모음과 키 자격 증명 모음 내에 저장된 키, 비밀, 인증서를 실수로 삭제하지 못하도록 하기 위해 설계되었습니다. 일시 삭제는 휴지통과 유사합니다. 키 자격 증명 모음이나 키 자격 증명 모음 개체를 삭제하면 해당 항목은 사용자가 구성 가능한 보존 기간이나 기본값인 90일 동안 복구 가능한 상태로 유지됩니다. 일시 삭제된 상태의 키 자격 증명 모음은 **제거** 하여 영구적으로 삭제할 수도 있습니다. 제거하면 키 자격 증명 모음과 키 자격 증명 모음 개체를 같은 이름으로 다시 만들 수 있습니다. 키 자격 증명 모음 및 개체를 복구하고 삭제하려면 상승된 액세스 정책 권한이 필요합니다. **일시 삭제를 사용하도록 설정한 후에는 사용하지 않도록 설정할 수 없습니다.**

**키 자격 증명 모음 이름은 전역적으로 고유** 하므로 일시 삭제된 상태의 키 자격 증명 모음과 동일한 이름으로 키 자격 증명 모음을 만들 수 없습니다. 마찬가지로 키, 비밀, 인증서의 이름은 키 자격 증명 모음 내에서 고유합니다. 따라서 일시 삭제된 상태의 다른 항목과 동일한 이름으로 비밀, 키, 인증서를 만들 수 없습니다.

**제거 방지** 는 악의적인 내부자가 키 자격 증명 모음, 키, 비밀, 인증서를 삭제하지 못하도록 하기 위해 설계되었습니다. 시간 기준 잠금이 설정된 휴지통으로 볼 수 있습니다. 구성 가능한 보존 기간 동안 언제든지 항목을 복구할 수 있습니다. **보존 기간이 경과할 때까지 키 자격 증명 모음을 영구적으로 삭제하거나 제거할 수는 없습니다.** 보존 기간이 경과하면 키 자격 증명 모음 또는 키 자격 증명 모음 개체가 자동으로 제거됩니다.

> [!NOTE]
> 제거 방지는 관리자 역할이나 사용 권한이 있는 사용자가 제거 방지를 재정의하거나 사용하지 않도록 설정하거나 우회하지 못하도록 하기 위해 설계되었습니다. **제거 방지를 사용하도록 설정한 후에는 Microsoft를 포함한 누구도 사용하지 않도록 설정하거나 재정의할 수 없습니다.** 즉, 삭제된 키 자격 증명 모음을 복구하거나 보존 기간이 경과될 때까지 기다린 이후에야 키 자격 증명 모음 이름을 다시 사용할 수 있습니다.

일시 삭제에 대한 자세한 내용은 [Azure Key Vault soft-delete overview](soft-delete-overview.md)(Azure Key Vault 일시 삭제 개요)를 참조하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>키 자격 증명 모음에서 일시 삭제가 사용하도록 설정되어 있는지 확인하고 일시 삭제를 사용하도록 설정

1. Azure 포털에 로그인합니다.
1. 키 자격 증명 모음을 선택합니다.
1. “속성” 블레이드를 클릭합니다.
1. 일시 삭제 옆의 라디오 단추가 “복구 사용”으로 설정되어 있는지 확인합니다.
1. 키 자격 증명 모음에서 일시 삭제를 사용하도록 설정하지 않은 경우 라디오 단추를 클릭하여 일시 삭제를 사용하도록 설정하고 “저장”을 클릭합니다.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text="속성에서 일시 삭제가 강조 표시되어 있음(사용하도록 설정된 것을 나타냄)":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>서비스 주체에 삭제된 비밀을 제거하고 복구하는 액세스 권한 부여

1. Azure 포털에 로그인합니다.
1. 키 자격 증명 모음을 선택합니다.
1. “액세스 정책” 블레이드를 클릭합니다.
1. 테이블에서 액세스 권한을 부여하려는 보안 주체의 행을 찾거나 새 보안 주체를 추가합니다.
1. 키, 인증서, 비밀의 드롭다운을 클릭합니다.
1. 드롭다운의 아래로 스크롤하고 “복구” 및 “제거”를 클릭합니다.
1. 보안 주체가 대부분의 작업을 수행하기 위해서는 가져오기 및 나열 기능도 필요합니다.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text="왼쪽 탐색 창에 액세스 정책이 강조 표시되어 있습니다. 액세스 정책에서 비밀 위치 드롭다운 목록이 표시되고 가져오기, 나열, 복구, 제거의 네 가지 항목이 선택되어 있습니다.":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>일시 삭제된 키 자격 증명 모음 나열, 복구, 제거

1. Azure 포털에 로그인합니다.
1. 페이지 맨 위에 있는 검색 창을 클릭합니다.
1. “최근 서비스”에서 “Key Vault”를 클릭합니다. 개별 키 자격 증명 모음을 클릭하지 마세요.
1. 화면 위쪽에서 “삭제된 자격 증명 모음 관리” 옵션을 클릭합니다.
1. 그러면 화면 오른쪽에 컨텍스트 창이 열립니다.
1. 구독을 선택합니다.
1. 키 자격 증명 모음이 일시 삭제된 경우 오른쪽의 컨텍스트 창에 표시됩니다.
1. 자격 증명 모음이 너무 많은 경우에는 컨텍스트 창의 맨 아래에서 “추가 로드”를 클릭하거나 CLI 또는 PowerShell을 사용하여 결과를 가져올 수 있습니다.
1. 복구하거나 제거하려는 자격 증명 모음을 찾으면 옆의 확인란을 선택합니다.
1. 주요 자격 증명 모음을 복구하려는 경우 컨텍스트 창 맨 아래에 있는 복구 옵션을 선택합니다.
1. 키 자격 증명 모음을 영구적으로 삭제하려는 경우 제거 옵션을 선택합니다.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text="키 자격 증명 모음에서 삭제된 자격 증명 모음 관리 옵션이 강조 표시되어 있습니다.":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text="삭제된 키 자격 증명 모음 관리에서 나열된 키 자격 증명 모음만 강조 표시 및 선택되어 있고 복구 단추가 강조 표시되어 있습니다.":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>일시 삭제된 비밀, 키, 인증서 나열, 복구, 제거

1. Azure 포털에 로그인합니다.
1. 키 자격 증명 모음을 선택합니다.
1. 관리하려는 비밀 유형(키, 비밀, 인증서)에 해당하는 블레이드를 선택합니다.
1. 화면 위쪽에서 “삭제된 키, 비밀, 인증서 관리”를 클릭합니다.
1. 화면 오른쪽에 컨텍스트 창이 표시됩니다.
1. 비밀, 키, 인증서가 목록에 표시되지 않는 경우 일시 삭제된 상태가 아닙니다.
1. 관리하려는 비밀, 키, 인증서를 선택합니다.
1. 컨텍스트 창의 맨 아래에서 복구 또는 제거 옵션을 선택합니다.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text="키에서 삭제된 키 관리 옵션이 강조 표시되어 있습니다.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>키 자격 증명 모음(CLI)

* 키 자격 증명 모음에서 일시 삭제가 사용하도록 설정되어 있는지 확인

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 키 자격 증명 모음에서 일시 삭제를 사용하도록 설정

    모든 새 키 자격 증명 모음에서 일시 삭제가 기본적으로 사용하도록 설정됩니다. 현재 일시 삭제가 사용하도록 설정되지 않는 키 자격 증명 모음을 사용하는 경우 다음 명령을 사용하여 일시 삭제를 사용하도록 설정합니다.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* 키 자격 증명 모음 삭제(일시 삭제를 사용하도록 설정한 경우 복구 가능)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 일시 삭제된 키 자격 증명 모음 모두 나열

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* 일시 삭제된 키 자격 증명 모음 복구

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 일시 삭제된 키 자격 증명 모음 제거 **(경고! 이 작업을 수행하면 키 자격 증명 모음이 영구적으로 삭제됩니다.)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 키 자격 증명 모음에서 제거 방지를 사용하도록 설정

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>인증서(CLI)

* 인증서를 제거하고 복구할 수 있는 액세스 권한 부여

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* 인증서 삭제

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 삭제된 인증서 나열

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 삭제된 인증서 복구

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 일시 삭제된 인증서 제거 **(경고! 이 작업을 수행하면 인증서가 영구적으로 삭제됩니다.)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>키(CLI)

* 키를 제거하고 복구할 수 있는 액세스 권한 부여

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --key-permissions recover purge
    ```

* Delete 키

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 삭제된 키 나열

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 삭제된 키 복구

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 일시 삭제된 키 제거 **(경고! 이 작업을 수행하면 키가 영구적으로 삭제됩니다.)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>비밀(CLI)

* 비밀을 제거하고 복구할 수 있는 액세스 권한 부여

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* 비밀 삭제

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 삭제된 비밀 나열

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 삭제된 비밀 복구

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 일시 삭제된 비밀 제거 **(경고! 이 작업을 수행하면 비밀이 영구적으로 삭제됩니다.)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>키 자격 증명 모음(PowerShell)

* 키 자격 증명 모음에서 일시 삭제가 사용하도록 설정되어 있는지 확인

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* 키 자격 증명 모음 삭제

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* 일시 삭제된 키 자격 증명 모음 모두 나열

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* 일시 삭제된 키 자격 증명 모음 복구

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* 일시 삭제된 키 자격 증명 모음 제거 **(경고! 이 작업을 수행하면 키 자격 증명 모음이 영구적으로 삭제됩니다.)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* 키 자격 증명 모음에서 제거 방지를 사용하도록 설정

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>인증서(PowerShell)

* 인증서를 복구하고 제거할 수 있는 권한 부여

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* 인증서 삭제

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* 키 자격 증명 모음의 삭제된 인증서 모두 표시

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* 삭제된 상태의 인증서 복구

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* 일시 삭제된 인증서 제거 **(경고! 이 작업을 수행하면 인증서가 영구적으로 삭제됩니다.)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>키(PowerShell)

* 키를 복구하고 제거할 수 있는 권한 부여

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* 키 삭제

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* 키 자격 증명 모음의 삭제된 인증서 모두 표시

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* 일시 삭제된 키 복구

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* 일시 삭제된 키 제거 **(경고! 이 작업을 수행하면 키가 영구적으로 삭제됩니다.)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>비밀(PowerShell)

* 비밀을 복구하고 제거할 수 있는 권한 부여

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* SQLPassword라는 비밀 삭제

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* 키 자격 증명 모음의 삭제된 비밀 모두 표시

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* 삭제된 상태의 비밀 복구

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* 삭제된 상태의 비밀 제거 **(경고! 이 작업을 수행하면 키가 영구적으로 삭제됩니다.)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
---

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault PowerShell cmdlets](/powershell/module/az.keyvault)
- [Key Vault Azure CLI commands](/cli/azure/keyvault)(Key Vault Azure CLI 명령)
- [Azure Key Vault 백업](backup.md)
- [키 자격 증명 모음 로깅을 사용하는 방법](howto-logging.md)
- [Azure Key Vault 보안 기능](security-features.md)
- [Azure Key Vault 개발자 가이드](developers-guide.md)