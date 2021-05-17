---
title: 데이터의 이중 암호화를 위해 인프라 암호화가 사용하도록 설정된 스토리지 계정 만들기
titleSuffix: Azure Storage
description: 데이터가 안전하다는 높은 수준의 보증을 요구하는 고객은 Azure Storage 인프라 수준에서 256비트 AES 암호화를 사용하도록 설정할 수도 있습니다. 인프라 암호화를 사용하는 경우 스토리지 계정의 데이터는 서로 다른 암호화 알고리즘 2개와 다른 키 2개를 사용하여 두 번 암호화됩니다.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 612ba18ba71a22ad6c346b26008e688195c1d1e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746570"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>데이터의 이중 암호화를 위해 인프라 암호화가 사용하도록 설정된 스토리지 계정 만들기

Azure Storage는 256비트 AES 암호화를 사용하여 서비스 수준에서 스토리지 계정의 모든 데이터를 자동으로 암호화하는, 사용 가능한 가장 강력한 블록 암호화 중 하나이며, FIPS 140-2를 준수합니다. 데이터가 안전하다는 높은 수준의 보증을 요구하는 고객은 Azure Storage 인프라 수준에서 256비트 AES 암호화를 사용하도록 설정할 수도 있습니다. 인프라 암호화를 사용하도록 설정하면 스토리지 계정의 데이터가 다른 암호화 알고리즘 2개와 다른 키 2개를 사용하여 두 번(서비스 수준에서 한 번, 인프라 수준에서 한 번) 암호화됩니다. Azure Storage 데이터의 이중 암호화는 암호화 알고리즘 또는 키 중 하나가 손상될 수 있는 시나리오로부터 보호합니다. 이 시나리오에서 추가 암호화 계층은 계속해서 데이터를 보호합니다.

서비스 수준 암호화는 Azure Key Vault 또는 Key Vault 관리형 HSM(하드웨어 보안 모델)(미리 보기)을 사용하여 Microsoft에서 관리형 키 또는 고객 관리형 키를 사용할 수 있도록 지원합니다. 인프라 수준 암호화는 Microsoft 관리형 키를 기반으로 하며 항상 별도의 키를 사용합니다. Azure Storage 암호화를 사용한 키 관리에 대한 자세한 내용은 [암호화 키 관리 정보](storage-service-encryption.md#about-encryption-key-management)를 참조하세요.

데이터를 이중으로 암호화하려면 먼저 인프라 암호화를 위해 구성된 스토리지 계정을 만들어야 합니다. 이 문서에서는 인프라 암호화를 사용하도록 설정하는 스토리지 계정을 만드는 방법을 설명합니다.

## <a name="register-to-use-infrastructure-encryption"></a>인프라 암호화를 사용하도록 등록

인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 먼저 PowerShell 또는 Azure CLI를 사용하여 Azure에서 이 기능을 사용하도록 등록해야 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

해당 없음

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 등록하려면 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 명령을 호출합니다.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

PowerShell 등록 상태를 확인하려면 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 사용합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

등록이 승인된 후 Azure Storage 리소스 공급자를 다시 등록해야 합니다. PowerShell을 사용하여 리소스 공급자를 다시 등록하려면 [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 호출합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 등록하려면 [az feature register](/cli/azure/feature#az-feature-register) 명령을 호출합니다.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Azure CLI 등록 상태를 확인하려면 [az feature](/cli/azure/feature#az-feature-show) 명령을 사용합니다.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

등록이 승인된 후 Azure Storage 리소스 공급자를 다시 등록해야 합니다. Azure CLI로 리소스 제공자를 다시 등록하려면 [az provider register](/cli/azure/provider#az-provider-register) 명령을 호출합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>인프라 암호화를 사용하도록 설정하여 계정 만들기

계정을 만들 때 인프라 암호화를 사용하도록 스토리지 계정을 구성해야 합니다. 스토리지 계정은 GPv2(범용 v2) 형식이어야 합니다.

계정을 만든 후에는 인프라 암호화를 사용하거나 사용하지 않도록 설정할 수 없습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

PowerShell을 사용하여 인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에서 **스토리지 계정** 페이지로 이동합니다.
1. **추가** 단추를 선택하여 GPv2(범용 v2) 스토리지 계정을 새로 추가합니다.
1. **고급** 탭에서 **인프라** 암호화를 찾은 다음 **사용** 을 선택합니다.
1. **검토 + 만들기** 를 선택하여 스토리지 계정 만들기를 완료합니다.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="계정을 만들 때 인프라 암호화를 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 [Az.Storage PowerShell 모듈](https://www.powershellgallery.com/packages/Az.Storage) 버전 2.2.0 이상을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

다음으로, [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출하여 GPv2(범용 v2) 스토리지 계정을 만듭니다. 인프라 암호화를 사용하도록 설정하는 `-RequireInfrastructureEncryption` 옵션을 포함합니다.

다음 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성된 GPv2(범용 v2) 스토리지 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용하도록 설정하는 방법을 보여 줍니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 인프라 암호화가 사용하도록 설정된 스토리지 계정을 만들려면 Azure CLI 버전 2.8.0 이상을 설치했는지 확인해야 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 명령을 호출하여 GPv2(범용 v2) 스토리지 계정을 만들고 인프라 암호화를 사용하도록 설정하는 `--require-infrastructure-encryption option`을 포함합니다.

다음 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성된 GPv2(범용 v2) 스토리지 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용하도록 설정하는 방법을 보여 줍니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[템플릿](#tab/template)

다음 JSON 예제에서는 RA-GRS(읽기 액세스 지역 중복 스토리지)에 대해 구성된 GPv2(범용 v2) 스토리지 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용하도록 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>인프라 암호화가 사용하도록 설정되어 있는지 확인

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 스토리지 계정에 인프라 암호화가 사용하도록 설정되어 있는지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정** 아래에서 **암호화** 를 선택합니다.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="계정에 대해 인프라 암호화가 사용하도록 설정되었는지 확인하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정에 대해 인프라 암호화가 사용하도록 설정되었는지 확인하려면 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출합니다. 이 명령은 스토리지 계정 속성 및 해당 값의 집합을 반환합니다. `Encryption` 속성 내에서 `RequireInfrastructureEncryption` 필드를 검색하고 이 필드가 `True`로 설정되어 있는지 확인합니다.

다음 예제에서는 `RequireInfrastructureEncryption` 속성의 값을 검색합니다. 꺾쇠괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 스토리지 계정에 대해 인프라 암호화가 사용하도록 설정되었는지 확인하려면 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 명령을 호출합니다. 이 명령은 스토리지 계정 속성 및 해당 값의 집합을 반환합니다. `encryption` 속성 내에서 `requireInfrastructureEncryption` 필드를 찾고 이 필드가 `true`로 설정되어 있는지 확인합니다.

다음 예제에서는 `requireInfrastructureEncryption` 속성의 값을 검색합니다. 꺾쇠괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화용 고객 관리형 키](customer-managed-keys-overview.md)
