---
title: Blob에 대한 일시 삭제를 사용 및 관리
titleSuffix: Azure Storage
description: Blob 개체에 대해 일시 삭제를 사용하여 데이터를 잘못 수정하거나 삭제할 때 더 쉽게 복구할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/15/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 970a6dfc167a6bef7984598c60e7ce89c6e4b34c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463725"
---
# <a name="enable-and-manage-soft-delete-for-blobs"></a>Blob에 대한 일시 삭제를 사용 및 관리

일시 삭제는 Blob 데이터가 실수로 또는 잘못하여 수정되거나 삭제되지 않도록 보호합니다. 스토리지 계정에 대해 일시 삭제를 사용할 때 해당 스토리지 계정의 Blob, Blob 버전(미리 보기) 및 스냅샷은 지정된 보존 기간 내에 삭제된 후 복구될 수 있습니다.

Microsoft는 데이터가 애플리케이션 또는 다른 스토리지 계정 사용자에 의해 실수로 수정 또는 삭제될 가능성이 있다면 일시 삭제를 설정할 것을 권장합니다.

이 문서에서는 일시 삭제를 시작하는 방법을 보여줍니다.

## <a name="enable-soft-delete"></a>일시 삭제 사용

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 스토리지 계정의 Blob에 대해 일시 삭제를 다음과 같이 사용합니다.

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 계정을 선택합니다. 

2. **Blob Service** 아래의 **데이터 보호** 옵션으로 이동합니다.

3. **Blob 일시 삭제**에서 **사용**을 클릭합니다.

4. *보존 정책*에**보존하려는**일 수를 입력합니다.

5. **저장** 단추를 선택하여 데이터 보호 설정을 확인합니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

일시 삭제된 Blob을 보려면 **삭제된 Blob 표시** 확인란을 선택합니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

지정된 Blob에 대한 일시 삭제된 스냅샷을 보려면 Blob을 선택한 다음, **스냅샷 보기**를 클릭합니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

**삭제된 스냅샷 표시** 확인란이 선택되었는지 확인합니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

일시 삭제된 Blob 또는 스냅샷을 클릭할 때 새 Blob 속성을 확인합니다. 개체가 삭제된 시점과 Blob 또는 Blob 스냅샷이 영구적으로 만료될 때까지 남은 일 수를 나타냅니다. 일시 삭제된 개체가 스냅샷이 아닌 경우 삭제를 취소하는 옵션을 갖게 됩니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-properties.png)

Blob 삭제 취소는 연결된 모든 스냅샷도 삭제 취소합니다. 활성 Blob에 대해 일시 삭제된 스냅샷을 삭제 취소하려면 Blob을 클릭하고 **모든 스냅샷 삭제 취소**를 선택합니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Blob의 스냅샷을 삭제 취소하면 **승격**을 클릭하여 루트 Blob에 대한 스냅샷을 복사할 수 있으므로 Blob을 스냅샷으로 복원합니다.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

일시 삭제를 활성화하려면 Blob 클라이언트의 서비스 속성을 업데이트합니다. 다음 예제에서는 구독에서 계정의 하위 집합에 대한 일시 삭제를 활성화합니다.

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```

다음 명령을 사용하여 일시 삭제가 켜졌는지 확인할 수 있습니다.

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

실수로 삭제된 Blob을 복구하려면 해당 Blob에서 삭제 취소를 호출합니다. 활성 및 일시 삭제된 Blob 모두에서 **삭제 취소 Blob**을 호출하면 활성으로 연결된 모든 일시 삭제된 스냅샷을 복원합니다. 다음 예제에서는 컨테이너의 모든 일시 삭제된 Blob 및 활성 Blob에서 삭제 취소를 호출합니다.

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
현재 일시 삭제 보존 정책을 찾으려면 다음 명령을 사용합니다.

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

일시 삭제를 활성화하려면 Blob 클라이언트의 서비스 속성을 업데이트합니다.

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

일시 삭제를 켰는지 확인하려면 다음 명령을 사용합니다. 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

일시 삭제를 활성화하려면 Blob 클라이언트의 서비스 속성을 업데이트합니다.

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

일시 삭제를 활성화하려면 Blob 클라이언트의 서비스 속성을 업데이트합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_EnableSoftDelete":::

실수로 삭제된 Blob을 복구하려면 해당 Blob에서 삭제 취소를 호출합니다. 활성 및 일시 삭제된 Blob 모두에서 **삭제 취소**를 호출하면 활성으로 연결된 모든 일시 삭제된 스냅샷을 복원합니다. 다음 예제에서는 컨테이너의 모든 일시 삭제된 Blob 및 활성 Blob에서 삭제 취소를 호출합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

특정 Blob 버전을 복구하려면 먼저 Blob에서 삭제 취소를 호출한 다음, Blob을 통해 원하는 스냅샷을 복사합니다. 다음 예제에서는 블록 Blob을 가장 최근에 생성된 스냅샷으로 복구합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobVersion":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

일시 삭제를 활성화하려면 Blob 클라이언트의 서비스 속성을 업데이트합니다.

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

실수로 삭제된 Blob을 복구하려면 해당 Blob에서 삭제 취소를 호출합니다. 활성 및 일시 삭제된 Blob 모두에서 **삭제 취소**를 호출하면 활성으로 연결된 모든 일시 삭제된 스냅샷을 복원합니다. 다음 예제에서는 컨테이너의 모든 일시 삭제된 Blob 및 활성 Blob에서 삭제 취소를 호출합니다.

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

특정 Blob 버전을 복구하려면 먼저 Blob에서 삭제 취소를 호출한 다음, Blob을 통해 원하는 스냅샷을 복사합니다. 다음 예제에서는 블록 Blob을 가장 최근에 생성된 스냅샷으로 복구합니다.

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

## <a name="next-steps"></a>다음 단계

- [Blob Storage에 대한 일시 삭제](soft-delete-overview.md)
- [Blob 버전 관리(미리 보기)](versioning-overview.md)