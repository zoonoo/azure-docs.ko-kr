---
title: C#으로 Azure Search 인덱스에 데이터 로드(.NET SDK) - Azure Search
description: C# 샘플 코드 및 .NET SDK를 사용하여 Azure Search의 전체 텍스트 검색 가능 인덱스에 데이터를 업로드하는 방법을 알아봅니다.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286910"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>빠른 시작: 2 - C#을 사용하여 Azure Search 인덱스에 데이터 로드

이 문서에서는 C# 및 [.NET SDK](https://aka.ms/search-sdk)를 사용하여 [Azure Search 인덱스](search-what-is-an-index.md)로 데이터를 가져오는 방법을 보여줍니다. 인덱스로 문서 푸시는 다음 작업을 통해 수행됩니다.

> [!div class="checklist"]
> * 검색 인덱스에 연결할 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 개체를 만듭니다.
> * 추가, 수정 또는 삭제하려는 문서를 포함하는 [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 개체를 만듭니다.
> * `SearchIndexClient`에서 `Documents.Index` 메서드를 호출하여 인덱스에 문서를 업로드합니다.

## <a name="prerequisites"></a>필수 조건

["클라이언트 만들기"](search-create-index-dotnet.md#CreateSearchServiceClient)의 설명에 따라 [Azure Search 인덱스](search-create-index-dotnet.md) 및 `SearchServiceClient` 개체를 만듭니다.


## <a name="create-a-client"></a>클라이언트 만들기
데이터를 가져오려면 `SearchIndexClient` 클래스의 인스턴스가 필요합니다. 이미 만들어진 `SearchServiceClient` 인스턴스를 사용하는 방법을 포함하여 이 클래스를 만드는 여러 가지 방법이 있습니다. 

다음 예제에서 볼 수 있듯이, `SearchServiceClient` 인스턴스를 사용하고 `Indexes.GetClient` 메서드를 호출할 수 있습니다. 이 코드 조각은 `serviceClient`라는 `SearchServiceClient`에서 "호텔"이라는 인덱스의 `SearchIndexClient`를 가져옵니다.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient`에는 `Documents` 속성이 있습니다. 이 속성은 인덱스에서 문서를 추가, 수정, 삭제 또는 쿼리해야 하는 모든 메서드를 제공합니다.

> [!NOTE]
> 일반적인 검색 애플리케이션에서는 쿼리와 인덱싱이 개별적으로 처리됩니다. `Indexes.GetClient`는 `SearchCredentials` 같은 개체를 다시 사용할 수 있기 때문에 편리하기는 하지만, 보다 강력한 방법을 사용하려면 관리 키 대신 쿼리 키를 전달할 수 있도록 `SearchIndexClient`를 직접 만들어야 합니다. 이 방법은 [최소 권한의 원칙](https://en.wikipedia.org/wiki/Principle_of_least_privilege)과 일치하며 애플리케이션 보안을 강화하는 데 도움이 됩니다. 다음 연습에서는 `SearchIndexClient`를 만들겠습니다. 키에 대한 자세한 내용은 [Azure Search 서비스용 api 키 만들기 및 관리](search-security-api-keys.md)를 참조하세요.
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>IndexBatch 생성

.NET SDK를 사용하여 데이터를 가져오려면 데이터를 `IndexBatch` 개체에 패키징해야 합니다. `IndexBatch`는 `IndexAction` 개체의 컬렉션을 캡슐화하며, 각 개체는 Azure Search가 해당 문서에 대해 수행할 작업(업로드, 병합, 삭제 및 mergeOrUpload)을 알려주는 문서 및 속성을 포함하고 있습니다. 인덱싱 작업에 대한 자세한 내용은 [인덱싱 작업: 업로드, 병합, mergeOrUpload, 삭제](search-what-is-data-import.md#indexing-actions)를 참조하세요.

문서에 대해 어떤 작업을 수행해야 하는지 알고 나면 `IndexBatch`를 생성할 준비가 완료된 것입니다. 아래 예제에서는 몇 가지 다른 작업으로 배치를 만드는 방법을 보여줍니다. 이 예제에서는 "호텔" 인덱스의 문서에 매핑되는 `Hotel`이라는 사용자 지정 클래스를 사용합니다.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "1",
                BaseRate = 199.0,
                Description = "Best hotel in town",
                DescriptionFr = "Meilleur hôtel en ville",
                HotelName = "Fancy Stay",
                Category = "Luxury",
                Tags = new[] { "pool", "view", "wifi", "concierge" },
                ParkingIncluded = false,
                SmokingAllowed = false,
                LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                Rating = 5,
                Location = GeographyPoint.Create(47.678581, -122.131577)
            }),
        IndexAction.Upload(
            new Hotel()
            {
                HotelId = "2",
                BaseRate = 79.99,
                Description = "Cheapest hotel in town",
                DescriptionFr = "Hôtel le moins cher en ville",
                HotelName = "Roach Motel",
                Category = "Budget",
                Tags = new[] { "motel", "budget" },
                ParkingIncluded = true,
                SmokingAllowed = true,
                LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                Rating = 1,
                Location = GeographyPoint.Create(49.678581, -122.131577)
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

이 경우에 `IndexAction` 클래스에서 호출된 메서드에 지정된 대로 `Upload`, `MergeOrUpload` 및 `Delete`를 검색 작업으로 사용합니다.

이 예제 "호텔" 인덱스는 문서 수로 채워진다고 가정합니다. `MergeOrUpload`를 사용하는 경우 가능한 문서 필드를 모두 지정하지 않는 방법 및 `Delete`를 사용하는 경우 문서 키(`HotelId`)를 지정하는 방법을 참고합니다.

또한 단일 인덱싱 요청에서 최대 1000개의 문서를 포함할 수 있습니다.

> [!NOTE]
> 이 예제에서는 다른 문서에 다른 동작을 적용하고 있습니다. 배치에서 모든 문서에 `IndexBatch.New`를 호출하는 대신 동일한 작업을 수행하려는 경우 `IndexBatch`라는 기타 정적 메서드를 사용할 수 있습니다. 예를 들어 `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` 또는 `IndexBatch.Delete`를 호출하여 배치를 만들 수 있습니다. 이러한 메서드는 `IndexAction` 개체 대신 문서의 컬렉션을 가져옵니다(이 예제에서 `Hotel` 유형의 개체).
> 
> 

## <a name="call-documentsindex"></a>Documents.Index 호출
`IndexBatch` 개체를 초기화했다면 `SearchIndexClient` 개체에서 `Documents.Index`를 호출하여 인덱스로 보낼 수 있습니다. 다음 예제에서는 수행해야 하는 몇 가지 추가 단계 뿐만 아니라 `Index`를 호출하는 방법을 보여줍니다.

```csharp
try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}

Console.WriteLine("Waiting for documents to be indexed...\n");
Thread.Sleep(2000);
```

`Index` 메서드에 대한 호출과 관련된 `try`/`catch`를 참고합니다. catch 블록은 인덱싱에 대한 중요한 오류 사례를 다룹니다. Azure Search 서비스가 일괄 처리에서 문서 일부를 인덱싱하는데 실패하는 경우 `Documents.Index`에 의해 `IndexBatchException`이(가) 발생합니다. 이는 부하가 높은 상태에서 서비스되는 동안에 문서를 인덱싱하는 경우 발생할 수 있습니다. **이 경우 코드에서 명시적으로 처리하는 것이 좋습니다.** 실패한 문서 인덱싱을 잠시 후 다시 시도하거나, 샘플에서 하던 것처럼 기록하여 계속하거나, 애플리케이션의 데이터 일관성 요구 사항에 따라 다른 작업을 수행할 수 있습니다.

마지막으로 2초 동안 위의 예에서 코드가 지연됩니다. Azure Search 서비스에서 인덱싱이 비동기적으로 발생하기 때문에, 샘플 애플리케이션은 문서 검색을 위해 잠시 기다려야 합니다. 이와 같이 데모, 테스트, 샘플 애플리케이션에서는 일반적으로 지연만 필요합니다.

문서 처리에 대한 자세한 내용은 [".NET SDK가 문서를 처리하는 방법"](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Azure Search 인덱스를 채운 후에는 문서를 검색하는 쿼리를 실행해야 합니다. 

> [!div class="nextstepaction"]
> [C#으로 Azure Search 인덱스 쿼리](search-query-dotnet.md)
