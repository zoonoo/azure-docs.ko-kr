---
title: 데이터의 이중 암호화를 위해 인프라 암호화가 사용 하도록 설정 된 저장소 계정 만들기
titleSuffix: Azure Storage
description: 데이터 보안을 강화 해야 하는 높은 수준의 보증을 요구 하는 고객은 Azure Storage 인프라 수준에서 256 비트 AES 암호화를 사용 하도록 설정할 수도 있습니다. 인프라 암호화를 사용 하는 경우 저장소 계정의 데이터는 두 개의 서로 다른 암호화 알고리즘과 두 개의 다른 키를 사용 하 여 두 번 암호화 됩니다.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3164de9c3e44001d58d46eab9f823041b440960b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984156"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>데이터의 이중 암호화를 위해 인프라 암호화가 사용 하도록 설정 된 저장소 계정 만들기

Azure Storage는 256 비트 AES 암호화를 사용 하 여 서비스 수준에서 저장소 계정의 모든 데이터를 자동으로 암호화 하 고, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며, FIPS 140-2 규격입니다. 데이터 보안을 강화 해야 하는 높은 수준의 보증을 요구 하는 고객은 Azure Storage 인프라 수준에서 256 비트 AES 암호화를 사용 하도록 설정할 수도 있습니다. 인프라 암호화를 사용 하는 경우 저장소 계정의 데이터는 서비스 수준에서 한 번, 두 개의 &mdash; &mdash; 서로 다른 암호화 알고리즘과 두 개의 다른 키를 사용 하 여 인프라 수준에서 한 번씩 암호화 됩니다. Azure Storage 데이터의 이중 암호화는 암호화 알고리즘 또는 키 중 하나가 손상 될 수 있는 시나리오를 방지 합니다. 이 시나리오에서 추가 암호화 계층은 계속 해 서 데이터를 보호 합니다.

