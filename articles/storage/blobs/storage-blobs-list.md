---
title: .NET-Azure Storage를 사용 하 여 blob 나열
description: .NET 클라이언트 라이브러리를 사용 하 여 Azure Storage 계정의 컨테이너에 있는 blob을 나열 하는 방법을 알아봅니다. 코드 예제에서는 플랫 목록에 blob을 나열 하는 방법을 보여 줍니다. 또는 디렉터리 또는 폴더로 구성 된 것 처럼 blob을 계층적으로 나열 하는 방법을 보여 줍니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137923"
---
# <a name="list-blobs-with-net"></a>.NET을 사용 하 여 blob 나열

코드에서 blob을 나열 하는 경우 Azure Storage에서 결과가 반환 되는 방식을 관리 하는 다양 한 옵션을 지정할 수 있습니다. 결과의 각 집합에서 반환할 결과 수를 지정 하 고 다음 집합을 검색할 수 있습니다. 이름이 해당 문자 또는 문자열로 시작 하는 blob을 반환 하는 접두사를 지정할 수 있습니다. 플랫 목록 구조 나 계층적으로 blob을 나열할 수 있습니다. 계층적 목록은 폴더로 구성 된 것 처럼 blob을 반환 합니다. 

이 문서에서는 [.net 용 Azure Storage 클라이언트 라이브러리](/dotnet/api/overview/azure/storage?view=azure-dotnet)를 사용 하 여 blob을 나열 하는 방법을 보여 줍니다.  

## <a name="understand-blob-listing-options"></a>Blob 목록 옵션 이해

저장소 계정의 blob을 나열 하려면 다음 메서드 중 하나를 호출 합니다.

- [CloudBlobClient Blob](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

컨테이너의 blob을 나열 하려면 다음 메서드 중 하나를 호출 합니다.

- [CloudBlobContainer Blob](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

이러한 메서드에 대 한 오버 로드는 목록 작업에서 blob을 반환 하는 방법을 관리 하기 위한 추가 옵션을 제공 합니다. 이러한 옵션은 다음 섹션에 설명 되어 있습니다.

### <a name="manage-how-many-results-are-returned"></a>반환 되는 결과 수 관리

기본적으로 목록 작업은 한 번에 최대 5000 개의 결과를 반환 합니다. 더 작은 결과 집합을 반환 하려면 **Listblobs** 메서드 중 하나를 호출할 때 `maxresults` 매개 변수에 대 한 0이 아닌 값을 제공 합니다.

목록 작업이 5000 개 이상의 blob을 반환 하거나 목록 작업이 저장소 계정에서 컨테이너의 하위 집합을 반환 하도록 `maxresults` 값을 지정한 경우 Azure Storage는 blob 목록과 함께 *연속 토큰* 을 반환 합니다. 연속 토큰은 Azure Storage에서 다음 결과 집합을 검색 하는 데 사용할 수 있는 불투명 값입니다.

코드에서 연속 토큰의 값을 확인 하 여 null 인지 여부를 확인 합니다. 연속 토큰이 null 이면 결과 집합이 완료 됩니다. 연속 토큰이 null이 아닌 경우 연속 토큰이 null이 될 때까지 다음 결과 집합을 검색 하기 위해 연속 토큰을 전달 하 여 목록 작업을 다시 호출 합니다.

### <a name="filter-results-with-a-prefix"></a>접두사를 사용 하 여 결과 필터링

컨테이너 목록을 필터링 하려면 `prefix` 매개 변수에 대 한 문자열을 지정 합니다. 접두사 문자열은 하나 이상의 문자를 포함할 수 있습니다. 그러면 Azure Storage는 이름이 해당 접두사로 시작 하는 blob만 반환 합니다.

### <a name="return-metadata"></a>메타 데이터 반환

결과를 사용 하 여 blob 메타 데이터를 반환 하려면 [Bloblistingdetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) 열거에 대 한 **메타 데이터** 값을 지정 합니다. Azure Storage에는 각 blob이 반환 된 메타 데이터가 포함 되어 있으므로이 컨텍스트에서 **Fetchattributes** 메서드 중 하나를 호출 하 여 blob 메타 데이터를 검색할 필요가 없습니다.

### <a name="flat-listing-versus-hierarchical-listing"></a>단순 목록 및 계층 구조 목록

Azure Storage blob은 계층적 패러다임 (예: 클래식 파일 시스템)이 아닌 플랫 패러다임으로 구성 됩니다. 그러나 폴더 구조를 모방 하기 위해 blob을 *가상 디렉터리로* 구성할 수 있습니다. 가상 디렉터리는 blob 이름의 일부를 형성 하며 구분 기호 문자로 표시 됩니다.

Blob을 가상 디렉터리로 구성 하려면 blob 이름에 구분 기호 문자를 사용 합니다. 기본 구분 기호 문자는 슬래시 (/) 이지만 구분 기호로 문자를 지정할 수 있습니다.

구분 기호를 사용 하 여 blob의 이름을 지정할 경우 blob을 계층적으로 나열 하도록 선택할 수 있습니다. 계층 구조 목록 작업의 경우 Azure Storage는 부모 개체 아래에 가상 디렉터리와 blob을 반환 합니다. 목록 작업을 재귀적으로 호출 하 여 계층 구조를 트래버스할 수 있습니다 .이는 프로그래밍 방식으로 클래식 파일 시스템을 트래버스하는 방법과 비슷합니다.

## <a name="use-a-flat-listing"></a>기본 목록 사용

기본적으로 목록 작업은 플랫 목록에 blob을 반환 합니다. 플랫 목록에서 blob은 가상 디렉터리로 구성 되지 않습니다.

다음 예에서는 지정 된 세그먼트 크기 (선택 사항)를 사용 하 여 지정 된 컨테이너에 있는 blob을 나열 하 고 blob 이름을 콘솔 창에 씁니다.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                // A flat listing operation returns only blobs, not virtual directories.
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

샘플 출력은 다음과 유사 합니다.

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>계층 구조 목록 사용

목록 작업을 계층적으로 호출 하는 경우 Azure Storage는 계층의 첫 번째 수준에서 가상 디렉터리 및 blob을 반환 합니다. 재귀 호출에 접두사를 전달 하 여 다음 디렉터리를 검색할 수 있도록 각 가상 디렉터리의 [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) 속성을 설정 합니다.

Blob을 계층적으로 나열 하려면 목록 메서드의 `useFlatBlobListing` 매개 변수를 **false**로 설정 합니다.

다음 예에서는 지정 된 세그먼트 크기 (선택 사항)를 사용 하 여 지정 된 컨테이너에 있는 blob을 나열 하 고 blob 이름을 콘솔 창에 씁니다.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

샘플 출력은 다음과 유사 합니다.

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> 계층 구조 목록 작업에는 Blob 스냅숏을 나열할 수 없습니다.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

- [Blob 나열](/rest/api/storageservices/list-blobs)
- [Blob 리소스 열거](/rest/api/storageservices/enumerating-blob-resources)
