<properties
	pageTitle="REST API를 사용하여 Azure 검색 인덱스 만들기 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색 및 HTTP REST API를 사용하여 코드에 인덱스를 만듭니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# REST API를 사용하여 Azure 검색 인덱스 만들기
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스를 만드는 방법을 보여줍니다. 아래 콘텐츠 중 일부는 [인덱스 만들기(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798941.aspx)에서 가져온 것입니다. 자세한 컨텍스트에 대해서는 부모 문서를 참조하세요.

인덱스를 만드는 필수 구성 요소에는 일반적으로 'httpClient'를 통해 이전에 설정된 검색 서비스 연결이 있습니다.

## 인덱스 스키마 정의

REST API를 사용하여 인덱스를 만들려면 Azure 검색 URL 끝점에 게시 요청을 발행합니다. 요청 본문에 JSON을 사용하여 인덱스의 구조를 정의합니다.

**요청 및 요청 헤더**:

아래 샘플에서는 서비스의 URL 끝점을 사용해야 하며 특히 서비스 이름 및 적절한 API 버전에 유의합니다.(이 문서를 게시할 때 현재 API 버전은 "2015-02-28"임) 또한 요청 헤더에서 [서비스를 만들](https://msdn.microsoft.com/library/azure/dn798941.aspx/) 때 받은 서비스의 기본 관리 키를 제공해야 합니다.

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**요청 본문**:

인덱스 이름(이 경우: "호텔") 뿐만 아니라 [필드의 이름, 형식 및 특성](https://msdn.microsoft.com/library/azure/dn798941.aspx)을 식별하는 위치입니다.

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

성공적인 요청의 경우 상태 코드 "201 생성됨"이 표시되어야 합니다. REST API를 통해 인덱스를 만드는 방법에 대한 자세한 내용은 [이 페이지](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 방문합니다.

<!---HONumber=Nov15_HO4-->