---
title: Azure Key Vault 복구 개요 | Microsoft Docs
description: Key Vault 복구 기능은 키 자격 증명 모음에 저장 된 키 자격 증명 모음 및 암호, 키 및 인증서를 실수로 또는 악의적으로 삭제할 수 없도록 설계 되었습니다.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: ShaneBala-keyvault
ms.author: sudbalas
manager: ravijan
ms.date: 09/30/2020
ms.openlocfilehash: bea4a5d92309710645dd63e611cd0a5e3b742c34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604350"
---
# <a name="azure-key-vault-recovery-overview"></a>Azure Key Vault 복구 개요

이 문서에서는 Azure Key Vault, 일시 삭제 및 보호 제거의 두 가지 복구 기능에 대해 설명 합니다. 이 문서에서는 이러한 기능에 대해 간략하게 설명 하 고 Azure Portal, Azure CLI 및 Azure PowerShell를 통해 이러한 기능을 관리 하는 방법을 보여 줍니다.

## <a name="what-are-soft-delete-and-purge-protection"></a>일시 삭제 및 보호 제거 란?

일시 삭제 및 보호 제거는 두 가지 주요 자격 증명 모음 복구 기능입니다.
> [!IMPORTANT]
> 모든 주요 자격 증명 모음에서 일시 삭제 보호를 사용 하도록 설정 해야 합니다. 일시 삭제 보호를 사용 하지 않도록 설정 하는 기능은 12 월 2020 일까 지 사용 되지 않습니다. 자세한 내용은 [ **여기**를 참조 하세요.](soft-delete-change.md)

**일시 삭제** 는 key vault 내에서 저장 된 키 자격 증명 모음 및 키, 암호 및 인증서를 실수로 삭제 하지 않도록 설계 되었습니다. 휴지통 같은 일시 삭제를 생각해 봅니다. 키 자격 증명 모음 또는 주요 자격 증명 모음 개체를 삭제 하는 경우 사용자 구성 가능한 보존 기간 또는 기본값 90 일에 대해 복구 가능한 상태로 유지 됩니다. 일시 삭제 된 상태의 키 자격 증명 모음은 영구적으로 삭제 되는 것을 의미 하는 **지울** 수도 있습니다. 이를 통해 키 자격 증명 모음 및 주요 자격 증명 모음 개체를 같은 이름으로 다시 만들 수 있습니다. 키 자격 증명 모음 및 개체를 복구 하 고 삭제 하려면 상승 된 액세스 정책 권한이 필요 합니다. **일시 삭제를 사용 하도록 설정한 후에는 사용 하지 않도록 설정할 수 없습니다.**

**주요 자격 증명 모음 이름은 전역적으로 고유**하므로 일시 삭제 된 상태에서 키 자격 증명 모음과 동일한 이름으로 key vault를 만들 수 없습니다. 마찬가지로 키, 암호 및 인증서의 이름은 키 자격 증명 모음 내에서 고유 합니다. 일시 삭제 된 상태에서 다른 이름과 동일한 이름으로 비밀, 키 또는 인증서를 만들 수 없습니다.

**보호 제거** 는 악의적인 참가자가 키 자격 증명 모음, 키, 암호 및 인증서를 삭제할 수 없도록 설계 되었습니다. 이를 시간 기반 잠금을 사용 하는 휴지통으로 간주 합니다. 구성 가능한 보존 기간 동안 언제 든 지 항목을 복구할 수 있습니다. **보존 기간이 경과할 때까지 키 자격 증명 모음을 영구적으로 삭제 하거나 제거할 수 없습니다.** 보존 기간이 경과 하면 주요 자격 증명 모음 또는 주요 자격 증명 모음 개체가 자동으로 제거 됩니다.

> [!NOTE]
> 제거 방지는 관리자 역할이 나 사용 권한이 제거 방지를 재정의, 사용 안 함 또는 우회 할 수 없도록 설계 되었습니다. **제거 보호를 사용 하도록 설정한 후에는 Microsoft를 포함 한 모든 사용자가 사용 하지 않도록 설정 하거나 재정의할 수 없습니다.** 즉, 키 자격 증명 모음 이름을 다시 사용 하기 전에 삭제 된 key vault를 복구 하거나 보존 기간이 경과할 때까지 기다려야 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete"></a>일시 삭제가 키 자격 증명 모음에 사용 하도록 설정 되어 있는지 확인 하 고 일시 삭제를 사용 하도록 설정 합니다.

1. Azure 포털에 로그인합니다.
1. 키 자격 증명 모음을 선택합니다.
1. "속성" 블레이드를 클릭 합니다.
1. 일시 삭제 옆의 라디오 단추가 "복구 사용"으로 설정 되어 있는지 확인 합니다.
1. 키 자격 증명 모음에서 일시 삭제를 사용 하도록 설정 하지 않은 경우 라디오 단추를 클릭 하 여 일시 삭제를 사용 하도록 설정 하 고 "저장"을 클릭 합니다.

