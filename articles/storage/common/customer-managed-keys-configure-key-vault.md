---
title: Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성
titleSuffix: Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 Azure Key Vault에 저장 된 고객이 관리 하는 키를 사용 하 여 Azure Storage 암호화를 구성 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 02661c9c2a581ab21a2ae9dc31e5da95426c0edd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843386"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault"></a>Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성

Azure Storage는 미사용 스토리지 계정의 모든 데이터를 암호화합니다. 기본적으로 데이터는 Microsoft 관리형 키로 암호화됩니다. 암호화 키에 대 한 추가 제어를 위해 사용자 고유의 키를 관리할 수 있습니다. 고객 관리 키는 Azure Key Vault 또는 Key Vault 관리 되는 HSM (하드웨어 보안 모델) (미리 보기)에 저장 되어야 합니다.

이 문서에서는 Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 주요 자격 증명 모음에 저장 된 고객 관리 키를 사용 하 여 암호화를 구성 하는 방법을 보여 줍니다. 관리 되는 HSM에 저장 된 고객 관리 키를 사용 하 여 암호화를 구성 하는 방법을 알아보려면 [관리 되는 hsm에 Azure Key Vault 저장 된 고객 관리 키를 사용 하 여 암호화 구성 (미리 보기)](customer-managed-keys-configure-key-vault-hsm.md)을 참조 하세요.

> [!NOTE]
> Azure Key Vault 및 Azure Key Vault 관리 HSM은 구성에 대해 동일한 Api 및 관리 인터페이스를 지원 합니다.

## <a name="configure-a-key-vault"></a>키 자격 증명 모음 구성

신규 또는 기존 키 자격 증명 모음을 사용 하 여 고객 관리 키를 저장할 수 있습니다. 스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다.

Azure Storage 암호화와 함께 고객 관리 키를 사용 하려면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호를 모두 사용 하도록 설정 해야 합니다. 일시 삭제는 새 키 자격 증명 모음을 만들 때 기본적으로 사용 하도록 설정 되며 해제할 수 없습니다. 키 자격 증명 모음을 만들 때 또는 생성 된 후에 제거 보호를 사용 하도록 설정할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure Portal를 사용 하 여 주요 자격 증명 모음 만들기](../../key-vault/general/quick-create-portal.md)를 참조 하세요. 키 자격 증명 모음을 만들 때 다음 그림에 표시 된 것 처럼 **보호 제거 사용** 을 선택 합니다.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/configure-key-vault-portal.png" alt-text="주요 자격 증명 모음을 만들 때 보호 제거를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

기존 키 자격 증명 모음에 대 한 보호 제거를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 주요 자격 증명 모음으로 이동 합니다.
1. **설정** 에서 **속성** 을 선택 합니다.
1. **보호 제거** 섹션에서 **보호 제거 사용** 을 선택 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 새 키 자격 증명 모음을 만들려면 [Az. KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault/2.0.0) PowerShell 모듈의 버전 2.0.0 이상을 설치 합니다. 그런 다음 [AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) 를 호출 하 여 새 키 자격 증명 모음을 만듭니다. 2.0.0 이상 버전을 사용 하는 경우 새 키 자격 증명 모음을 만들 때 일시 삭제가 기본적으로 사용 하도록 설정 됩니다.

다음 예제에서는 일시 삭제 및 제거 보호를 모두 사용 하 여 새 key vault를 만듭니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnablePurgeProtection
```

PowerShell을 사용 하 여 기존 주요 자격 증명 모음에 대 한 보호 제거를 사용 하도록 설정 하는 방법을 알아보려면 [powershell에서 일시 삭제를 사용 하는 방법](../../key-vault/general/key-vault-recovery.md)을 참조 하세요.

그런 다음 시스템 할당 관리 id를 저장소 계정에 할당 합니다. 이 관리 되는 id를 사용 하 여 저장소 계정에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다. 시스템 할당 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

PowerShell을 사용 하 여 관리 되는 id를 할당 하려면 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount)를 호출 합니다.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

마지막으로, 저장소 계정에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 합니다. 이 단계에서는 이전에 저장소 계정에 할당 한 관리 되는 id를 사용 합니다.

키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)를 호출 합니다.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 새 키 자격 증명 모음을 만들려면 [az keyvault create](/cli/azure/keyvault#az-keyvault-create)를 호출 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-purge-protection
```

