<properties
	pageTitle="Fiddler를 사용하여 Azure 검색 REST API를 평가 및 테스트하는 방법 | Microsoft Azure | 호스트된 클라우드 검색 서비스"
	description="코드를 작성할 필요가 없는 Fiddler를 사용하여 Azure 검색 가용성을 확인하고 REST API를 사용해 봅니다."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="12/18/2015"
	ms.author="heidist"/>

# Fiddler를 사용하여 Azure 검색 REST API를 평가 및 테스트
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

이 문서에서는 [Telerik에서 무료로 다운로드](http://www.telerik.com/fiddler)할 수 있는 Fiddler를 사용하여 HTTP 요청을 실행하고 코드를 작성할 필요 없이 Azure 검색 REST API를 사용하여 응답을 확인하는 방법을 설명합니다. Azure 검색은 Microsoft Azure에서 완벽하게 관리되는 호스트된 클라우드 검색 서비스이며 .NET 및 REST API를 통해 쉽게 프로그래밍 가능합니다. Azure 검색 서비스 REST API는 [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)에 설명되어 있습니다.

아래 단계에서는 인덱스를 만들고 문서를 업로드한 다음 인덱스를 쿼리하고 시스템에 서비스 정보를 쿼리합니다.

이러한 단계를 완료하려면 Azure 검색 서비스 및 `api-key`가 필요합니다. 시작하는 방법에 대한 지침은 [포털에서 Azure 검색 서비스 만들기](search-create-service-portal.md)를 참조하세요.

## 인덱스 만들기

1. Fiddler를 시작합니다. **파일** 메뉴에서 **트래픽 캡쳐**를 해제하여 현재 작업과 관련 없는 HTTP 활동을 숨깁니다.

3. **작성기** 탭에서 다음 스크린샷과 같은 요청을 작성합니다.

  	![][1]

2. **PUT**을 선택합니다.

3. 서비스 URL을 지정하는 URL, 요청 특성 및 api-version을 입력합니다. 다음 몇 가지 사항에 주의하세요.
   + HTTPS를 접두사로 사용합니다.
   + 요청 특성은 "/indexes/hotels"입니다. 그러면 검색에서 'hotels'라는 인덱스를 만듭니다.
   + api-version은 소문자이며 "?api-version=2015-02-28"로 지정됩니다. Azure 검색은 주기적으로 업데이트를 배포하므로 API 버전이 중요합니다. 드물긴 하지만 서비스 업데이트에서 새로운 API 변경 사항을 소개할 수도 있습니다. 이러한 이유로 Azure 검색은 각 요청에 API 버전이 필요하므로 어떤 것을 사용할지 전체를 제어합니다.

    다음 예제와 같은 전체 URL이 표시됩니다.

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	호스트와 api-key를 해당 서비스에 유효한 값으로 바꾸어 요청 헤더를 지정합니다.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	요청 본문에 인덱스 정의를 구성하는 필드를 붙여 넣습니다.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

6.	**실행**을 클릭합니다.

몇 초 후에 인덱스가 성공적으로 만들어졌음을 나타내는 HTTP 201 응답이 검색 목록에 표시됩니다.

HTTP 504가 표시될 경우 URL이 HTTPS를 지정하는지 확인합니다. HTTP 400 또는 404가 표시되는 경우 요청 본문에서 복사/붙여 넣기 오류가 없는지 확인합니다. HTTP 403은 대개 api-key에 문제가 있음을 나타냅니다(잘못된 키 또는 api-key 지정 방법과 관련된 구문 문제).

## 문서 로드

**작성기** 탭에서 문서 게시 요청은 다음과 같이 표시됩니다. 요청 본문에 4개 호텔에 대한 검색 데이터가 포함됩니다.

   ![][2]

1. **POST**를 선택합니다.

2.	HTTPS, 서비스 URL, "/indexes/<'indexname'>/docs/index?api-version=2015-02-28" 순으로 URL을 입력합니다. 다음 예제와 같은 전체 URL이 표시됩니다.

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	요청 헤더는 이전과 동일해야 합니다. 호스트와 api-key를 해당 서비스에 유효한 값으로 바꾼 것에 주의하세요.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	요청 본문에 호텔 인덱스에 추가될 문서 4개가 포함됩니다.

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
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
        	"hotelName": "Roach Motel",
        	"category": "Budget",
        	"tags": ["motel", "budget"],
        	"parkingIncluded": true,
        	"smokingAllowed": true,
        	"lastRenovationDate": "1982-04-28T00:00:00Z",
        	"rating": 1,
        	"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	**실행**을 클릭합니다.

몇 초 후에 HTTP 200 응답이 검색 목록에 표시됩니다. 이는 문서가 성공적으로 만들어졌음을 나타냅니다. 207이 표시될 경우 하나 이상의 문서를 업로드하지 못했습니다. 404가 표시될 경우 요청의 헤더 또는 본문에 구문 오류가 있습니다.

## 인덱스 쿼리

이제 인덱스와 문서가 로드되었으므로 쿼리를 실행할 수 있습니다. **작성기** 탭에서 서비스를 쿼리하는 **가져오기** 명령은 다음과 유사합니다.

   ![][3]

1.	**GET**을 선택합니다.

2.	HTTPS, 서비스 URL, "/indexes/<'indexname'>/docs?", 쿼리 매개 변수 순으로 URL을 입력합니다. 예를 들어 샘플 호스트 이름을 해당 서비스에 유효한 이름으로 바꾸어 다음 URL을 사용합니다.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    이 쿼리는 "motel" 용어를 검색하고 등급에 대한 패싯 범주를 가져옵니다.

3.	요청 헤더는 이전과 동일해야 합니다. 호스트와 api-key를 해당 서비스에 유효한 값으로 바꾼 것에 주의하세요.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

응답 코드는 200이고, 다음 스크린샷과 같은 응답 출력이 표시됩니다.

   ![][4]

다음 예제 쿼리는 MSDN의 [검색 인덱스 작업(Azure 검색 API)](http://msdn.microsoft.com/library/dn798927.aspx)에서 가져온 것입니다. 이 항목의 많은 예제 쿼리에는 공백이 포함되어 있으며, 공백은 Fiddler에서 허용되지 않습니다. Fiddler에서 쿼리를 시도하기에 앞서, 쿼리 문자열을 붙여 넣기 전에 각 공백을 + 문자로 바꾸세요.

**공백을 바꾸기 전:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**공백을 +로 바꾼 후:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## 시스템 쿼리

시스템을 쿼리하여 문서 수와 저장소 사용을 가져올 수도 있습니다. **작성기** 탭에서 요청은 다음과 유사하고, 응답에서 문서 수와 사용된 공간을 반환합니다.

 ![][5]

1.	**GET**을 선택합니다.

2.	해당 서비스 URL과 그 뒤에 "/indexes/hotels/stats?api-version=2015-02-28"을 포함하는 URL을 입력합니다.

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.	호스트와 api-key를 해당 서비스에 유효한 값으로 바꾸어 요청 헤더를 지정합니다.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	요청 본문을 비워 둡니다.

5.	**실행**을 클릭합니다. 세션 목록에 HTTP 200 상태 코드가 표시됩니다. 명령에 대해 게시할 항목을 선택합니다.

6.	**검사기** 탭과 **헤더** 탭을 차례로 클릭하고 JSON 형식을 선택합니다. 문서 수와 저장소 크기(KB)가 표시됩니다.

## 다음 단계

Azure 검색을 관리 및 사용하는 코드 없는 접근 방식은 [Azure에서 검색 서비스 관리](search-manage.md)를 참조하세요.


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png

<!---HONumber=AcomDC_1223_2015-->