:::image type="content" source="../media/key-vault-recovery-1.png" alt-text=" Azure Portal의 스크린샷<>":::

## <a name="grant-access-to-a-service-principal-to-purge-and-recover-deleted-secrets"></a>서비스 주체에 게 삭제 된 비밀을 제거 하 고 복구 하는 액세스 권한 부여

1. Azure 포털에 로그인합니다.
1. 키 자격 증명 모음을 선택합니다.
1. "액세스 정책" 블레이드를 클릭 합니다.
1. 테이블에서 액세스 권한을 부여 하려는 보안 주체의 행을 찾거나 새 보안 주체를 추가 합니다.
1. 키, 인증서 및 비밀에 대 한 드롭다운을 클릭 합니다.
1. 드롭다운 아래로 스크롤하고 "복구" 및 "제거"를 클릭 합니다.
1. 또한 보안 주체에는 대부분의 작업을 수행 하는 get 및 list 기능이 필요 합니다.

:::image type="content" source="../media/key-vault-recovery-2.png" alt-text=" Azure Portal의 스크린샷<>":::

## <a name="list-recover-or-purge-a-soft-deleted-key-vault"></a>일시 삭제 된 키 자격 증명 모음 나열, 복구 또는 제거

1. Azure 포털에 로그인합니다.
1. 페이지 맨 위에 있는 검색 표시줄을 클릭 합니다.
1. "최근 서비스"에서 "Key Vault"을 클릭 합니다. 개별 키 자격 증명 모음을 클릭 하지 마십시오.
1. 화면 위쪽에서 "삭제 된 자격 증명 모음 관리" 옵션을 클릭 합니다.
1. 컨텍스트 창이 화면 오른쪽에 열립니다.
1. 구독을 선택합니다.
1. 키 자격 증명 모음이 일시 삭제 된 경우 오른쪽의 컨텍스트 창에 표시 됩니다.
1. 자격 증명 모음이 너무 많은 경우에는 컨텍스트 창의 맨 아래에서 "더 로드"를 클릭 하거나 CLI 또는 PowerShell을 사용 하 여 결과를 가져올 수 있습니다.
1. 복구 하거나 제거 하려는 자격 증명 모음을 찾았으면 옆의 확인란을 선택 합니다.
1. 주요 자격 증명 모음을 복구 하려는 경우 상황에 맞는 창 맨 아래에 있는 복구 옵션을 선택 합니다.
1. 키 자격 증명 모음을 영구적으로 삭제 하려면 제거 옵션을 선택 합니다.

:::image type="content" source="../media/key-vault-recovery-3.png" alt-text=" Azure Portal의 스크린샷<>":::

:::image type="content" source="../media/key-vault-recovery-4.png" alt-text=" Azure Portal의 스크린샷<>":::

## <a name="list-recover-or-purge-soft-deleted-secrets-keys-and-certificates"></a>일시 삭제 된 비밀, 키 및 인증서 나열, 복구 또는 제거

1. Azure 포털에 로그인합니다.
1. 키 자격 증명 모음을 선택합니다.
1. 관리 하려는 비밀 유형 (키, 비밀 또는 인증서)에 해당 하는 블레이드를 선택 합니다.
1. 화면 위쪽에서 "삭제 된 키, 비밀 또는 인증서"를 클릭 합니다.
1. 컨텍스트 창은 화면 오른쪽에 표시 됩니다.
1. 비밀, 키 또는 인증서가 목록에 표시 되지 않는 경우 일시 삭제 된 상태가 아닙니다.
1. 관리 하려는 비밀, 키 또는 인증서를 선택 합니다.
1. 컨텍스트 창의 맨 아래에서 복구 또는 제거 옵션을 선택 합니다.

:::image type="content" source="../media/key-vault-recovery-5.png" alt-text=" Azure Portal의 스크린샷<>":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="key-vault-cli"></a>Key Vault (CLI)

* 키 자격 증명 모음에서 일시 삭제를 사용 하도록 설정 했는지 확인

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 키 자격 증명 모음에 대해 일시 삭제를 사용 하도록 설정

    모든 새 키 자격 증명 모음은 일시 삭제를 기본적으로 사용 하도록 설정 합니다. 일시 삭제를 사용 하도록 설정 하지 않은 키 자격 증명 모음이 현재 있는 경우 다음 명령을 사용 하 여 일시 삭제를 사용 하도록 설정 합니다.

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-soft-delete true
    ```

* 키 자격 증명 모음 삭제 (일시 삭제를 사용 하는 경우 복구 가능)

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}
    ```

* 일시 삭제 된 키 자격 증명 모음 모두 나열

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type vault
    ```

* 일시 삭제 된 키 복구-자격 증명 모음

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 일시 삭제 된 key vault를 제거 합니다 **(경고! 이 작업은 키 자격 증명 모음을 영구적으로 삭제 합니다.)**

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} -n {VAULT NAME}
    ```

