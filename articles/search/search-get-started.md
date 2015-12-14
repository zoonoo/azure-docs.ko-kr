<properties 
	pageTitle="Azure 검색 시작 | Microsoft Azure | 호스트된 클라우드 검색 서비스" 
	description="Microsoft Azure에서 클라우드 호스팅되는 검색 서비스인 Azure 검색을 시작합니다." 
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
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

# Azure 검색 시작

Microsoft Azure 검색은 호스팅되는 클라우드 검색 서비스로, 서비스 기능을 사용자 지정 응용 프로그램에 포함할 수 있습니다. .NET 라이브러리 또는 REST API를 사용하여 액세스 및 관리하는 데이터에 대한 저장소와 검색 엔진을 제공합니다.

이 문서에서는 Azure 검색 REST API 사용을 시작합니다.

.NET 개발자를 위한 다른 방법은 Azure 검색 .NET SDK를 사용하는 것입니다. 자세한 내용은 [.NET에서 Azure 검색 시작](search-get-started-dotnet.md) 또는 [Azure 검색 .NET SDK를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.


> [AZURE.NOTE]이 자습서를 완료하려면 [Azure 구독](../includes/free-trial-note.md)이 필요합니다. 평가판 구독에 등록할 준비가 되지 않은 경우 이 자습서를 건너뛰고 [Azure 앱 서비스 시도](https://tryappservice.azure.com/)를 선택할 수 있습니다. 이 대체 옵션은 ASP.NET 웹앱과 함께 Azure 검색을 무료로 제공하며 - 세션당 한 시간 - 구독이 필요 없습니다.
 
<a id="sub-1"></a>
## 무료 서비스로 시작

관리자는 공유 서비스를 선택할 때 무료로 또는 전용 리소스를 옵트인(opt in)할 때 할인 요금으로 검색 서비스를 기존 구독에 추가할 수 있습니다.

구독자는 학습 목적, 개념 증명 테스트 또는 소규모 개발 검색 프로젝트에 사용할 수 있는 공유 다중 테넌트 검색 서비스에 무료로 자동 액세스할 수 있습니다. 다음 단계에 따라 무료 버전을 등록하세요.

1. 기존 구독을 사용하여 [Azure 클래식 포털](https://portal.azure.com)에 로그인합니다. 이 URL을 클릭하면 포털로 이동됩니다. 반드시 포털을 사용해야 합니다. 

2. 페이지 위쪽에서 **새로 만들기**를 클릭합니다.
 
  	![][6]

3. **데이터 + 저장소** | **검색**을 클릭합니다.

	- 서비스 URL에 사용할 소문자 서비스 이름을 입력합니다. 공백을 사용하지 않고 15자 문자열 제한 이내로 유지해야 합니다.

	- **가격 계층**의 화살표를 클릭하여 가격 옵션을 선택합니다. **무료**를 선택하고 페이지 맨 아래에서 **선택**을 클릭합니다. 무료 버전은 자습서를 체험하고 개념 증명 코드를 작성하기에 충분한 용량을 제공하지만 프로덕션 응용 프로그램에 사용할 수 없습니다.

	- **리소스 그룹**의 화살표를 클릭하여 기존 그룹을 선택하거나 새로 만듭니다. 리소스 그룹은 일반적인 목적에 사용되는 서비스 및 리소스의 컨테이너입니다. 예를 들어 Azure 검색, Azure 웹 사이트, Azure BLOB 저장소를 기반으로 하여 사용자 지정 검색 응용 프로그램을 작성하는 경우 포털 관리 페이지에서 해당 서비스를 결합하는 리소스 그룹을 만들 수 있습니다.

	- 여러 구독이 있고 이 검색 서비스에 다른 구독을 사용하려는 경우 **구독**의 화살표를 클릭합니다.

	- **위치**의 화살표를 클릭하여 데이터 센터 지역을 선택합니다. 이 미리 보기에서는 미국 서부, 미국 동부, 북유럽 및 동남아시아 중에서 선택할 수 있습니다. 나중에 다른 지역이 온라인에 표시되면 만드는 서비스에 대해 하나의 지역을 선택합니다. 여러 데이터 센터에 리소스 분산은 공개 미리 보기에서 지원되는 구성이 아닙니다.

4. **만들기**를 클릭하여 서비스를 프로비전합니다. **만들기**는 모든 필수 값을 입력한 후에만 사용할 수 있습니다.

몇 분 후에 서비스가 만들어집니다. 구성 설정으로 돌아가서 URL 또는 api-key를 가져올 수 있습니다. 검색 서비스에 연결하려면 URL과 호출을 인증할 api-key가 둘 다 있어야 합니다. 다음은 이러한 값을 신속하게 찾는 방법입니다.

14. **홈**으로 이동하여 대시보드를 엽니다. 검색 서비스를 클릭하여 서비스 대시보드를 엽니다. 

  	![][13]

15.	서비스 대시보드에는 **속성** 및 **키**에 대한 타일과 리소스 사용을 한눈에 보여 주는 사용 정보가 표시됩니다.

이러한 값을 사용하여 서비스에 연결하는 방법에 대한 지침은 [서비스 작동 테스트](#sub-3)를 참조하세요.

<a id="sub-2"></a>
## 표준 검색으로 업그레이드

표준 검색은 Azure 데이터 센터에서 사용자만 사용할 수 있는 전용 리소스를 제공합니다. 검색 작업에는 저장소 및 서비스 복제본이 둘 다 필요합니다. 표준 검색을 등록할 때 시나리오에 가장 중요한 리소스를 더 많이 사용하도록 서비스 구성을 최적화할 수 있습니다.

전용 리소스가 있으면 크기가 확장되고 성능이 향상되지만 추가 기능은 없습니다. 공유 및 표준 검색 둘 다 동일한 기능을 제공합니다.

표준 검색을 사용하려면 표준 가격 계층을 선택하여 새 검색 서비스를 만듭니다. 업그레이드는 무료 버전의 바로 업그레이드가 아닙니다. 표준으로 전환하면 크기가 확장될 수 있으므로 새 서비스가 필요합니다. 검색 응용 프로그램에서 사용하는 인덱스와 문서를 다시 로드해야 합니다.

전용 리소스를 설정하는 데 시간이 걸릴 수 있습니다(15분 이상).

**1단계 - 가격 책정 계층을 표준으로 설정하여 새 서비스 만들기**

1. 기존 구독을 사용하여 [Azure 클래식 포털](https://portal.azure.com)에 로그인합니다. 

2. 페이지 아래쪽에서 **새로 만들기**를 클릭합니다.

4. 갤러리에서 **데이터 + 저장소** | **검색**을 클릭합니다.

7. 서비스 구성 설정을 작성한 다음 **만들기**를 클릭합니다.

8. **가격 책정 계층**의 가격 옵션을 선택합니다. **표준**을 선택하고 페이지 맨 아래에서 **선택**을 클릭합니다.

**2단계 - 크기 조정 요구 사항에 따라 검색 단위 조정**

표준 검색은 각각 하나의 복제본과 파티션으로 시작되지만 상위 리소스 수준에서 쉽게 크기를 조정할 수 있습니다.

1.	서비스가 만들어진 후 서비스 대시보드로 돌아가서 **크기 조정** 타일을 클릭합니다.

2.	슬라이더를 사용하여 복제본, 파티션 또는 둘 다를 추가합니다.

추가 복제본과 파티션은 검색 단위로 청구됩니다. 리소스를 추가할 때 특정 리소스 구성을 지원하는 데 필요한 총 검색 단위가 페이지에 표시됩니다.

[가격 정보](http://go.microsoft.com/fwlink/p/?LinkID=509792)에서 단위당 청구 정보를 확인할 수 있습니다. 파티션 및 복제본 조합을 구성하는 방법 결정에 대한 도움말은 [제한 및 제약 조건](search-limits-quotas-capacity.md)을 참조하세요.

 ![][15]

<a id="sub-3"></a>
## 서비스 작동 테스트

서비스가 작동하고 클라이언트 응용 프로그램에서 액세스 가능한지 확인하는 것이 검색 구성의 최종 단계입니다. 이 절차에서는 [Telerik에서 무료 다운로드](http://www.telerik.com/fiddler)로 제공되는 Fiddler를 사용하여 HTTP 요청을 실행하고 응답을 확인합니다. Fiddler를 사용하면 코드를 작성하지 않고 API를 즉시 테스트할 수 있습니다.

다음 절차는 공유 및 표준 검색 둘 다에서 작동합니다. 아래 단계에서는 인덱스를 만들고 문서를 업로드한 다음 인덱스를 쿼리하고 시스템에 서비스 정보를 쿼리합니다.

### 인덱스 만들기

1. Fiddler를 시작합니다. File 메뉴에서 **Capture Traffic**을 해제하여 현재 작업과 관련 없는 HTTP 활동을 숨깁니다. Composer 탭에서 다음과 같은 요청을 작성합니다. 

  	![][16]

2. **PUT**을 선택합니다.

3. 서비스 URL(Properties 페이지에서 확인할 수 있음)을 지정하는 URL, 요청 특성 및 api-version을 입력합니다. 다음 몇 가지 사항에 주의하세요.
   + HTTPS를 접두사로 사용합니다.
   + 요청 특성은 "/indexes/hotels"입니다. 그러면 검색에서 'hotels'라는 인덱스를 만듭니다.
   + api-version은 소문자이며 "?api-version=2015-02-28"로 지정됩니다. Azure 검색은 주기적으로 업데이트를 배포하므로 API 버전이 중요합니다. 드물긴 하지만 서비스 업데이트에서 새로운 API 변경 사항을 소개할 수도 있습니다. API 버전을 사용하면 기존 버전을 계속 사용하고 편리한 시간에 최신 버전으로 업그레이드할 수 있습니다.

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
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
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

### 문서 로드

Composer 탭에서 문서 게시 요청은 다음과 같이 표시됩니다. 요청 본문에 4개 호텔에 대한 검색 데이터가 포함됩니다.

   ![][17]

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

### 인덱스 쿼리

이제 인덱스와 문서가 로드되었으므로 쿼리를 실행할 수 있습니다. Composer 탭에서 서비스를 쿼리하는 GET 명령은 다음과 유사합니다.

   ![][18]

1.	**GET**을 선택합니다.

2.	HTTPS, 서비스 URL, "/indexes/<'indexname'>/docs?", 쿼리 매개 변수 순으로 URL을 입력합니다. 예를 들어 샘플 호스트 이름을 해당 서비스에 유효한 이름으로 바꾸어 다음 URL을 사용합니다.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    이 쿼리는 "motel" 용어를 검색하고 등급에 대한 패싯 범주를 가져옵니다.

3.	요청 헤더는 이전과 동일해야 합니다. 호스트와 api-key를 해당 서비스에 유효한 값으로 바꾼 것에 주의하세요.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

응답 코드는 200이고, 다음 그림과 같은 응답 출력이 표시됩니다.
 
   ![][19]

다음 예제 쿼리는 MSDN의 [검색 인덱스 작업(Azure 검색 API)](http://msdn.microsoft.com/library/dn798927.aspx)에서 가져온 것입니다. 이 항목의 많은 예제 쿼리에는 공백이 포함되어 있으며, 공백은 Fiddler에서 허용되지 않습니다. Fiddler에서 쿼리를 시도하기에 앞서, 쿼리 문자열을 붙여 넣기 전에 각 공백을 + 문자로 바꾸세요.

**공백을 바꾸기 전:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**공백을 +로 바꾼 후:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### 시스템 쿼리

시스템을 쿼리하여 문서 수와 저장소 사용을 가져올 수도 있습니다. Composer 탭에서 요청은 다음과 유사하고, 응답에서 문서 수와 사용된 공간을 반환합니다.

   ![][20]

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

6.	Inspectors 탭 | Headers를 클릭하고 JSON 형식을 선택합니다. 문서 수와 저장소 크기(KB)가 표시됩니다.

 	![][21]

<a id="sub-4"></a>
## 검색 서비스 대시보드 살펴보기

구성 페이지를 찾을 위치에 리프레셔가 필요한 경우 다음 단계에 따라 서비스 대시보드를 찾으세요.

1.	기존 구독을 사용하여 [Azure 클래식 포털](https://portal.azure.com)에 로그인합니다. 
2.	**홈**을 클릭한 다음 검색 서비스에 대한 타일을 클릭합니다.

 	![][22]

4.	타일을 클릭하면 서비스 대시보드가 열립니다. **시작**, **중지** 및 **삭제** 명령이 맨 위에 있습니다.

5.	페이지 맨 위 부근에 서비스 URL이 있습니다. Azure 검색 서비스에 연결하려면 이 URL이 필요합니다.
	
7.	**키** 아이콘을 클릭하여 api-key를 확인합니다. 서비스에 인증하려면 관리자 키가 필요합니다. 기본 또는 보조를 사용할 수 있습니다. 필요한 경우 서비스에 대한 읽기 전용 액세스를 위해 쿼리 키를 만들 수 있습니다.


<!--Next steps and links -->
<a id="next-steps"></a>
## 사용해보기

다음 단계를 시작할 준비가 되었나요? 다음 링크를 통해 Azure 검색을 사용하는 검색 응용 프로그램을 작성 및 관리하는 방법을 보여 주는 추가 자료로 이동할 수 있습니다.

- [Azure 검색 지리적 검색 샘플 만들기](search-create-geospatial.md)

- [Microsoft Azure에서 검색 솔루션 관리](search-manage.md)

- [Azure 검색이란?](search-what-is-azure-search.md)

- [Azure 검색 서비스 REST API](http://msdn.microsoft.com/library/dn798935.aspx)

- [Azure 검색.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [채널 9 비디오: Azure 검색 소개](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [채널 9 비디오: Azure 검색 및 지리적 검색 데이터](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [클라우드 커버 에피소드 152: Azure 검색에서 인덱스 생성](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=AcomDC_1203_2015-->