---
title: .NET-Azure Storage를 사용 하 여 blob 컨테이너 나열
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정에 blob 컨테이너를 나열 하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ab7749c93f39d0c7b630b63e0b0e68589b61ede2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090950"
---
# <a name="list-blob-containers-with-net"></a>.NET을 사용 하 여 blob 컨테이너 나열

코드에서 Azure Storage 계정의 컨테이너를 나열 하는 경우 Azure Storage에서 결과가 반환 되는 방식을 관리 하는 다양 한 옵션을 지정할 수 있습니다. 이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage)를 사용 하 여 컨테이너를 나열 하는 방법을 보여 줍니다.  

## <a name="understand-container-listing-options"></a>컨테이너 목록 옵션 이해

저장소 계정에 컨테이너를 나열 하려면 다음 방법 중 하나를 호출 합니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

이러한 메서드에 대 한 오버 로드는 목록 작업에서 컨테이너를 반환 하는 방법을 관리 하기 위한 추가 옵션을 제공 합니다. 다음 섹션에서는 이러한 방법에 대해 설명합니다.

### <a name="manage-how-many-results-are-returned"></a>반환되는 결과 수 관리

기본적으로 목록 작업은 한 번에 최대 5,000개의 결과를 반환합니다. 더 작은 결과 집합을 반환 하려면 반환할 결과 페이지의 크기에 대 한 0이 아닌 값을 제공 합니다.

저장소 계정에 5000 개 이상의 컨테이너가 포함 된 경우 또는 목록 작업에서 저장소 계정의 컨테이너 하위 집합을 반환 하도록 페이지 크기를 지정한 경우 Azure Storage는 컨테이너 목록과 함께 *연속 토큰* 을 반환 합니다. 연속 토큰은 Azure Storage에서 다음 결과 세트를 검색하는 데 사용할 수 있는 불투명 값입니다.

코드에서 연속 토큰의 값을 확인 하 여이 값이 비어 있는지 (.NET v12의 경우) 또는 null (.NET v11 및 이전 버전의 경우) 인지 확인 합니다. 연속 토큰이 null이면 결과 세트가 완료된 것입니다. 연속 토큰이 null이 아닌 경우에는 연속 토큰이 null 일 때까지 다음 결과 집합을 검색 하기 위해 연속 토큰을 전달 하 여 목록 메서드를 다시 호출 합니다.

### <a name="filter-results-with-a-prefix"></a>접두사를 사용하여 결과 필터링

컨테이너 목록을 필터링하려면 `prefix` 매개 변수에 대한 문자열을 지정합니다. 접두사 문자열은 하나 이상의 문자를 포함할 수 있습니다. 그런 다음 Azure Storage은 해당 접두사로 시작 하는 이름을 가진 컨테이너만 반환 합니다.

### <a name="return-metadata"></a>메타데이터 반환

결과를 사용 하 여 컨테이너 메타 데이터를 반환 하려면 [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) 열거형 (.net v12의 경우) 또는 [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) enum (.net v11 및 이전 버전의 경우)에 대 한 **메타 데이터** 값을 지정 합니다. Azure Storage에는 각 컨테이너가 반환 된 메타 데이터가 포함 되어 있으므로 컨테이너 메타 데이터도 페치할 필요가 없습니다.

## <a name="example-list-containers"></a>예: 컨테이너 나열

다음 예제에서는 지정 된 접두사로 시작 하는 저장소 계정에서 컨테이너를 비동기적으로 나열 합니다. 이 예제에서는 지정 된 접두사로 시작 하 고 목록 작업에 대 한 호출 당 지정 된 수의 결과를 반환 하는 컨테이너를 나열 합니다. 그런 다음 연속 토큰을 사용 하 여 결과의 다음 세그먼트를 가져옵니다. 또한이 예제에서는 결과와 함께 컨테이너 메타 데이터를 반환 합니다.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>추가 정보

- [컨테이너 나열](/rest/api/storageservices/list-containers2)
- [Blob 리소스 열거](/rest/api/storageservices/enumerating-blob-resources)