서비스 수준 암호화는 Azure Key Vault 또는 Key Vault 관리 되는 HSM (하드웨어 보안 모델) (미리 보기)을 사용 하 여 Microsoft에서 관리 하는 키 또는 고객이 관리 하는 키를 사용할 수 있도록 지원 합니다. 인프라 수준 암호화는 Microsoft에서 관리 하는 키를 기반으로 하며 항상 별도의 키를 사용 합니다. Azure Storage 암호화를 사용한 키 관리에 대 한 자세한 내용은 [암호화 키 관리 정보](storage-service-encryption.md#about-encryption-key-management)를 참조 하세요.

데이터를 이중으로 암호화 하려면 먼저 인프라 암호화를 위해 구성 된 저장소 계정을 만들어야 합니다. 이 문서에서는 인프라 암호화를 사용 하도록 설정 하는 저장소 계정을 만드는 방법을 설명 합니다.

## <a name="register-to-use-infrastructure-encryption"></a>인프라 암호화를 사용 하도록 등록

인프라 암호화가 사용 하도록 설정 된 저장소 계정을 만들려면 먼저 PowerShell 또는 Azure CLI를 사용 하 여 Azure에서이 기능을 사용 하도록 등록 해야 합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/A

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell에 등록 하려면 [AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) 명령을 호출 합니다.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

PowerShell을 사용 하 여 등록 상태를 확인 하려면 [AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 호출 합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

등록이 승인 된 후 Azure Storage 리소스 공급자를 다시 등록 해야 합니다. PowerShell을 사용 하 여 리소스 공급자를 다시 등록 하려면 [AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 호출 합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 등록 하려면 [az feature register](/cli/azure/feature#az-feature-register) 명령을 호출 합니다.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Azure CLI 등록 상태를 확인 하려면 [az feature](/cli/azure/feature#az-feature-show) 명령을 호출 합니다.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

등록이 승인 된 후 Azure Storage 리소스 공급자를 다시 등록 해야 합니다. Azure CLI를 사용 하 여 리소스 공급자를 다시 등록 하려면 [az provider register](/cli/azure/provider#az-provider-register) 명령을 호출 합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[템플릿](#tab/template)

N/A

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>인프라 암호화가 사용 하도록 설정 된 계정 만들기

계정을 만들 때 인프라 암호화를 사용 하도록 저장소 계정을 구성 해야 합니다. 저장소 계정은 범용 v2 유형 이어야 합니다.

계정을 만든 후에는 인프라 암호화를 사용 하거나 사용 하지 않도록 설정할 수 없습니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

PowerShell을 사용 하 여 인프라 암호화가 사용 하도록 설정 된 저장소 계정을 만들려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **저장소 계정** 페이지로 이동 합니다.
1. **추가** 단추를 선택 하 여 범용 v2 저장소 계정을 새로 추가 합니다.
1. **고급** 탭에서 **인프라** 암호화를 찾은 다음 **사용**을 선택 합니다.
1. **검토 + 만들기** 를 선택 하 여 저장소 계정 만들기를 완료 합니다.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="계정을 만들 때 인프라 암호화를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 인프라 암호화가 사용 하도록 설정 된 저장소 계정을 만들려면 [Az. Storage PowerShell module](https://www.powershellgallery.com/packages/Az.Storage), version 2.2.0 이상을 설치 했는지 확인 합니다. 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

그런 다음 [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출 하 여 범용 v2 저장소 계정을 만듭니다. `-RequireInfrastructureEncryption`인프라 암호화를 사용 하도록 설정 하는 옵션을 포함 합니다.

다음 예제에서는 읽기 액세스 지역 중복 저장소 (RA-GRS)에 대해 구성 된 범용 v2 저장소 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용 하도록 설정 하는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 인프라 암호화가 사용 하도록 설정 된 저장소 계정을 만들려면 Azure CLI 버전 2.8.0 이상을 설치 했는지 확인 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 명령을 호출 하 여 범용 v2 저장소 계정을 만들고를 포함 `--require-infrastructure-encryption option` 하 여 인프라 암호화를 사용 하도록 설정 합니다.

다음 예제에서는 읽기 액세스 지역 중복 저장소 (RA-GRS)에 대해 구성 된 범용 v2 저장소 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용 하도록 설정 하는 방법을 보여 줍니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

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

다음 JSON 예제에서는 읽기 액세스 지역 중복 저장소 (RA-GRS)에 대해 구성 된 범용 v2 저장소 계정을 만들고 데이터의 이중 암호화에 대해 인프라 암호화를 사용 하도록 설정 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

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

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>인프라 암호화가 사용 하도록 설정 되어 있는지 확인

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal를 사용 하 여 저장소 계정에 인프라 암호화가 사용 하도록 설정 되어 있는지 확인 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 스토리지 계정으로 이동합니다.
1. **설정**에서 **암호화**를 선택 합니다.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="계정을 만들 때 인프라 암호화를 사용 하도록 설정 하는 방법을 보여 주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하 여 저장소 계정에 대해 인프라 암호화가 사용 하도록 설정 되었는지 확인 하려면 [AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출 합니다. 이 명령은 저장소 계정 속성 및 해당 값의 집합을 반환 합니다. `RequireInfrastructureEncryption`속성 내에서 필드를 검색 하 `Encryption` 고가로 설정 되어 있는지 확인 `True` 합니다.

다음 예제에서는 속성의 값을 검색 합니다 `RequireInfrastructureEncryption` . 꺾쇠 괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 저장소 계정에 대 한 인프라 암호화가 사용 하도록 설정 되었는지 확인 하려면 [az storage account show](/cli/azure/storage/account#az-storage-account-show) 명령을 호출 합니다. 이 명령은 저장소 계정 속성 및 해당 값의 집합을 반환 합니다. `requireInfrastructureEncryption`속성 내에서 필드를 찾아 `encryption` 로 설정 되었는지 확인 `true` 합니다.

다음 예제에서는 속성의 값을 검색 합니다 `requireInfrastructureEncryption` . 꺾쇠 괄호 안의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

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
- [Azure Storage 암호화를 위한 고객 관리 키](customer-managed-keys-overview.md)