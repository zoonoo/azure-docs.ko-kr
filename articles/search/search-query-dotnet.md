<properties
	pageTitle=".NET을 사용하여 Azure 검색 쿼리 작성 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색에 검색 쿼리를 작성하고 .NET 라이브러리 또는 SDK를 사용하여 검색 결과를 필터링하고 정렬하고 패싯하는 검색 매개 변수를 사용합니다."
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#.NET에서 Azure 검색의 쿼리 작성
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

이 문서는 [Azure 검색 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하여 쿼리를 작성하는 방법을 보여줍니다. 아래 콘텐츠는 [.NET 응용 프로그램에서 Azure 검색을 사용하는 방법](search-howto-dotnet-sdk.md)의 하위 집합입니다. 종단 간 단계에 대해서는 부모 문서를 참조하세요.

쿼리를 만드는 필수 구성 요소에는 일반적으로 `SearchServiceClient`을(를) 통해 이전에 설정된 검색 서비스 연결이 있습니다.

다음 코드 조각은 검색 문자열 입력을 SearchDocuments 메서드에 전달 하는 메서드를 만듭니다.

	private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
	{
		// Execute search based on search text and optional filter
		var sp = new SearchParameters();
	
		if (!String.IsNullOrEmpty(filter))
		{
			sp.Filter = filter;
		}
	
		DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
		foreach (SearchResult<Hotel> result in response)
		{
			Console.WriteLine(result.Document);
		}
	}
	
첫째, 이 메서드는 새로운 SearchParameters 개체를 만듭니다 이는 정렬, 필터링, 페이징, 패시팅 같은 쿼리에 대한 추가 옵션을 지정하는 데 사용됩니다. 이 예에서는 필터 속성만 설정합니다.

다음 단계를 실제로 검색 쿼리를 실행합니다. 이 작업은 Documents.Search 메서드를 사용하여 수행됩니다. 이 경우, 문자열로 사용할 검색 텍스트와 앞서 만든 검색 매개 변수를 전달합니다. 또한 Hotel을 Documents.Search의 유형 매개 변수로 지정하면 SDK가 검색 결과 내에 문서를 Hotel 유형의 개체로 역직렬화하도록 지정할 수 있습니다.

마지막으로, 이 메서드는 검색 결과에서 일치하는 모든 항목을 반복하여 각 문서를 콘솔에 인쇄합니다.

이 메서드를 호출하는 방법을 자세히 살펴보겠습니다.

	SearchDocuments(indexClient, searchText: "fancy wifi");
	SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

첫 번째 호출에서는 "멋진" 또는 "wifi"라는 쿼리 용어를 포함하는 모든 문서를 찾습니다. 두번째 호출에서는 검색 텍스트를 "모두 찾기"를 의미하는 "*"로 설정합니다. 검색 쿼리 식 구문에 대한 자세한 내용은 [Azure 검색의 간단한 쿼리 구문](https://msdn.microsoft.com/library/azure/dn798920.aspx)에서 찾을 수 있습니다.

두 번째 호출을 OData $filter 식, 범주 eq 'Luxury'를 사용합니다. 이는 범주 필드가 문자열 "Luxury"와 일치하는 문서만 반환하기 위해 검색을 제한합니다. [Azure 검색에 대한 OData 식 구문](https://msdn.microsoft.com/library/azure/dn798921.aspx)에서 OData 구문에 대해 자세히 확인할 수 있습니다.

<!---HONumber=AcomDC_1223_2015-->