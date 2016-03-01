<properties
	pageTitle=".NET을 사용하여 Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색.NET SDK 또는 라이브러리를 사용하여 코드에 인덱스를 만듭니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/18/2016"
	ms.author="heidist"/>

# .NET을 사용하여 Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

이 문서는 [Azure 검색.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하여 인덱스를 만드는 방법을 보여줍니다. 아래 콘텐츠는 [.NET 응용 프로그램에서 Azure 검색을 사용하는 방법](search-howto-dotnet-sdk.md)의 하위 집합입니다. 종단 간 단계에 대해서는 부모 문서를 참조하세요.

인덱스를 만드는 필수 구성 요소에는 일반적으로 `SearchServiceClient`을(를) 통해 이전에 설정된 검색 서비스 연결이 있습니다. 원활한 재배포를 보장하려면 이미 있는 동일한 이름의 기존 인덱스를 삭제하는 것이 가장 좋습니다.

'호텔'이라는 인덱스를 가정하면 이에 대한 메서드를 다음과 같이 생성할 수 있습니다

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

이 메서드는 주어진 `SearchServiceClient`을(를) 사용하여 인덱스가 존재하는지 확인하고 존재하면 이를 삭제합니다.

새 "호텔" 인덱스를 만들려면 다음과 유사한 메서드를 생성합니다.

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

이 메서드는 새 인덱스의 스키마를 정의하는 `Field` 개체 목록과 함께 새 `Index` 개체를 만듭니다. 각 필드에는 이름, 데이터 유형, 그리고 검색 동작을 정의하는 몇 가지 특성이 있습니다. 필드 외에도, 점수 매기기 프로필, 서제스터 또는 CORS 옵션을 인덱스에 추가할 수도 있습니다(이들 필드는 간단하게 나타내기 위해 샘플에서 생략됩니다). [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx)의 SDK 참조뿐만 아니라 [Azure 검색 REST API 참조](https://msdn.microsoft.com/library/azure/dn798935.aspx)에서 인덱스 개체와 그 구성 요소에 대한 자세한 정보를 찾을 수 있습니다.

<!---HONumber=AcomDC_0224_2016-->