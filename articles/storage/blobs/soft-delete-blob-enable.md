---
title: Blob에 일시 삭제를 사용하도록 설정
titleSuffix: Azure Storage
description: Blob에 대해 일시 삭제를 사용하도록 설정하여 실수로 인한 삭제 또는 덮어쓰기로부터 Blob 데이터를 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554123"
---
# <a name="enable-soft-delete-for-blobs"></a>Blob에 일시 삭제를 사용하도록 설정

Blob 일시 삭제는 지정된 기간 동안 시스템에서 삭제된 데이터를 유지하여 개별 Blob 및 해당 버전, 스냅샷 및 메타데이터를 실수로 삭제하거나 덮어쓰지 않도록 보호합니다. 보존 기간 중에는 삭제 시 Blob을 해당 상태로 복원할 수 있습니다. 보존 기간이 만료되면 Blob이 영구적으로 삭제됩니다. Blob 일시 삭제에 대한 자세한 내용은 [Blob 일시 삭제](soft-delete-blob-overview.md)를 참조하세요.

Blob 일시 삭제는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

## <a name="enable-blob-soft-delete"></a>Blob 일시 삭제 사용

Blob 일시 삭제는 새 스토리지 계정에 대해 기본적으로 비활성화되어 있습니다. Azure Portal, PowerShell 또는 Azure CLI를 사용하여 언제든지 스토리지 계정에 대해 일시 삭제를 사용하거나 사용하지 않도록 설정할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정에 대해 Blob 일시 삭제를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정으로 이동합니다.
1. **Blob service** 아래에서 **데이터 보호** 옵션을 찾습니다.
1. **복구** 섹션에서 **Blob 일시 삭제 켜기** 를 선택합니다.
1. 1~365일 사이의 보존 기간을 지정합니다. Microsoft는 최소 7일의 보존 기간을 권장합니다.
1. 변경 내용을 저장합니다.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Azure Portal에서 일시 삭제를 활성화하는 방법을 보여주는 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 Blob 일시 삭제를 사용하도록 설정하려면 [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) 명령을 호출하여 보존 기간(일)을 지정합니다.

다음 예제에서는 Blob 일시 삭제를 사용하도록 설정하고 보존 기간을 7일로 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Blob 일시 삭제에 대한 현재 설정을 확인하려면 [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) 명령을 호출합니다.

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Azure CLI를 사용하여 Blob 일시 삭제를 사용하도록 설정하려면 [az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) 명령을 호출하여 보존 기간(일)을 지정합니다.

다음 예제에서는 Blob 일시 삭제를 사용하도록 설정하고 보존 기간을 7일로 설정합니다. 대괄호의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Blob 일시 삭제에 대한 현재 설정을 확인하려면 [az storage account blob-service-properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) 명령을 호출합니다.

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>다음 단계

- [Blob에 대한 일시 삭제](soft-delete-blob-overview.md)
- [일시 삭제된 Blob 관리 및 복원](soft-delete-blob-manage.md)
