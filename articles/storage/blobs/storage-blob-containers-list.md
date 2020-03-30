---
title: .NET - Azure 저장소를 통해 Blob 컨테이너 목록
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정에 Blob 컨테이너를 나열하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 155b8f5d50c7b106daff8dab4df17200b844c988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135907"
---
# <a name="list-blob-containers-with-net"></a>.NET을 가진 Blob 컨테이너 를 나열합니다.

코드에서 Azure Storage 계정에 컨테이너를 나열할 때 Azure Storage에서 결과를 반환하는 방법을 관리하는 여러 옵션을 지정할 수 있습니다. 이 문서에서는 .NET 에 [대한 Azure Storage 클라이언트 라이브러리를](/dotnet/api/overview/azure/storage?view=azure-dotnet)사용하여 컨테이너를 나열하는 방법을 보여 주며.  

## <a name="understand-container-listing-options"></a>컨테이너 목록 옵션 이해

저장소 계정에 컨테이너를 나열하려면 다음 방법 중 하나를 호출하십시오.

- [목록 컨테이너분할](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [목록 컨테이너세분화Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

이러한 메서드의 오버로드는 목록 작업에서 컨테이너를 반환하는 방법을 관리하기 위한 추가 옵션을 제공합니다. 이러한 옵션은 다음 섹션에 설명되어 있습니다.

### <a name="manage-how-many-results-are-returned"></a>반환되는 결과 수 관리

기본적으로 목록 작업은 한 번에 최대 5000개의 결과를 반환합니다. 더 작은 결과 집합을 반환하려면 `maxresults` **ListContainerSegmented** 메서드 중 하나를 호출할 때 매개 변수에 대해 0이 아닌 값을 제공합니다.

저장소 계정에 5000개 이상의 컨테이너가 포함되어 있거나 목록 `maxresults` 작업이 저장소 계정의 컨테이너 하위 집합을 반환하도록 값을 지정한 경우 Azure Storage는 컨테이너 목록이 포함된 *연속 토큰을* 반환합니다. 연속 토큰은 Azure Storage에서 다음 결과 집합을 검색하는 데 사용할 수 있는 불투명 값입니다.

코드에서 연속 토큰의 값을 확인하여 null인지 확인합니다. 연속 토큰이 null이면 결과 집합이 완료됩니다. 연속 토큰이 null이 아닌 경우 연속 토큰이 null이 될 때까지 연속 토큰을 검색하기 위해 연속 토큰을 전달하여 **ListContainersSegmented ededAsync를** 다시 호출합니다. **ListContainersSegmentedAsync**

### <a name="filter-results-with-a-prefix"></a>접두사로 결과 필터링

컨테이너 목록을 필터링하려면 매개 변수에 `prefix` 대한 문자열을 지정합니다. 접두사 문자열에는 하나 이상의 문자가 포함될 수 있습니다. 그런 다음 Azure Storage는 해당 접두사로 이름이 시작되는 컨테이너만 반환합니다.

### <a name="return-metadata"></a>메타데이터 반환

결과와 함께 컨테이너 메타데이터를 반환하려면 [컨테이너 ListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) 열거형에 대한 **메타데이터** 값을 지정합니다. Azure Storage에는 반환된 각 컨테이너가 포함된 메타데이터가 포함되어 있으므로 컨테이너 메타데이터를 검색하기 위해 **FetchAttributes** 메서드 중 하나를 호출할 필요도 없습니다.

## <a name="example-list-containers"></a>예: 컨테이너 목록

다음 예제에서는 지정된 접두사로 시작하는 저장소 계정의 컨테이너를 비동기적으로 나열합니다. 이 예제에서는 한 번에 5개의 결과를 증분으로 컨테이너를 나열하고 연속 토큰을 사용하여 다음 결과 세그먼트를 가져옵니다. 또한 이 예제는 결과와 함께 컨테이너 메타데이터를 반환합니다.

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix)
{
    Console.WriteLine("List all containers beginning with prefix {0}, plus container metadata:", prefix);

    try
    {
        ContainerResultSegment resultSegment = null;
        BlobContinuationToken continuationToken = null;

        do
        {
            // List containers beginning with the specified prefix, returning segments of 5 results each.
            // Passing null for the maxResults parameter returns the max number of results (up to 5000).
            // Requesting the container's metadata with the listing operation populates the metadata,
            // so it's not necessary to also call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, 5, continuationToken, null, null);

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

            // Get the continuation token. If not null, get the next segment.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0} : {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>참조

[List Containers](/rest/api/storageservices/list-containers2)
[Blob 리소스를 나열하는](/rest/api/storageservices/enumerating-blob-resources) 컨테이너 목록
