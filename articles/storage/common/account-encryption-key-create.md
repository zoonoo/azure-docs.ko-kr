---
title: 테이블 및 큐에 대해 고객 관리 키를 지원하는 계정 만들기
titleSuffix: Azure Storage
description: 테이블 및 큐에 대한 고객 관리 키 구성을 지원하는 저장소 계정을 만드는 방법을 알아봅니다. Azure CLI 또는 Azure 리소스 관리자 템플릿을 사용하여 Azure 저장소 암호화에 대한 계정 암호화 키를 사용하는 저장소 계정을 만듭니다. 그런 다음 계정에 대해 고객 관리 키를 구성할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083557"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>테이블 및 큐에 대해 고객 관리 키를 지원하는 계정 만들기

Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화합니다. 기본적으로 큐 저장소 및 테이블 저장소는 서비스 범위가 정해지고 Microsoft에서 관리하는 키를 사용합니다. 고객 관리 키를 사용하여 큐 또는 테이블 데이터를 암호화하도록 선택할 수도 있습니다. 큐 및 테이블이 있는 고객 관리 키를 사용하려면 먼저 서비스가 아닌 계정으로 범위가 지정되는 암호화 키를 사용하는 저장소 계정을 만들어야 합니다. 큐 및 테이블 데이터에 대해 계정 암호화 키를 사용하는 계정을 만든 후 해당 저장소 계정에 대해 Azure Key Vault를 사용하여 고객 관리 키를 구성할 수 있습니다.

이 문서에서는 계정으로 범위가 조정된 키를 사용하는 저장소 계정을 만드는 방법에 대해 설명합니다. 계정이 처음 생성되면 Microsoft는 계정 키를 사용하여 계정의 데이터를 암호화하고 Microsoft는 키를 관리합니다. 이후에 계정의 고객 관리 키를 구성하여 사용자 고유의 키를 제공하고, 키 버전을 업데이트하고, 키를 회전하고, 액세스 제어를 해지하는 기능을 비롯한 이러한 이점을 활용할 수 있습니다.

## <a name="about-the-feature"></a>기능에 대해

Queue 및 테이블 저장소에 대한 계정 암호화 키를 사용하는 저장소 계정을 만들려면 먼저 이 기능을 Azure에 사용하려면 등록해야 합니다. 제한된 용량으로 인해 액세스 요청이 승인되기까지 몇 달이 걸릴 수 있습니다.

다음 리전에서 Queue 및 Table 저장소에 대한 계정 암호화 키를 사용하는 저장소 계정을 만들 수 있습니다.

- 미국 동부
- 미국 중남부
- 미국 서부 2  

### <a name="register-to-use-the-account-encryption-key"></a>계정 암호화 키를 사용하려면 등록

