<properties
	pageTitle="REST 호출을 사용하여 Azure 검색 쿼리 작성 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="Azure 검색에 검색 쿼리를 작성하고 .NET 라이브러리 또는 SDK를 사용하여 검색 결과를 필터링하고 정렬하고 패싯하는 검색 매개 변수를 사용합니다."
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

# REST 호출을 사용하여 Azure 검색의 쿼리 작성
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

이 문서는 [Azure 검색 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 사용하여 인덱스에 대한 쿼리를 생성하는 방법을 보여줍니다. 아래 콘텐츠 중 일부는 [문서 검색(Azure 검색 REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)에서 가져온 것입니다. 자세한 컨텍스트에 대해서는 부모 문서를 참조하세요.

가져오기에 대한 필수 구성 요소에는 검색 가능한 데이터를 제공하는 문서와 함께 제 위치에 이미 로드된 기존 인덱스가 포함시키는 것입니다.

REST API를 사용하여 인덱스를 검색하려면 GET HTTP 요청을 실행합니다. 쿼리 매개 변수는 HTTP 요청의 URL 내에서 정의됩니다.

**요청 및 요청 헤더**:

URL에서 서비스 이름, 인덱스 이름 뿐만 아니라 적절한 API 버전을 제공해야 합니다. URL의 끝의 쿼리 문자열은 쿼리 매개 변수를 제공하는 위치입니다. 쿼리 문자열의 매개 변수 중 하나는 적절한 API 버전이어야 합니다.(현재 API 버전은 이 문서를 게시할 때 "2015-02-28"임)

요청 헤더로 콘텐츠 형식을 정의하고 서비스의 기본 또는 보조 관리자 키를 제공해야 합니다.

	GET https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

Azure 검색은 매우 강력한 쿼리를 만드는 다양한 옵션을 제공합니다. 쿼리 문자열의 다른 매개 변수에 대해 자세히 알아보려면 [이 페이지](https://msdn.microsoft.com/library/azure/dn798927.aspx)를 방문합니다.

**예제:**

다양한 쿼리 문자열이 포함된 몇 가지 예는 다음과 같습니다. 이러한 샘플은 "호텔"이라는 더미 인덱스를 사용합니다.

"품질"이라는 용어에 대한 전체 인덱스를 검색합니다.

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=quality&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

전체 인덱스를 검색합니다.

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

전체 인덱스를 검색하고 특정 필드(lastRenovationDate)로 정렬합니다.

	GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key or secondary admin key]

성공적인 쿼리 요청은 "200 확인"이라는 상태 코드에서 발생하고 검색 결과는 JSON 형식의 응답 본문에  
서 찾을 수 있습니다. 자세한 내용은 "Response" 섹션의 [이 페이지](https://msdn.microsoft.com/library/azure/dn798927.aspx)를 방문합니다.

<!---HONumber=Nov15_HO4-->