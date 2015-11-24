<properties
	pageTitle="REST API를 사용하여 Azure 검색으로 데이터 가져오기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="REST API를 사용하여 Azure 검색에서 인덱스에 데이터를 업로드하는 방법"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# REST API를 사용하여 Azure 검색으로 데이터 가져오기
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스를 채우는 방법을 보여줍니다. 아래 콘텐츠 중 일부는 [문서 추가, 업데이트 또는 삭제(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798930.aspx)에서 가져온 것입니다. 자세한 컨텍스트에 대해서는 부모 문서를 참조하세요.

REST API를 사용하여 인덱스에 문서를 푸시하기 위해 서비스의 URL 끝점에 HTTP POST 요청을 발급합니다.

**요청 및 요청 헤더**:

URL에서 서비스 이름 뿐만 아니라 적절한 API 버전을 제공해야 합니다.(이 문서를 게시할 때 현재 API 버전은 "2015-02-28"임)

요청 헤더에서 콘텐츠 형식을 정의하고 서비스의 기본 관리자 키를 제공해야 합니다.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**요청 본문**:


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

이 경우 "업로드"를 검색 작업으로 사용합니다. 기존 문서를 업데이트 및 삭제하는 경우 "병합", "mergeOrUpload" 및 "삭제"를 사용할 수 있습니다.

인덱스를 업데이트할 때 성공한 경우 "200 확인"이라는 상태 코드를 받습니다. 요청에 하나 이상의 항목이 성공적으로 인덱싱되지 않은 경우 "207" 상태 코드를 받습니다.

문서 동작 및 성공/오류 응답에 대한 자세한 내용은 [이 페이지](https://msdn.microsoft.com/library/azure/dn798930.aspx)를 참조하세요.

<!---HONumber=Nov15_HO4-->