Azure CLI를 사용 하 여 기존 주요 자격 증명 모음에 대 한 보호 제거를 사용 하도록 설정 하는 방법은 [CLI에서 일시 삭제를 사용 하는 방법](../../key-vault/general/key-vault-recovery.md)을 참조 하세요.

그런 다음 시스템 할당 관리 id를 저장소 계정에 할당 합니다. 이 관리 되는 id를 사용 하 여 저장소 계정에 키 자격 증명 모음에 액세스할 수 있는 권한을 부여 합니다. 시스템 할당 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

Azure CLI를 사용 하 여 관리 되는 id를 할당 하려면 [az storage account update](/cli/azure/storage/account#az-storage-account-update)를 호출 합니다.

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

마지막으로, 저장소 계정에 액세스할 수 있는 권한이 있도록 키 자격 증명 모음에 대 한 액세스 정책을 구성 합니다. 이 단계에서는 이전에 저장소 계정에 할당 한 관리 되는 id를 사용 합니다.

키 자격 증명 모음에 대 한 액세스 정책을 설정 하려면 [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy)를 호출 합니다.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

---

## <a name="add-a-key"></a>키 추가

다음으로 키 자격 증명 모음에 키를 추가 합니다.

Azure Storage 암호화는 2048, 3072 및 4096 크기의 RSA 및 RSA HSM 키를 지원 합니다. 키에 대 한 자세한 내용은 [키](../../key-vault/keys/about-keys.md)정보를 참조 하세요.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 키를 추가 하는 방법에 대 한 자세한 내용은 [빠른 시작: Azure Portal를 사용 하 여 Azure Key Vault에서 키 설정 및 검색](../../key-vault/keys/quick-create-portal.md)을 참조 하세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 키를 추가 하려면 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName `
    -Name <key> `
    -Destination 'Software'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 키를 추가 하려면 [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create)를 호출 합니다. 대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
az keyvault key create \
    --name <key> \
    --vault-name <key-vault>
```

---

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리형 키로 암호화 구성

그런 다음 Azure Key Vault에서 고객 관리 키를 사용 하도록 Azure Storage 계정을 구성 하 고 저장소 계정과 연결할 키를 지정 합니다.

고객 관리 키를 사용 하 여 암호화를 구성 하는 경우 연결 된 키 자격 증명 모음에서 새 버전을 사용할 수 있을 때마다 Azure Storage 암호화에 사용 되는 키 버전을 자동으로 업데이트 하도록 선택할 수 있습니다. 또는 키 버전이 수동으로 업데이트 될 때까지 암호화에 사용할 키 버전을 명시적으로 지정할 수 있습니다.

> [!NOTE]
> 키를 회전 하려면 Azure Key Vault에서 키의 새 버전을 만듭니다. Azure Storage는 Azure Key Vault 키 회전을 처리 하지 않으므로 키를 수동으로 회전 하거나 일정에 따라 회전 하는 함수를 만들어야 합니다.

### <a name="configure-encryption-for-automatic-updating-of-key-versions"></a>키 버전 자동 업데이트에 대 한 암호화 구성

Azure Storage는 암호화에 사용 되는 고객 관리 키를 최신 키 버전을 사용 하도록 자동으로 업데이트할 수 있습니다. Azure Key Vault에서 고객이 관리 하는 키를 회전 하는 경우 Azure Storage는 최신 버전의 키를 사용 하 여 암호화를 자동으로 시작 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 키 버전의 자동 업데이트를 사용 하 여 고객 관리 키를 구성 하려면 다음 단계를 수행 합니다.

1. 스토리지 계정으로 이동합니다.
1. 스토리지 계정에 대한 **설정** 블레이드에서 **암호화** 를 클릭합니다. 다음 그림에 표시 된 것 처럼 **고객 관리 키** 옵션을 선택 합니다.

    ![암호화 옵션을 보여 주는 포털 스크린샷](./media/customer-managed-keys-configure-key-vault/portal-configure-encryption-keys.png)

1. **Key Vault에서 선택** 옵션을 선택합니다.
1. **키 자격 증명 모음 및 키 선택** 을 선택 합니다.
1. 사용할 키를 포함 하는 키 자격 증명 모음을 선택 합니다.
1. 키 자격 증명 모음에서 키를 선택 합니다.

   ![주요 자격 증명 모음 및 키를 선택 하는 방법을 보여 주는 스크린샷](./media/customer-managed-keys-configure-key-vault/portal-select-key-from-key-vault.png)

1. 변경 내용을 저장합니다.

키를 지정한 후에는 키 버전의 자동 업데이트가 사용 하도록 설정 되어 있으며 현재 암호화에 사용 중인 키 버전을 표시 하는 Azure Portal 나타냅니다.

:::image type="content" source="media/customer-managed-keys-configure-key-vault/portal-auto-rotation-enabled.png" alt-text="사용할 수 있는 키 버전의 자동 업데이트를 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 키 버전의 자동 업데이트를 사용 하 여 고객 관리 키를 구성 하려면 [Az. Storage](https://www.powershellgallery.com/packages/Az.Storage) 모듈, 버전 2.0.0 이상을 설치 합니다.

고객 관리 키에 대 한 키 버전을 자동으로 업데이트 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성할 때 키 버전을 생략 합니다. 다음 예제와 같이 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 하 고 **-KeyvaultEncryption** 옵션을 포함 하 여 저장소 계정에 대 한 고객 관리 키를 사용 하도록 설정 합니다.

괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVaultUri $keyVault.VaultUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 키 버전을 자동으로 업데이트 하 여 고객 관리 키를 구성 하려면 [Azure CLI 버전 2.4.0](/cli/azure/release-notes-azure-cli#april-21-2020) 이상을 설치 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

고객 관리 키에 대 한 키 버전을 자동으로 업데이트 하려면 저장소 계정에 대 한 고객 관리 키를 사용 하 여 암호화를 구성할 때 키 버전을 생략 합니다. 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 합니다. 매개 변수를 포함 하 `--encryption-key-source` 고로 설정 하 여 `Microsoft.Keyvault` 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 합니다.

대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

---

### <a name="configure-encryption-for-manual-updating-of-key-versions"></a>키 버전 수동 업데이트에 대 한 암호화 구성

키 버전을 수동으로 업데이트 하려면 고객이 관리 하는 키를 사용 하 여 암호화를 구성할 때 버전을 명시적으로 지정 합니다. 이 경우 키 자격 증명 모음에 새 버전이 만들어지면 Azure Storage에서 키 버전을 자동으로 업데이트 하지 않습니다. 새 키 버전을 사용 하려면 Azure Storage 암호화에 사용 되는 버전을 수동으로 업데이트 해야 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 키 버전의 수동 업데이트를 사용 하 여 고객 관리 키를 구성 하려면 버전을 포함 한 키 URI를 지정 합니다. 키를 URI로 지정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 키 URI를 찾으려면 키 자격 증명 모음으로 이동 하 고 **키 설정을 선택** 합니다. 원하는 키를 선택한 다음 키를 클릭 하 여 해당 버전을 확인 합니다. 해당 버전에 대 한 설정을 보려면 키 버전을 선택 합니다.
1. URI를 제공 하는 **키 식별자** 필드의 값을 복사 합니다.

    ![주요 자격 증명 모음 키 URI를 보여 주는 스크린샷](media/customer-managed-keys-configure-key-vault/portal-copy-key-identifier.png)

1. 저장소 계정에 대 한 **암호화 키** 설정에서 **키 URI 입력** 옵션을 선택 합니다.
1. 복사한 URI를 **키 uri** 필드에 붙여넣습니다. 키 버전의 자동 업데이트를 사용 하도록 설정 하려면 URI에서 키 버전을 생략 합니다.

   ![키 URI를 입력 하는 방법을 보여 주는 스크린샷](./media/customer-managed-keys-configure-key-vault/portal-specify-key-uri.png)

1. 키 자격 증명 모음을 포함 하는 구독을 지정 합니다.
1. 변경 내용을 저장합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

키 버전의 수동 업데이트를 사용 하 여 고객 관리 키를 구성 하려면 저장소 계정에 대 한 암호화를 구성할 때 키 버전을 명시적으로 제공 합니다. 다음 예제와 같이 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 하 고 **-KeyvaultEncryption** 옵션을 포함 하 여 저장소 계정에 대 한 고객 관리 키를 사용 하도록 설정 합니다.

괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

키 버전을 수동으로 업데이트 하는 경우 새 버전을 사용 하도록 저장소 계정의 암호화 설정을 업데이트 해야 합니다. 먼저 [AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) 를 호출 하 여 최신 버전의 키를 가져옵니다. 그런 다음 이전 예제와 같이 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 여 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

키 버전의 수동 업데이트를 사용 하 여 고객 관리 키를 구성 하려면 저장소 계정에 대 한 암호화를 구성할 때 키 버전을 명시적으로 제공 합니다. 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 여 저장소 계정의 암호화 설정을 업데이트 합니다. 매개 변수를 포함 하 `--encryption-key-source` 고로 설정 하 여 `Microsoft.Keyvault` 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 합니다.

대괄호 안의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [-1].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

키 버전을 수동으로 업데이트 하는 경우 새 버전을 사용 하도록 저장소 계정의 암호화 설정을 업데이트 해야 합니다. 먼저 az [keyvault show](/cli/azure/keyvault#az-keyvault-show)를 호출 하 여 KEY vault URI를 쿼리하고, [az keyvault 키 목록 버전](/cli/azure/keyvault/key#az-keyvault-key-list-versions)을 호출 하 여 키 버전을 쿼리 합니다. 그런 다음 이전 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 여 새 버전의 키를 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

---

## <a name="change-the-key"></a>키 변경

언제 든 지 암호화를 Azure Storage 하는 데 사용 하는 키를 변경할 수 있습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal 키를 변경 하려면 다음 단계를 수행 합니다.

1. 저장소 계정으로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 키 자격 증명 모음을 선택 하 고 새 키를 선택 합니다.
1. 변경 내용을 저장합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 키를 변경 하려면 [고객이 관리 하는 키를 사용 하 여 암호화 구성](#configure-encryption-with-customer-managed-keys) 에 표시 된 대로 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 하 고 새 키 이름 및 버전을 제공 합니다. 새 키가 다른 키 자격 증명 모음에 있으면 키 자격 증명 모음 URI도 업데이트 해야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 키를 변경 하려면 [고객이 관리 하는 키로 암호화 구성](#configure-encryption-with-customer-managed-keys) 에 표시 된 것 처럼 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 고 새 키 이름 및 버전을 제공 합니다. 새 키가 다른 키 자격 증명 모음에 있으면 키 자격 증명 모음 URI도 업데이트 해야 합니다.

---

## <a name="revoke-customer-managed-keys"></a>고객 관리 키 철회

고객 관리 키를 해지 하면 저장소 계정과 키 자격 증명 모음 간의 연결이 제거 됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 고객이 관리 하는 키를 해지 하려면 [고객이 관리 하는 키 사용 안 함](#disable-customer-managed-keys)에 설명 된 대로 키를 사용 하지 않도록 설정 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

키 자격 증명 모음 액세스 정책을 제거 하 여 고객 관리 키를 해지할 수 있습니다. PowerShell에서 고객이 관리 하는 키를 해지 하려면 다음 예제와 같이 [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/remove-azkeyvaultaccesspolicy) 명령을 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Remove-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

키 자격 증명 모음 액세스 정책을 제거 하 여 고객 관리 키를 해지할 수 있습니다. Azure CLI에서 고객이 관리 하는 키를 해지 하려면 다음 예제와 같이 [az keyvault delete 정책](/cli/azure/keyvault#az-keyvault-delete-policy) 명령을 호출 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```azurecli-interactive
az keyvault delete-policy \
    --name <key-vault> \
    --object-id $storage_account_principal
```

---

## <a name="disable-customer-managed-keys"></a>고객 관리 키 사용 안 함

고객 관리 키를 사용 하지 않도록 설정 하면 저장소 계정이 Microsoft 관리 키로 다시 암호화 됩니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal에서 고객이 관리 하는 키를 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. 저장소 계정으로 이동 하 여 **암호화** 설정을 표시 합니다.
1. 사용자의 **키 사용** 설정 옆에 있는 확인란의 선택을 취소 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에서 고객이 관리 하는 키를 사용 하지 않도록 설정 하려면 다음 예제와 같이 옵션을 사용 하 여 [AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 를 호출 `-StorageEncryption` 합니다. 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -StorageEncryption  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 고객이 관리 하는 키를 사용 하지 않도록 설정 하려면 다음 예제와 같이 [az storage account update](/cli/azure/storage/account#az-storage-account-update) 를 호출 하 고 `--encryption-key-source parameter` 를로 설정 합니다 `Microsoft.Storage` . 괄호 안의 자리 표시자 값을 고유한 값으로 바꾸고 앞의 예제에 정의 된 변수를 사용 해야 합니다.

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-source Microsoft.Storage
```

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화를 위한 고객 관리 키](customer-managed-keys-overview.md)
- [Azure Key Vault 관리 HSM에 저장 된 고객 관리 키를 사용 하 여 암호화 구성 (미리 보기)](customer-managed-keys-configure-key-vault-hsm.md)
