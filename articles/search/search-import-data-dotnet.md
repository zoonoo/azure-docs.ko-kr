<properties
	pageTitle=".NET을 사용하여 Azure 검색으로 데이터 가져오기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description=".NET SDK 또는.NET 라이브러리를 사용하여 Azure 검색에서 인덱스에 데이터를 업로드하는 방법"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# .NET을 사용하여 Azure 검색으로 데이터 가져오기
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

이 문서는 [Azure 검색.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하여 인덱스를 채우는 방법을 보여줍니다. 아래 콘텐츠는 [.NET 응용 프로그램에서 Azure 검색을 사용하는 방법](search-howto-dotnet-sdk.md)의 하위 집합입니다. 종단 간 단계에 대해서는 부모 문서를 참조하세요.

데이터를 인덱스로 가져오는 데 필수 구성 요소에는 이전에 만들어진 인덱스의 포함이 있습니다.

'호텔'이라는 인덱스를 가정하면 데이터 가져오기에 대한 메서드를 다음과 같이 생성할 수 있습니다

`Main`의 다음 단계는 새로 만든 인덱스를 채우는 것입니다. 이것은 다음 메서드로 수행됩니다.

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(sitecoreItems);
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

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

이 메서드는 네 부분으로 이루어져 있습니다. 첫 번째 부분은 입력 데이터를 인덱스에 업로드할 때 사용되는 다양한 `Hotel` 개체를 만듭니다. 이 데이터는 간단히 하기 위해 하드 코딩합니다. 사용 중인 응용 프로그램의 경우, 데이터는 SQL 데이터베이스와 같은 외부 데이터 원본에서 나올 가능성이 높습니다.

두 번째 부분은 각 `Hotel`에 대해 `IndexAction`을(를) 만든 다음, 이를 새로운 `IndexBatch`와(과) 함께 그룹화합니다. 그런 다음 `Documents.Index` 메서드를 통해 Azure 검색 인덱스에 일괄적으로 업로드합니다.

> [AZURE.NOTE] 이 예에서는 문서를 업로드하는 것입니다. 변경 사항을 기존 문서에 병합하거나 문서를 삭제하려는 경우, 해당하는 `Merge`, `MergeOrUpload` 또는 `Delete` 메서드를 사용할 수 있습니다.

이 메서드의 세 번째 부분은 인덱싱에 중요한 오류 사례를 처리하는 catch 블록입니다. Azure 검색 서비스가 일괄 처리에서 문서 일부를 인덱싱하는데 실패하는 경우 `Documents.Index`에 의해 `IndexBatchException`이(가) 발생합니다. 이는 부하가 높은 상태에서 서비스 되는 동안에 문서를 인덱싱는 경우 발생할 수 있습니다. **이 경우 코드에서 명시적으로 처리하는 것이 좋습니다.** 실패한 문서 인덱싱을 잠시 후 다시 시도하거나, 샘플에서 하던 것처럼 기록하여 계속하거나, 응용 프로그램의 데이터 일관성 요구 사항에 따라 다른 작업을 수행할 수 있습니다.

마지막으로, 메서드가 2초 동안 지연됩니다. Azure 검색 서비스에서 인덱싱이 비동기적으로 발생하기 때문에, 샘플 응용 프로그램은 문서 검색을 위해 잠시 기다려야 합니다. 이와 같이 데모, 테스트, 샘플 응용 프로그램에서는 일반적으로 지연만 필요합니다.

<!---HONumber=AcomDC_0224_2016-->