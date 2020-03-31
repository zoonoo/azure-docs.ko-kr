---
title: .NET - Azure 저장소를 사용하면 Blob 복사
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정에서 Blob을 복사하는 방법을 알아봅니다.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/20/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 9ffa69980f020580376aea447f40ac615f26cf03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135890"
---
# <a name="copy-a-blob-with-net"></a>.NET을 사용하면 Blob 복사

이 문서에서는 Azure Storage 계정으로 Blob을 복사하는 방법을 보여 줍니다. 또한 비동기 복사 작업을 중단하는 방법도 보여 주며, 이 작업을 중단하는 방법도 보여 주어 있습니다. 예제 코드는 [.NET 에 대한 Azure Storage 클라이언트 라이브러리를 사용합니다.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-copying-blobs"></a>Blob 복사 에 대해

동일한 저장소 계정 내에서 Blob을 복사하면 동기 작업입니다. 계정 간에 복사하면 비동기 작업입니다. [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) 및 [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) 메서드는 상태를 확인하거나 복사 작업을 중단하는 데 사용되는 복사 ID 값을 반환합니다.

복사 작업의 소스 Blob은 블록 Blob, 추가 Blob, 페이지 Blob 또는 스냅숏일 수 있습니다. 대상 blob가 이미 있으면 원본 blob와 동일한 blob 유형이어야 합니다. 기존 대상 blob는 덮어씁니다. 

복사 작업이 진행되는 동안에는 대상 Blob을 수정할 수 없습니다. 대상 blob는 처리되지 않은 blob 복사 작업을 하나만 포함할 수 있습니다. 즉, Blob은 보류 중인 여러 복사 작업의 대상이 될 수 없습니다.

전체 소스 Blob 또는 파일은 항상 복사됩니다. 바이트 또는 블록 집합의 범위를 복사하는 것은 지원되지 않습니다.

Blob이 복사되면 시스템 속성이 동일한 값을 가진 대상 Blob에 복사됩니다.

모든 Blob 형식에 대해 대상 Blob의 [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet) 속성을 확인하여 복사 작업 의 상태를 얻을 수 있습니다. 복사가 완료되면 최종 blob가 커밋됩니다.

복사 작업은 다음 양식 중 어느 한 가지를 수행할 수 있습니다.

  - 다른 이름을 사용해서 원본 blob를 대상 blob에 복사할 수 있습니다. 대상 Blob은 동일한 Blob 유형(블록, 추가 또는 페이지)의 기존 Blob이거나 복사 작업에서 만든 새 Blob일 수 있습니다.
  - 원본 Blob을 동일한 이름의 대상 Blob에 복사하여 대상 Blob을 효과적으로 대체할 수 있습니다. 이러한 복사 작업은 커밋되지 않은 블록을 제거하고 대상 Blob의 메타데이터를 덮어씁니다.
  - Azure File 서비스의 원본 파일을 대상 Blob에 복사할 수 있습니다. 대상 Blob은 기존 블록 Blob이거나 복사 작업에서 만든 새 블록 Blob일 수 있습니다. 파일에서 페이지 Blob 또는 부가 형 Blob로 복사하는 것은 지원되지 않습니다.
  - 기본 Blob에 대해 스냅샷을 복사할 수 있습니다. 스냅샷의 수준을 기본 Blob 위치로 올리면 이전 Blob 버전을 복원할 수 있습니다.
  - 스냅샷을 다른 이름으로 대상 Blob에 복사할 수 있습니다. 복사된 대상 Blob는 쓰기 가능한 Blob이고 스냅숏이 아닙니다.

## <a name="copy-a-blob"></a>Blob 복사

Blob을 복사하려면 다음 방법 중 하나를 호출합니다.

 - [시작 복사](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet)
 - [시작복사동기화](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet)

다음 코드 예제에서는 이전에 만든 Blob에 대한 참조를 받고 동일한 컨테이너의 새 Blob에 복사합니다.

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
            Console.WriteLine();
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

## <a name="abort-a-blob-copy-operation"></a>Blob 복사 작업 중단

복사 작업을 중단하면 블록 Blob, Blob 및 페이지 Blob에 대해 0 길이의 대상 Blob이 생성됩니다. 그러나 대상 Blob의 메타데이터에는 원본 Blob에서 복사하거나 [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy?view=azure-dotnet) 또는 [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync?view=azure-dotnet) 호출에서 명시적으로 설정된 새 값이 있습니다. 원본 메타데이터를 복사 하기 전에 유지 하려면 호출 `StartCopy` 하기 전에 `StartCopyAsync`대상 Blob의 스냅숏을 만들거나 .

진행 중인 Blob 복사 작업을 중단하면 대상 Blob의 [CopyState.상태가](/dotnet/api/microsoft.azure.storage.blob.copystate.status?view=azure-dotnet#Microsoft_Azure_Storage_Blob_CopyState_Status) [CopyStatus.Aborted로](/dotnet/api/microsoft.azure.storage.blob.copystatus?view=azure-dotnet)설정됩니다.

[AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy?view=azure-dotnet) 및 [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync?view=azure-dotnet) 메서드는 진행 중인 Blob 복사 작업을 취소하고 길이가 0이고 전체 메타데이터가 있는 대상 Blob을 남깁니다.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

다음 항목에는 Azure REST API를 사용하여 Blob 을 복사하고 진행 중인 복사 작업을 중단하는 것에 대한 정보가 포함되어 있습니다.

- [복사 Blob](/rest/api/storageservices/copy-blob)
- [Blob 복사 중단](/rest/api/storageservices/abort-copy-blob)
