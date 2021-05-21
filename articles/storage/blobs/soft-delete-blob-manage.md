---
title: 일시 삭제된 Blob 관리 및 복원
titleSuffix: Azure Storage
description: Azure Portal 또는 Azure Storage 클라이언트 라이브러리를 사용하여 일시 삭제된 Blob 및 스냅샷을 관리하고 복원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d5ef85d947ae999fd94ba5a6e9cdb00baec9786
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555897"
---
# <a name="manage-and-restore-soft-deleted-blobs"></a>일시 삭제된 Blob 관리 및 복원

Blob 일시 삭제는 지정된 기간 동안 시스템에서 삭제된 데이터를 유지하여 개별 Blob 및 해당 버전, 스냅샷 및 메타데이터를 실수로 삭제하거나 덮어쓰지 않도록 보호합니다. 보존 기간 중에는 삭제 시 Blob을 해당 상태로 복원할 수 있습니다. 보존 기간이 만료되면 Blob이 영구적으로 삭제됩니다. Blob 일시 삭제에 대한 자세한 내용은 [Blob 일시 삭제](soft-delete-blob-overview.md)를 참조하세요.

Blob 일시 삭제는 Blob 데이터에 대한 포괄적인 데이터 보호 전략의 일환입니다. Microsoft의 데이터 보호 권장 사항에 대해 자세히 알아보려면 [데이터 보호 개요](data-protection-overview.md)를 참조하세요.

## <a name="manage-soft-deleted-blobs-with-the-azure-portal"></a>Azure Portal을 사용하여 일시 삭제된 Blob 관리

Azure Portal을 사용하여 일시 삭제된 Blob 및 스냅샷을 보고 복원할 수 있습니다.

### <a name="view-deleted-blobs"></a>삭제된 Blob 보기

Blob이 일시 삭제되면 기본적으로 Azure Portal에 표시되지 않습니다. 일시 삭제된 Blob을 보려면 컨테이너의 **개요** 페이지로 이동하고 **삭제된 Blob 표시** 설정을 토글합니다. 일시 삭제된 Blob는 **삭제됨** 상태로 표시됩니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blobs-list-portal.png" alt-text="Azure Portal에서 일시 삭제된 Blob을 나열하는 방법을 보여 주는 스크린샷":::

그런 다음, Blob 목록에서 삭제된 Blob을 선택하 여 해당 속성을 표시합니다. **개요** 탭 아래에서 Blob의 상태가 **삭제됨** 으로 설정되어 있는지 확인합니다. 또한 포털은 Blob이 영구적으로 삭제될 때까지 남은 일 수를 표시합니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-properties-portal.png" alt-text="Azure Portal에서 일시 삭제된 Blob의 속성을 보여주는 스크린샷":::

### <a name="view-deleted-snapshots"></a>삭제된 스냅샷 표시

Blob을 삭제하면 Blob과 연결된 모든 스냅샷은 삭제됩니다. 일시 삭제된 Blob에 스냅샷이 있으면 삭제된 스냅샷이 포털에 표시될 수도 있습니다. 일시 삭제된 Blob의 속성을 표시한 다음, **스냅샷** 탭으로 이동하고 **삭제된 스냅샷 표시** 를 토글합니다.

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-snapshots-portal.png" alt-text="예를 보여 주는 스크린샷":::

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>버전 관리를 사용하지 않는 경우 일시 삭제된 개체 복원하기

Blob 버전 관리를 사용하지 않는 경우에 Azure Portal에서 일시 삭제된 Blob을 복원하려면, 먼저 Blob의 속성을 표시한 다음, **개요** 탭에서 **삭제 취소** 단추를 선택합니다. 또한 Blob을 복원하면 일시 삭제 보존 기간 동안 삭제된 모든 스냅샷이 복원됩니다.

:::image type="content" source="media/soft-delete-blob-manage/undelete-soft-deleted-blob-portal.png" alt-text="Azure Portal에서 일시 삭제된 Blob을 복원하는 방법을 보여 주는 스크린샷":::

일시 삭제된 스냅샷을 기본 Blob으로 승격하려면, 먼저 Blob의 일시 삭제된 스냅샷이 복원되었는지 확인합니다. 기본 Blob 자체가 일시 삭제되지 않은 경우에도 Blob의 일시 삭제된 스냅샷을 복원하려면 **삭제 취소** 단추를 선택합니다. 그런 다음, 승격할 스냅샷을 선택하고 **스냅샷 승격** 단추를 사용하여 기본 Blob을 스냅샷의 콘텐츠로 덮어씁니다.

:::image type="content" source="media/soft-delete-blob-manage/promote-snapshot.png" alt-text="스냅샷을 기본 Blob로 승격하는 방법을 보여 주는 스크린샷":::

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>버전 관리를 사용하는 경우 일시 삭제된 Blob 복원하기

