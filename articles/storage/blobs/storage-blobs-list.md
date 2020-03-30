---
title: .NET - Azure 저장소를 가진 Blob 목록
description: .NET 클라이언트 라이브러리를 사용하여 Azure Storage 계정의 컨테이너에 Blob을 나열하는 방법을 알아봅니다. 코드 예제에서는 플랫 리스팅에 Blob을 나열하는 방법 또는 디렉터리 또는 폴더로 구성된 것처럼 Blob을 계층적으로 나열하는 방법을 보여 주습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: eb62883859a3efeb1c05deb38d8a40fba76e9cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137923"
---
# <a name="list-blobs-with-net"></a>.NET을 가진 Blob 을 나열합니다.

코드에서 Blob을 나열할 때 Azure Storage에서 결과를 반환하는 방법을 관리하는 여러 옵션을 지정할 수 있습니다. 각 결과 집합에서 반환할 결과 수를 지정한 다음 후속 집합을 검색할 수 있습니다. 이름이 해당 문자 또는 문자열로 시작하는 Blob을 반환하는 접두사를 지정할 수 있습니다. 또한 플랫 리스팅 구조또는 계층적으로 Blob을 나열할 수 있습니다. 계층적 목록은 폴더로 구성된 것처럼 Blob을 반환합니다. 

이 문서에서는 .NET 에 [대한 Azure Storage 클라이언트 라이브러리를](/dotnet/api/overview/azure/storage?view=azure-dotnet)사용하여 Blob을 나열하는 방법을 보여 주며 있습니다.  

## <a name="understand-blob-listing-options"></a>Blob 목록 이해

저장소 계정에 Blob을 나열하려면 다음 방법 중 하나를 호출합니다.

- [클라우드블렙클라이언트.리스트블럼스](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [클라우드블렙클라이언트.리스트블럭스세분화](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [클라우드블렙클라이언트.리스트블럭스세그먼트Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

컨테이너에 Blob을 나열하려면 다음 방법 중 하나를 호출합니다.

- [클라우드블렙 컨테이너.리스트블럼](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [클라우드블렙 컨테이너.리스트블럭스세분화](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [클라우드블렙 컨테이너.리스트블럭스세그먼트Async](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

이러한 메서드의 오버로드는 목록 작업에서 Blob을 반환하는 방법을 관리하기 위한 추가 옵션을 제공합니다. 이러한 옵션은 다음 섹션에 설명되어 있습니다.

### <a name="manage-how-many-results-are-returned"></a>반환되는 결과 수 관리

기본적으로 목록 작업은 한 번에 최대 5000개의 결과를 반환합니다. 더 작은 결과 집합을 반환하려면 `maxresults` **ListBlobs** 메서드 중 하나를 호출할 때 매개 변수에 대해 0이 아닌 값을 제공합니다.

목록 작업이 5000개 이상의 Blob을 반환하거나 목록 작업이 `maxresults` 저장소 계정의 컨테이너 하위 집합을 반환하도록 값을 지정한 경우 Azure Storage는 Blob 목록과 함께 *연속 토큰을* 반환합니다. 연속 토큰은 Azure Storage에서 다음 결과 집합을 검색하는 데 사용할 수 있는 불투명 값입니다.

코드에서 연속 토큰의 값을 확인하여 null인지 확인합니다. 연속 토큰이 null이면 결과 집합이 완료됩니다. 연속 토큰이 null이 아닌 경우 연속 토큰이 null이 될 때까지 연속 토큰을 검색하기 위해 연속 토큰을 다시 호출합니다.

### <a name="filter-results-with-a-prefix"></a>접두사로 결과 필터링

컨테이너 목록을 필터링하려면 매개 변수에 `prefix` 대한 문자열을 지정합니다. 접두사 문자열에는 하나 이상의 문자가 포함될 수 있습니다. 그런 다음 Azure Storage는 해당 접두사로 이름이 시작되는 Blob만 반환합니다.

### <a name="return-metadata"></a>메타데이터 반환

결과와 함께 Blob 메타데이터를 반환하려면 [BlobListingDetails세부 정보](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) 열거형에 대한 **메타데이터** 값을 지정합니다. Azure Storage에는 반환된 각 Blob이 포함된 메타데이터가 포함되어 있으므로 이 컨텍스트에서 **FetchAttributes** 메서드 중 하나를 호출하여 Blob 메타데이터를 검색할 필요가 없습니다.

### <a name="flat-listing-versus-hierarchical-listing"></a>플랫 리스팅 대 계층적 리스팅

Azure Storage의 Blob은 계층적 패러다임(예: 클래식 파일 시스템)이 아닌 평면 패러다임으로 구성됩니다. 그러나 폴더 구조를 모방하기 위해 Blob을 *가상 디렉터리로* 구성할 수 있습니다. 가상 디렉터리 Blob 이름의 일부를 구성 하 고 구분 기호 문자로 표시 됩니다.

Blob을 가상 디렉터리로 구성하려면 Blob 이름에 구분 기호 문자를 사용합니다. 기본 구분 기호 문자는 정방향 슬래시(/)이지만 모든 문자를 구분 기호로 지정할 수 있습니다.

구분 기호를 사용하여 Blob의 이름을 지정하는 경우 계층적으로 Blob을 나열하도록 선택할 수 있습니다. 계층적 목록 작업의 경우 Azure Storage는 상위 개체 아래에 있는 모든 가상 디렉터리 및 Blob을 반환합니다. 목록 작업을 재귀적으로 호출하여 클래식 파일 시스템을 프로그래밍 방식으로 통과하는 방법과 유사하게 계층 구조를 통과할 수 있습니다.

## <a name="use-a-flat-listing"></a>플랫 리스팅 사용

기본적으로 목록 작업은 플랫 리스팅에서 Blob을 반환합니다. 플랫 리스팅에서 Blob은 가상 디렉터리별로 구성되지 않습니다.

다음 예제에서는 플랫 목록을 사용하여 지정된 컨테이너의 Blob을 나열하고 선택적 세그먼트 크기를 지정하고 Blob 이름을 콘솔 창에 씁니다.

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

샘플 출력은 다음과 유사합니다.

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

## <a name="use-a-hierarchical-listing"></a>계층적 목록 사용

목록 작업을 계층적으로 호출하면 Azure Storage는 계층구조의 첫 번째 수준에서 가상 디렉터리 및 Blob을 반환합니다. 각 가상 디렉터리의 [접두사](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) 속성이 설정되어 재귀 호출에서 접두사를 전달하여 다음 디렉터리를 검색할 수 있습니다.

Blob을 계층적으로 나열하려면 목록 `useFlatBlobListing` 메서드의 매개 변수를 **false로**설정합니다.

다음 예제에서는 플랫 목록을 사용하여 지정된 컨테이너의 Blob을 나열하고 선택적 세그먼트 크기를 지정하고 Blob 이름을 콘솔 창에 씁니다.

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

샘플 출력은 다음과 유사합니다.

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
> Blob 스냅숏은 계층적 목록 작업에 나열할 수 없습니다.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>다음 단계

- [Blob 나열](/rest/api/storageservices/list-blobs)
- [Blob 리소스 열거](/rest/api/storageservices/enumerating-blob-resources)
