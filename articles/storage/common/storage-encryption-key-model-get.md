---
title: 저장소 계정에 사용 중인 암호화 키 모델 확인
titleSuffix: Azure Storage
description: Azure 포털, PowerShell 또는 Azure CLI를 사용하여 저장소 계정에 대해 암호화 키가 관리되는 방식을 확인합니다. 키는 Microsoft(기본값) 또는 고객이 관리할 수 있습니다. 고객 관리 키는 Azure 키 자격 증명 모음에 저장해야 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0df0ba4ce76d249bcb4738b41c94677e061f14ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409861"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>저장소 계정에 사용 중인 Azure 저장소 암호화 키 모델 확인

저장소 계정의 데이터는 Azure 저장소에 의해 자동으로 암호화됩니다. Azure Storage 암호화는 저장소 계정 수준에서 암호화 키를 관리하기 위한 두 가지 옵션을 제공합니다.

- **Microsoft에서 관리하는 키입니다.** 기본적으로 Microsoft는 저장소 계정을 암호화하는 데 사용되는 키를 관리합니다.
- **고객 관리 키.** 선택적으로 저장소 계정의 암호화 키관리를 선택할 수 있습니다. 고객 관리 키는 Azure 키 자격 증명 모음에 저장해야 합니다.

또한 일부 Blob 저장소 작업에 대한 개별 요청 수준에서 암호화 키를 제공할 수 있습니다. 요청에 암호화 키가 지정되면 해당 키는 저장소 계정에서 활성화된 암호화 키를 재정의합니다. 자세한 내용은 [Blob 저장소에 대한 요청에 대해 고객이 제공한 키 지정을](../blobs/storage-blob-customer-provided-key.md)참조하십시오.

암호화 키에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화를](storage-service-encryption.md)참조하십시오.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>저장소 계정의 암호화 키 모델 확인

저장소 계정이 암호화를 위해 Microsoft 관리 키 또는 고객 관리 키를 사용하고 있는지 확인하려면 다음 방법 중 하나를 사용합니다.

# <a name="azure-portal"></a>[Azure 포털](#tab/portal)

Azure 포털을 사용하여 저장소 계정의 암호화 모델을 확인하려면 다음 단계를 따르십시오.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **암호화** 설정을 선택하고 설정을 기록합니다.

다음 이미지는 Microsoft 관리 키로 암호화된 저장소 계정을 보여 주며,

![Microsoft 관리 키로 암호화된 계정 보기](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

다음 이미지는 고객 관리 키로 암호화된 저장소 계정을 보여 주며, 다음 이미지는 다음과 같은 것입니다.

![Azure 포털에서 암호화 키 설정을 보여주는 스크린샷](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[Powershell](#tab/powershell)

PowerShell을 사용하여 저장소 계정의 암호화 모델을 확인하려면 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출한 다음 계정에 대한 **KeySource** 속성을 확인합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

**KeySource** 속성의 값이 `Microsoft.Storage`있으면 계정은 Microsoft 에서 관리하는 키로 암호화됩니다. **KeySource** 속성의 `Microsoft.Keyvault`값이 있으면 계정이 고객 관리 키로 암호화됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 저장소 계정의 암호화 모델을 확인하려면 [az 저장소 계정 표시](/cli/azure/storage/account#az-storage-account-show) 명령을 호출한 다음 계정에 대한 **keySource** 속성을 확인합니다.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

**keySource** 속성의 `Microsoft.Storage`값이 있으면 계정은 Microsoft 관리 키로 암호화됩니다. **keySource** 속성의 `Microsoft.Keyvault`값이 있으면 계정이 고객 관리 키로 암호화됩니다.

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure 저장소 암호화](storage-service-encryption.md)
- [Azure 키 볼트를 사용하여 고객 관리 키를 사용하여 Azure 저장소 암호화 관리](encryption-customer-managed-keys.md)