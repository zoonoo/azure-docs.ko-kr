---
title: 스토리지 계정에 사용 중인 암호화 키 모델 확인
titleSuffix: Azure Storage
description: Azure Portal, PowerShell 또는 Azure CLI를 사용하여 스토리지 계정의 암호화 키가 어떻게 관리되고 있는지 확인할 수 있습니다. 키는 Microsoft(기본값) 또는 고객이 관리할 수 있습니다. 고객 관리형 키는 Azure Key Vault에 저장해야 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 08bc36500bbd95633d1cb1d02bf10a7397401aa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780122"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>스토리지 계정에 사용 중인 Azure Storage 암호화 키 모델 확인

스토리지 계정의 데이터는 Azure Storage에서 자동으로 암호화됩니다. Azure Storage 암호화는 스토리지 계정 수준에서 암호화 키를 관리하기 위한 다음 두 가지 옵션을 제공합니다.

- **Microsoft 관리형 키** 기본적으로 Microsoft에서 스토리지 계정을 암호화하는 데 사용되는 키를 관리합니다.
- **고객 관리형 키** 필요에 따라 스토리지 계정의 암호화 키를 직접 관리할 수 있습니다. 고객 관리형 키는 Azure Key Vault에 저장해야 합니다.

또한 일부 Blob Storage 작업을 위해 개별 요청 수준에서 암호화 키를 제공할 수 있습니다. 요청에 암호화 키를 지정하면 해당 키는 스토리지 계정에서 활성화된 암호화 키를 재정의합니다. 자세한 내용은 [Blob Storage에 대한 요청에 고객이 제공한 키 지정](../blobs/storage-blob-customer-provided-key.md)을 참조하세요.

암호화 키에 대한 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)를 참조하세요.

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>스토리지 계정의 암호화 키 모델 확인

스토리지 계정에서 암호화를 위해 Microsoft 관리형 키를 사용하는지, 아니면 고객 관리형 키를 사용하는지 확인하려면 다음 방법 중 하나를 사용합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure Portal을 사용하여 스토리지 계정의 암호화 모델을 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.
1. **암호화** 설정을 선택하고 설정을 확인합니다.

다음 이미지는 Microsoft 관리형 키로 암호화된 스토리지 계정을 보여 줍니다.

![Microsoft 관리형 키로 암호화된 계정 보기](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

그리고 다음 이미지는 고객 관리형 키로 암호화된 스토리지 계정을 보여 줍니다.

![Azure Portal의 암호화 키 설정 스크린샷](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용하여 스토리지 계정의 암호화 모델을 확인하려면 [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) 명령을 호출한 다음, 계정의 **KeySource** 속성을 확인합니다.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

**KeySource** 속성의 값이 `Microsoft.Storage`이면 계정이 Microsoft 관리형 키로 암호화됩니다. **KeySource** 속성의 값이 `Microsoft.Keyvault`이면 계정이 고객 관리형 키로 암호화됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLI를 사용하여 스토리지 계정의 암호화 모델을 확인하려면 [az storage account show](/cli/azure/storage/account#az_storage_account_show) 명령을 호출한 다음, 계정의 **keySource** 속성을 확인합니다.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

**keySource** 속성의 값이 `Microsoft.Storage`이면 계정이 Microsoft 관리형 키로 암호화됩니다. **keySource** 속성의 값이 `Microsoft.Keyvault`이면 계정이 고객 관리형 키로 암호화됩니다.

---

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Storage 암호화용 고객 관리형 키](customer-managed-keys-overview.md)
