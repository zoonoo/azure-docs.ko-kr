---
title: '빠른 시작: Postman 및 REST API - Azure Search'
description: Postman 및 샘플 데이터와 정의를 사용하여 Azure Search REST API를 호출하는 방법을 알아봅니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: eaf594286e5ffc101ad2d24e808fcb806998d053
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66471567"
---
# <a name="quickstart-explore-azure-search-rest-apis-using-postman"></a>빠른 시작: Postman을 사용하여 Azure Search REST API 검색
> [!div class="op_single_selector"]
> * [Postman](search-fiddler.md)
> * [C#](search-create-index-dotnet.md)
> * [Python](search-get-started-python.md)
> * [포털](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
>*

[Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice)를 검색하는 가장 쉬운 방법 중 하나는 Postman 또는 다른 웹 테스트 도구를 사용하여 HTTP 요청을 작성하고 응답을 검사하는 것입니다. 적절한 도구와 이러한 지침을 사용하면 코드를 작성하기 전에 요청을 전송하고 응답을 볼 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만든 다음 [Azure Search에 등록](search-create-service-portal.md)하십시오.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스와 도구는 다음과 같습니다. 

+ [Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

+ [Postman 데스크톱 앱](https://www.getpostman.com/) 또는 [Telerik Fiddler](https://www.telerik.com/fiddler)는 요청을 Azure Search에 보내는 데 사용됩니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-search"></a>Azure Search에 연결

이 섹션에서는 선택한 웹 도구를 사용하여 Azure Search에 대한 연결을 설정합니다. 각 도구는 세션에 대한 요청 헤더 정보를 유지하므로 api-key 및 Content-Type을 한 번만 입력하면 됩니다.

두 도구 중 하나에서 명령(GET, POST, PUT 등)을 선택하고 URL 엔드포인트를 제공하며, 일부 작업의 경우 요청 본문에 JSON을 제공해야 합니다. 검색 서비스 이름(YOUR-SEARCH-SERVICE-NAME)을 유효한 값으로 바꿉니다. 

    https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06

HTTPS 접두사, 서비스 이름, 개체 이름(이 경우 인덱스 컬렉션) 및 [api-version](search-api-versions.md)을 확인합니다. api-version은 현재 버전에 대해 `?api-version=2019-05-06`로 지정된 필수 소문자 문자열입니다. API 버전은 정기적으로 업데이트됩니다. 각 요청에 api-version을 포함시키면 어느 것이 사용되는지를 완전히 제어할 수 있습니다.  

요청 헤더 구성에는 콘텐츠 형식 및 Azure Search에 인증하는 데 사용되는 api-key의 두 가지 요소가 포함됩니다. 관리 API 키(YOUR-ADMIN-API-KEY)를 유효한 값으로 바꿉니다. 

    api-key: <YOUR-ADMIN-API-KEY>
    Content-Type: application/json

Postman에서 다음 스크린샷과 같은 요청을 작성합니다. 동사로 **GET**을 선택하고, URL을 제공하고, **보내기**를 클릭합니다. 이 명령은 Azure Search에 연결하여 인덱스 컬렉션을 읽고, 성공적으로 연결되면 200 HTTP 상태 코드를 반환합니다. 서비스에 이미 인덱스가 있으면 응답에 인덱스 정의도 포함됩니다.

![Postman 요청 헤더][6]

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

Azure Search에서는 일반적으로 데이터를 로드하기 전에 인덱스를 만듭니다. 이 작업에는 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) REST API가 사용됩니다. 

URL은 `hotels` 인덱스 이름을 포함하도록 확장됩니다.

Postman에서 이렇게 하려면 다음을 수행합니다.

1. 동사를 **PUT**으로 변경합니다.

2. 이 URL(`https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06`)에 복사합니다.

3. 아래에 표시된 인덱스 정의를 요청 본문에 지정합니다.

4. **보내기**를 클릭합니다.

![Postman 요청 본문][8]

### <a name="index-definition"></a>인덱스 정의

fields 컬렉션은 문서 구조를 정의합니다. 각 문서에는 이러한 필드가 있어야 하며, 각 필드에는 데이터 형식이 있어야 합니다. 문자열 필드는 전체 텍스트 검색에 사용되므로 콘텐츠를 검색할 수 있도록 하려면 숫자 데이터를 문자열로 캐스팅해야 합니다.

필드의 특성에 따라 허용되는 작업이 결정됩니다. REST API는 기본적으로 많은 작업을 허용합니다. 예를 들어, 모든 문자열은 기본적으로 검색, 조회, 필터링이 가능하고 패싯이 가능합니다. 동작을 해제해야 하는 경우 특성만 설정하면 되는 경우가 많습니다.

          {
         "name": "hotels",  
         "fields": [
           {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
           {"name": "baseRate", "type": "Edm.Double"},
           {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
           {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
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

이 요청을 제출할 때 인덱스가 성공적으로 생성되었음을 나타내는 HTTP 201 응답을 받아야 합니다. 이 작업은 포털에서 확인할 수 있지만 포털 페이지에 새로 고침 간격이 있으므로 그 때까지 1~2분이 걸릴 수 있습니다.

> [!TIP]
> HTTP 504가 표시될 경우 URL이 HTTPS를 지정하는지 확인합니다. HTTP 400 또는 404가 표시되는 경우 요청 본문에서 복사/붙여 넣기 오류가 없는지 확인합니다. HTTP 403은 대개 api-key에 문제가 있음을 나타냅니다(잘못된 키 또는 api-key 지정 방법과 관련된 구문 문제).

## <a name="2---load-documents"></a>2 - 문서 로드

인덱스 생성과 인덱스 채우기는 별도의 단계입니다. Azure Search 검색에서 인덱스에는 JSON 문서로 제공할 수 있는 검색 가능한 모든 데이터가 포함됩니다. 이 작업에는 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) REST API가 사용됩니다. 

URL은 `docs` 컬렉션 및 `index` 작업을 포함하도록 확장됩니다.

Postman에서 이렇게 하려면 다음을 수행합니다.

1. 동사를 **POST**로 변경합니다.

2. 이 URL(`https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs/index?api-version=2019-05-06`)에 복사합니다.

3. 아래에 표시된 JSON 문서를 요청 본문에 지정합니다.

4. **보내기**를 클릭합니다.

![Postman 요청 페이로드][10]

### <a name="json-documents-to-load-into-the-index"></a>인덱스에 로드할 JSON 문서

요청 본문에 호텔 인덱스에 추가될 문서 4개가 포함됩니다.

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

몇 초 후에 HTTP 200 응답이 검색 목록에 표시됩니다. 이는 문서가 성공적으로 만들어졌음을 나타냅니다. 

207이 표시될 경우 하나 이상의 문서를 업로드하지 못했습니다. 404가 표시될 경우 요청의 헤더 또는 본문에 구문 오류가 있습니다. `/docs/index`를 포함하도록 엔드포인트를 변경했는지 확인하십시오.

> [!Tip]
> 선택한 데이터 원본의 경우 인덱싱에 필요한 코드의 양을 줄이고 단순화할 수 있는 대체 *인덱서* 방식을 선택할 수 있습니다. 자세한 내용은 [인덱서 작업](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)을 참조하세요.


## <a name="3---search-an-index"></a>3 - 인덱스 검색

이제 인덱스와 문서가 로드되었으므로 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents) REST API를 사용하여 쿼리를 실행할 수 있습니다.

URL은 검색 연산자를 사용하여 지정된 쿼리 문자열을 포함하도록 확장됩니다.

Postman에서 이렇게 하려면 다음을 수행합니다.

1. 동사를 **GET**으로 변경합니다.

2. 이 URL(`https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/docs?search=motel&$count=true&api-version=2019-05-06`)에 복사합니다.

3. **보내기**를 클릭합니다.

이 쿼리는 "motel"이라는 용어를 검색하고 검색 결과에 문서의 수를 반환합니다. **보내기**를 클릭한 후 요청 및 응답은 Postman의 다음 스크린샷과 유사합니다. 상태 코드는 200이어야 합니다.

 ![Postman 쿼리 응답][11]


## <a name="get-index-properties"></a>인덱스 속성 가져오기
시스템을 쿼리하여 문서 수와 저장소 사용량을 가져올 수도 있습니다. `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels/stats?api-version=2019-05-06`

Postman에서 요청은 다음과 유사하며 응답에는 문서 수와 사용된 공간(바이트 단위)이 포함됩니다.

 ![Postman 시스템 쿼리][12]

api-version 구문이 다른 점에 유의하세요. 이 요청의 경우 `?`를 사용하여 api-version을 추가합니다. `?`는 쿼리 문자열에서 URL 경로를 구분하고 &는 쿼리 문자열에서 각 '이름=값' 쌍을 구분합니다. 이 쿼리에서 api-version은 쿼리 문자열의 처음이자 유일한 항목입니다.

이 API에 대한 자세한 내용은 [인덱스 통계 가져오기 REST API](https://docs.microsoft.com/rest/api/searchservice/get-index-statistics)를 참조하세요.


## <a name="use-fiddler"></a>Fiddler 사용

이 섹션은 이전 섹션과 동일하며, Fiddler 스크린샷과 지침에만 해당합니다.

### <a name="connect-to-azure-search"></a>Azure Search에 연결

다음 스크린샷과 같은 요청을 작성합니다. 동사로 **GET**을 선택합니다. Fiddler가 `User-Agent=Fiddler`를 추가합니다. 두 개의 추가 요청 헤더를 그 아래 새 줄에 붙여넣을 수 있습니다. 서비스에 대한 관리자 액세스 키를 사용하여 서비스의 content-type 및 api-key를 포함시킵니다.

대상에 대해 `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2019-05-06` URL의 수정된 버전을 복사합니다.

![Fiddler 요청 헤더][1]

> [!Tip]
> 불필요하고 관계없는 HTTP 활동을 숨기려면 웹 트래픽을 끕니다. Fiddler의 **파일** 메뉴에서 **Capture Traffic**(트래픽 캡처)을 끕니다. 

### <a name="1---create-an-index"></a>1 - 인덱스 만들기

동사를 **PUT**으로 변경합니다. `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels?api-version=2019-05-06` URL의 수정된 버전을 복사하여 붙여넣습니다. 위에 제공된 인덱스 정의를 요청 본문에 복사합니다. 이 페이지는 다음 스크린샷과 비슷합니다. 완성된 요청을 보내려면 오른쪽 위에 있는 **실행**을 클릭합니다.

![Fiddler 요청 본문][7]

### <a name="2---load-documents"></a>2 - 문서 로드

동사를 **POST**로 변경합니다. `/docs/index`를 포함하도록 URL을 변경합니다. 다음 스크린샷과 같이 문서를 요청 본문에 복사한 이후에 요청을 실행합니다.

![Fiddler 요청 페이로드][9]

### <a name="tips-for-running-our-sample-queries-in-fiddler"></a>Fiddler에서 샘플 쿼리를 실행하는 팁

다음 예제 쿼리는 [문서 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 문서에서 가져온 것입니다. 이 문의 많은 예제 쿼리에는 공백이 포함되어 있으며, 공백은 Fiddler에서 허용되지 않습니다. Fiddler에서 쿼리를 시도하기에 앞서, 쿼리 문자열을 붙여 넣기 전에 각 공백을 + 문자로 바꾸세요.

**공백이 교체되기 전(lastRenovationDate desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06

**공백이 +로 교체된 후(lastRenovationDate+desc):**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2019-05-06

### <a name="tips-for-viewing-index-statistic-in-fiddler"></a>Fiddler에서 인덱스 통계를 보는 팁

Fiddler에서 **Inspectors**(검사기) 탭과 **헤더** 탭을 차례로 클릭하고 JSON 형식을 선택합니다. 문서 수와 저장소 크기(KB)가 표시됩니다.

## <a name="next-steps"></a>다음 단계

REST 클라이언트는 즉석 탐색에 매우 유용하지만 이제 REST API가 어떻게 작동하는지 알게 되었니 코드로 진행할 수 있습니다. 다음 단계는 다음 링크를 참조하세요.

+ [빠른 시작: .NET SDK를 사용하여 인덱스 만들기](search-create-index-dotnet.md)
+ [빠른 시작: PowerShell을 사용하여 인덱스(REST) 만들기](search-create-index-rest-api.md)

<!--Image References-->
[1]: ./media/search-fiddler/fiddler-url.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png
[6]: ./media/search-fiddler/postman-url.png
[7]: ./media/search-fiddler/fiddler-request.png
[8]: ./media/search-fiddler/postman-request.png
[9]: ./media/search-fiddler/fiddler-docs.png
[10]: ./media/search-fiddler/postman-docs.png
[11]: ./media/search-fiddler/postman-query.png
[12]: ./media/search-fiddler/postman-system-query.png