큐 또는 테이블 저장소에 계정 암호화 키를 사용하도록 등록하려면 PowerShell 또는 Azure CLI를 사용합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell에 등록하려면 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 호출합니다.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 등록하려면 [az 기능 레지스터](/cli/azure/feature#az-feature-register) 명령을 호출합니다.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

### <a name="check-the-status-of-your-registration"></a>등록 상태 확인

큐 또는 테이블 저장소에 대한 등록 상태를 확인하려면 PowerShell 또는 Azure CLI를 사용합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell으로 등록 상태를 확인하려면 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 명령을 호출합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI로 등록 상태를 확인하려면 [az 기능](/cli/azure/feature#az-feature-show) 명령을 호출합니다.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Azure 저장소 리소스 공급자 다시 등록

등록이 승인되면 Azure Storage 리소스 공급자를 다시 등록해야 합니다. PowerShell 또는 Azure CLI를 사용하여 리소스 공급자를 다시 등록합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell으로 리소스 공급자를 다시 등록하려면 [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 호출합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 리소스 공급자를 다시 등록하려면 [az 공급자 레지스터](/cli/azure/provider#az-provider-register) 명령을 호출합니다.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>계정 암호화 키를 사용하는 계정 만들기

저장소 계정을 만들 때 큐 및 테이블에 대해 계정 암호화 키를 사용하도록 새 저장소 계정을 구성해야 합니다. 계정을 만든 후에는 암호화 키의 범위를 변경할 수 없습니다.

저장소 계정은 범용 v2 형식이어야 합니다. 저장소 계정을 만들고 Azure CLI 또는 Azure 리소스 관리자 템플릿을 사용하여 계정 암호화 키에 의존하도록 구성할 수 있습니다.

> [!NOTE]
> 저장소 계정을 만들 때 계정 암호화 키로 데이터를 암호화하도록 큐 및 테이블 저장소만 선택적으로 구성할 수 있습니다. Blob 저장소 및 Azure 파일은 항상 계정 암호화 키를 사용하여 데이터를 암호화합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell을 사용하여 계정 암호화 키를 사용하는 저장소 계정을 만들려면 Azure PowerShell 모듈 버전 3.4.0 이상에 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell 설치 모듈을](/powershell/azure/install-az-ps)참조하십시오.

다음으로 적절한 매개 변수를 사용하여 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 명령을 호출하여 범용 v2 저장소 계정을 만듭니다.

- `-EncryptionKeyTypeForQueue` 옵션을 포함하고 큐 저장소의 `Account` 데이터를 암호화하기 위해 계정 암호화 키를 사용하도록 값을 설정합니다.
- `-EncryptionKeyTypeForTable` 옵션을 포함하고 테이블 저장소의 `Account` 데이터를 암호화하기 위해 계정 암호화 키를 사용하도록 값을 설정합니다.

다음 예제에서는 읽기 액세스 지리적 중복 저장소(RA-GRS)를 위해 구성되고 계정 암호화 키를 사용하여 Queue 및 Table 저장소 모두에 대한 데이터를 암호화하는 범용 v2 저장소 계정을 만드는 방법을 보여 주습니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용하여 계정 암호화 키를 사용하는 저장소 계정을 만들려면 Azure CLI 버전 2.0.80 이상에 설치했는지 확인합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음으로 az 저장소 계정 [create](/cli/azure/storage/account#az-storage-account-create) 명령을 호출하 여 범용 v2 저장소 계정을 만들고 적절한 매개 변수를 사용합니다.

- `--encryption-key-type-for-queue` 옵션을 포함하고 큐 저장소의 `Account` 데이터를 암호화하기 위해 계정 암호화 키를 사용하도록 값을 설정합니다.
- `--encryption-key-type-for-table` 옵션을 포함하고 테이블 저장소의 `Account` 데이터를 암호화하기 위해 계정 암호화 키를 사용하도록 값을 설정합니다.

다음 예제에서는 읽기 액세스 지리적 중복 저장소(RA-GRS)를 위해 구성되고 계정 암호화 키를 사용하여 Queue 및 Table 저장소 모두에 대한 데이터를 암호화하는 범용 v2 저장소 계정을 만드는 방법을 보여 주습니다. 괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[템플릿](#tab/template)

다음 JSON 예제에서는 읽기 액세스 지리적 중복 저장소(RA-GRS)를 위해 구성되고 계정 암호화 키를 사용하여 Queue 및 Table 저장소 모두에 대한 데이터를 암호화하는 범용 v2 저장소 계정을 만듭니다. 각도 대괄호의 자리 표시자 값을 고유한 값으로 바꿔야 합니다.

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
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

계정 암호화 키를 사용하는 계정을 만든 후 다음 문서 중 하나를 참조하여 Azure Key Vault를 사용하여 고객 관리 키를 구성합니다.

- [Azure 포털을 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성](storage-encryption-keys-portal.md)
- [PowerShell을 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성](storage-encryption-keys-powershell.md)
- [Azure CLI를 사용하여 Azure 키 자격 증명 모음을 사용하여 고객 관리 키 구성](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>계정 암호화 키 확인

저장소 계정의 서비스가 계정 암호화 키를 사용하고 있는지 확인하려면 Azure CLI [az 저장소 계정](/cli/azure/storage/account#az-storage-account-show) 명령을 호출합니다. 이 명령은 저장소 계정 속성 및 해당 값 집합을 반환합니다. 암호화 속성 `keyType` 내의 각 서비스에 대한 필드를 찾아 로 `Account`설정되어 있는지 확인합니다.

# <a name="powershell"></a>[Powershell](#tab/powershell)

저장소 계정의 서비스가 계정 암호화 키를 사용하고 있는지 확인하려면 [Get-AzStorageAccount 명령을 호출합니다.](/powershell/module/az.storage/get-azstorageaccount) 이 명령은 저장소 계정 속성 및 해당 값 집합을 반환합니다. 속성 내의 `KeyType` 각 서비스에 대한 필드를 찾아 서 `Account`설정되어 있는지 확인합니다. `Encryption`

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

저장소 계정의 서비스가 계정 암호화 키를 사용하고 있는지 확인하려면 [az 저장소 계정](/cli/azure/storage/account#az-storage-account-show) 명령을 호출합니다. 이 명령은 저장소 계정 속성 및 해당 값 집합을 반환합니다. 암호화 속성 `keyType` 내의 각 서비스에 대한 필드를 찾아 로 `Account`설정되어 있는지 확인합니다.

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[템플릿](#tab/template)

해당 없음

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure 저장소 암호화](storage-service-encryption.md) 
- [Azure 키 볼트란?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