버전 관리를 사용하는 경우 Azure Portal에서 일시 삭제된 Blob을 복원하려면, 일시 삭제된 blob을 선택하여 해당 속성을 표시한 다음, **버전** 탭을 선택합니다. 현재 버전으로 승격하려는 버전을 선택하고 **현재 버전 만들기** 를 선택합니다.  

:::image type="content" source="media/soft-delete-blob-manage/soft-deleted-blob-promote-version-portal.png" alt-text="Azure Portal에서 Blob을 복원하기 위해 버전을 승격하는 방법을 보여 주는 스크린샷":::

버전 관리를 사용하는 경우 삭제된 버전이나 스냅샷을 복원하려면 Blob의 속성을 표시한 다음, **개요** 탭에서 **삭제 취소** 단추를 선택합니다.

> [!NOTE]
> 버전 관리를 사용하는 경우 삭제된 Blob에서 **삭제 취소** 단추를 선택하면 일시 삭제된 버전이나 스냅샷이 복원되지만 기본 Blob은 복원되지 않습니다. 기본 Blob을 복원하려면 이전 버전을 승격해야 합니다.

## <a name="manage-soft-deleted-blobs-with-code"></a>코드를 사용하여 일시 삭제된 Blob 관리

Azure Storage 클라이언트 라이브러리를 사용하여 일시 삭제된 Blob 또는 스냅샷을 복원할 수 있습니다. 다음 예제에서는 .NET 클라이언트 라이브러리 사용 방법을 보여 줍니다.

### <a name="restore-soft-deleted-objects-when-versioning-is-disabled"></a>버전 관리를 사용하지 않는 경우 일시 삭제된 개체 복원하기

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

버전 관리를 사용하지 않을 때 삭제된 Blob을 복원하려면 해당 Blob에 대한 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출합니다. **Blob 삭제 취소** 작업은 일시 삭제된 Blob 및 해당 Blob와 연결된 삭제된 스냅샷을 복원합니다.

삭제되지 않은 Blob에 대한 **Blob 삭제 취소** 호출은 효과가 없습니다. 다음 예제에서는 컨테이너의 모든 Blob에 대한 **Blob 삭제 취소** 를 호출하고 일시 삭제된 Blob 및 해당 스냅샷을 복원합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverDeletedBlobs":::

특정 버전을 복원하려면 먼저 기본 Blob 또는 버전에서 **Blob 삭제 취소** 작업을 호출한 다음, 기본 Blob을 통해 원하는 버전을 복사합니다. 다음 예제에서는 블록 Blob을 가장 최근에 저장된 버전으로 복원합니다.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RecoverSpecificBlobSnapshot":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

버전 관리를 사용하지 않을 때 삭제된 Blob을 복원하려면 해당 Blob에 대한 [Blob 삭제 취소](/rest/api/storageservices/undelete-blob) 작업을 호출합니다. **Blob 삭제 취소** 작업은 일시 삭제된 Blob 및 해당 Blob와 연결된 삭제된 스냅샷을 복원합니다.

삭제되지 않은 Blob에 대한 **Blob 삭제 취소** 호출은 효과가 없습니다. 다음 예제에서는 컨테이너의 모든 Blob에 대한 **Blob 삭제 취소** 를 호출하고 일시 삭제된 Blob 및 해당 스냅샷을 복원합니다.

```csharp
// Restore all blobs in a container.
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

특정 스냅샷을 복원하려면 먼저 기본 Blob 또는 버전에서 **Blob 삭제 취소** 작업을 호출한 다음, 기본 Blob을 통해 원하는 스냅샷을 복사합니다. 다음 예제에서는 블록 Blob을 가장 최근에 생성된 스냅샷으로 복원합니다.

```csharp
// Restore the block blob.
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container, prefixed by the blob name.
IEnumerable<IListBlobItem> allBlobSnapshots = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Copy the most recently generated snapshot to the base blob.
CloudBlockBlob copySource = allBlobSnapshots.First(snapshot => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)snapshot).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```  

---

### <a name="restore-soft-deleted-blobs-when-versioning-is-enabled"></a>버전 관리를 사용하는 경우 일시 삭제된 Blob 복원하기

버전 관리를 사용하는 경우 일시 삭제된 Blob을 복원하려면 [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업을 사용하여 기본 Blob을 통해 이전 버전을 복사합니다.  

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/DataProtection.cs" id="Snippet_RestorePreviousVersion":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

해당 사항 없음 Blob 버전 관리는 Azure Storage 클라이언트 라이브러리 버전 12.x 이상에서만 지원됩니다.

---

## <a name="next-steps"></a>다음 단계

- [Blob Storage에 대한 일시 삭제](./soft-delete-blob-overview.md)
- [Blob에 일시 삭제를 사용하도록 설정](soft-delete-blob-enable.md)
- [Blob 버전 관리](versioning-overview.md)