* 키 자격 증명 모음에서 제거 방지 사용

    ```azurecli
    az keyvault update --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME} --enable-purge-protection true
    ```

## <a name="certificates-cli"></a>인증서 (CLI)

* 인증서를 제거 하 고 복구할 수 있는 액세스 권한 부여

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --certificate-permissions recover purge
    ```

* 인증서 삭제

    ```azurecli
    az keyvault certificate delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 삭제 된 인증서 나열

    ```azurecli
    az keyvault certificate list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 삭제 된 인증서 복구

    ```azurecli
    az keyvault certificate recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

* 일시 삭제 된 인증서를 제거 합니다 **(경고! 이 작업을 수행 하면 인증서가 영구적으로 삭제 됩니다.)**

    ```azurecli
    az keyvault certificate purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {CERTIFICATE NAME}
    ```

## <a name="keys-cli"></a>키 (CLI)

* 키 제거 및 복구에 대 한 액세스 권한 부여

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

* 삭제 된 키 복구

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

* 일시 삭제 된 키를 제거 합니다 **(경고! 이 작업을 수행 하면 키가 영구적으로 삭제 됨)**

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {KEY NAME}
    ```

## <a name="secrets-cli"></a>비밀 (CLI)

* 비밀을 제거 하 고 복구할 수 있는 액세스 권한 부여

    ```azurecli
    az keyvault set-policy --upn user@contoso.com --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} -n {VAULT NAME}  --secret-permissions recover purge
    ```

* 비밀 삭제

    ```azurecli
    az keyvault secret delete --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 삭제 된 암호 나열

    ```azurecli
    az keyvault secret list-deleted --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME}
    ```

* 삭제 된 비밀 복구

    ```azurecli
    az keyvault secret recover --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

* 일시 삭제 된 비밀 제거 **(경고! 이 작업을 수행 하면 비밀이 영구적으로 삭제 됨)**

    ```azurecli
    az keyvault secret purge --subscription {SUBSCRIPTION ID} --vault-name {VAULT NAME} --name {SECRET NAME}
    ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

## <a name="key-vault-powershell"></a>Key Vault (PowerShell)

* 키 자격 증명 모음에서 일시 삭제를 사용 하도록 설정 했는지 확인

    ```powershell
    Get-AzKeyVault -VaultName "ContosoVault"
    ```

* 키 자격 증명 모음에 대해 일시 삭제를 사용 하도록 설정

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

* 키 자격 증명 모음 삭제

    ```powershell
    Remove-AzKeyVault -VaultName 'ContosoVault'
    ```

* 일시 삭제 된 키 자격 증명 모음 모두 나열

    ```powershell
    Get-AzKeyVault -InRemovedState
    ```

* 일시 삭제 된 키 복구-자격 증명 모음

    ```powershell
    Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
    ```

* 일시 삭제 된 키 자격 증명 모음 **을 제거 합니다 (경고! 이 작업은 키 자격 증명 모음을 영구적으로 삭제 합니다.)**

    ```powershell
    Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
    ```

* 키 자격 증명 모음에서 제거 방지 사용

    ```powershell
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```

## <a name="certificates-powershell"></a>인증서 (PowerShell)

* 인증서를 복구 및 제거할 수 있는 권한 부여

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToCertificates recover,purge
    ```

* 인증서 삭제

  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

* 키 자격 증명 모음에서 삭제 된 모든 인증서 나열

  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

* 삭제 된 상태의 인증서 복구

  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

* 일시 삭제 된 인증서를 제거 합니다 **(경고! 이 작업을 수행 하면 인증서가 영구적으로 삭제 됩니다.)**

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState
  ```

## <a name="keys-powershell"></a>키 (PowerShell)

* 키 복구 및 제거 권한 부여

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys recover,purge
    ```

* 키 삭제

  ```powershell
  Remove-AzKeyVaultKey -VaultName ContosoVault -Name 'MyKey'
  ```

* 키 자격 증명 모음에서 삭제 된 모든 인증서 나열

  ```powershell
  Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
  ```

* 일시 삭제 된 키를 복구 하려면

    ```powershell
    Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
    ```

* 일시 삭제 된 키를 제거 합니다 **(경고! 이 작업을 수행 하면 키가 영구적으로 삭제 됨)**

    ```powershell
    Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
    ```

## <a name="secrets-powershell"></a>비밀 (PowerShell)

* 암호를 복구 및 제거할 수 있는 권한 부여

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToSecrets recover,purge
    ```

* SQLPassword 라는 암호를 삭제 합니다.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

* 키 자격 증명 모음에서 삭제 된 모든 암호 나열

  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

* 삭제 된 상태의 비밀 복구

  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

* 삭제 된 상태의 비밀 제거 **(경고! 이 작업을 수행 하면 키가 영구적으로 삭제 됨)**

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```
