<properties 
	pageTitle="Azure 검색을 사용하여 지리 공간 검색 앱 만들기" 
	description="Bing과 Azure 검색을 사용하여 지리 공간적 검색 앱 만들기" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Azure 검색을 사용하여 지리 공간 검색 앱 만들기

이 자습서에는 Azure 검색 및 Bing 지도를 사용하여 웹 응용 프로그램에 지리 공간적 검색 기능을 추가하는 방법을 보여 줍니다. 지리적 검색 기능을 통해 현재 위치에서 5km 거리 내의 모든 식당을 찾는 등 특정 지점 거리 내의 대상을 검색할 수 있습니다. Azure 검색의 지리 공간 기능은 일반적으로 사용되는 매핑 기능을 지원합니다. 예를 들어 다각형 경계 내의 주택 매물을 표시하는 부동산 앱에서 다각형 도형을 사용하려는 경우 OData 또는 간단한 검색 구문을 통해 원하는 도형을 쉽게 사용할 수 있습니다.

더 자세한 개요를 확인하려면 [Azure 검색 및 지리 공간적 데이터](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) Channel 9 비디오를 시청하세요.

![][7]

여기서는 응용 프로그램을 만들기 위해 Bing 매핑 서비스를 사용하여 CSV 파일에서 로드한 주소에 지역 코드를 적용한 다음 결과 데이터를 검색 인덱스에 저장합니다.

이 자습서는 [Azure 검색 – Adventure Works 데모](http://azuresearchadventureworksdemo.codeplex.com)를 기반으로 작성되었습니다. 해당 데모를 아직 연습해 보지 않았다면 인덱스를 만들고 웹 앱에서 Azure 검색 API를 호출하는 작업을 먼저 수행해 보세요.

<a id="sub-1"></a>
## 필수 조건

+	ASP.NET MVC 4 및 SQL Server가 설치된 Visual Studio 2012 이상 버전. 소프트웨어를 아직 설치하지 않은 경우 무료로 제공되는 Express 버전: [Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) 및 [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/evalcenter/dn434042.aspx)를 다운로드할 수 있습니다.
+	Azure 검색 서비스. 검색 서비스 이름과 관리 키가 필요합니다. 자세한 내용은 [포털에서 Azure 검색서비스 만들기](search-create-service-portal.md)를 참조하세요.
+	Bing 지도 서비스 및 서비스 액세스용 키. 관련 지침은 다음 섹션에 나와 있습니다.
+	[CodePlex의 Azure 검색 지리적 검색 샘플](https://azuresearchgeospatial.codeplex.com/). 소스 탭에서 **다운로드**를 클릭하여 솔루션 zip 파일을 다운로드하세요. 

    ![][12]

이 솔루션에는 다음의 두 프로젝트가 포함되어 있습니다.

+	**StoreIndexer**: Azure 검색 인덱스를 만들고 데이터를 로드합니다.
+	**AdventureWorksWebGeo**: Azure 검색 인덱스를 쿼리하고 Bing 지도에 매장 위치를 표시하는 MVC4 기반 응용 프로그램입니다.

[AZURE.INCLUDE [이 자습서를 완료하려면 Azure 계정이 있어야 합니다.](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## Bing 지도

여기서는 두 가지 용도로 Bing 지도 API를 사용합니다.

+ **지도에 지역 코드 적용:** 데이터에는 시/도, 구/군/시, 우편 번호 등의 주소가 포함되어 있지만 지리 공간적 검색을 수행할 수 있도록 해당 주소의 위도와 경도도 포함하려고 합니다. 이러한 좌표를 가져오기 위해 Bing 지도 DataFlow API를 사용하여 지역 코드를 적용할 주소 배치를 전송합니다. Bing 평가판 계정 사용 시에는 한 번에 전송할 수 있는 주소가 50개로 제한되지만 이 자습서에서는 그 정도면 충분합니다.

+ **Bing 지도:** 앱을 실행할 때 Bing 지도를 사용하여 매장 위치를 Bing 지도 위에 겹쳐서 표시합니다.

### Bing 지도용 계정 만들기

1. [Bing 지도 포털](https://www.bingmapsportal.com/)로 이동하여 새 계정을 만듭니다. 계정을 만들려면 세부 정보를 입력합니다.

2. 계정을 만든 후 **키 만들기 또는 보기**를 선택하고 세부 정보를 입력하여 키를 만듭니다. 이 데모에서는 **평가판 키**를 선택할 수 있습니다.

3. **제출**을 클릭하면 Bing 지도 응용 프로그램용 키 세부 정보가 표시됩니다. 이 키는 나중에 사용할 것이므로 적어 둡니다.

<a id="sub-3"></a>
## Bing 지도 DataFlow API를 사용하여 C#으로 지도 지역 코딩

이 단계에서는 Bing 지도 DataFlow API를 사용하여 전 세계 여러 자전거 매장의 일부 주소를 지역 코딩합니다.

이 데이터는 앞에서 다운로드한 소스에 포함된 store\_locations.csv라는 CSV 파일에 포함되어 있습니다. 텍스트 편집기나 Excel에서 이 파일을 열면 각 매장의 ID 열, 매장 이름 및 주소를 확인할 수 있습니다.

그러면 지도를 지역 코딩하는 방법을 설명하는 코드를 살펴보겠습니다.

1. Visual Studio에서 AdventureWorksGeo 솔루션을 열고, 솔루션 탐색기에서 프로젝트 **StoreIndexer**를 확장하고 Program.cs를 엽니다. [Azure 검색 – Adventure Works 데모](http://azuresearchadventureworksdemo.codeplex.com/)에서 인덱스 생성을 이미 다루었으므로, Program.cs에서 작동하는 방법에 대한 설명은 생략하겠습니다.

2. **Main** 함수로 이동하여 **ApplyStoreData**를 호출하는지 확인합니다. 해당 함수로 이동한 다음 코드를 단계별로 실행합니다.

3. **ApplyStoreData**는 "store\_locations.csv"라는 CSV 파일의 데이터를 System.Data.DataTable에 로드합니다.

    이 파일에는 모든 매장 및 Azure 검색으로 로드하려는 매장의 주소가 포함되어 있습니다. 파일의 각 행을 반복하면 **indexOperations** 집합을 만들 수 있습니다. 이 집합은 이전에 **CreateStoresIndex()** 함수에서 작성된 Azure 검색 인덱스에 삽입됩니다.

    나중에 인덱스를 자세히 살펴보면 각 매장의 위도와 경도가 포함된 **GeoPt** 필드가 비어 있음을 확인할 수 있습니다. 따라서 **Main** 함수의 다음 단계를 수행해야 합니다.

5. **ExtractAddressInfoToXML()** 함수로 이동합니다. 이 함수는store\_locations.csv 파일에서 주소 정보를 추출한 다음, Bing 지도가 지역 코딩용으로 허용하는 서식으로 지정된 XML 파일에 해당 주소 정보를 로드합니다. 이와 같이 파일이 작성되면 **GeoCoding.CreateJob** 함수를 호출하여 Bing 지도 DataFlow에서 처리하도록 파일을 전송합니다.

6. 지역 코드 적용 프로세스는 다소 시간이 걸릴 수 있으므로 10초마다 **GeoCoding.CheckStatus**를 호출하여 작업 완료 여부를 확인하는 루프가 있습니다. 작업이 완료되면 주소 클래스로 **GeoCoding.DownloadResults**를 호출하여 결과를 다운로드합니다.

7. 마지막 단계에서는 이와 같이 지역 코드가 적용된 주소를 가져와 Azure 검색으로 보냅니다. **UpdateStoreData** 함수를 열어 이 작업이 수행되는 방식을 자세히 살펴보겠습니다.

  **UpdateStoreData**에서 **@search.action: merge** 작업 사용: merge 작업을 사용하여 Edm.GeographyPoint 형식의 위치 필드를 방금 Bing 지도에서 다운로드한 지역 코드가 적용된 위도 및 경도 좌표로 업데이트합니다. 이를 위해 "store" 인덱스에서 문서의 고유 키인 storeId를 조회한 다음 이 새 데이터를 기존 문서에 병합합니다.

8. 응용 프로그램을 실행하기 전에 App.config를 열고 "SearchServiceName", "SearchServiceApiKey" 및 "BingMapsAPI"의 값을 Azure 검색 서비스 및 Bing 지도 API의값으로 업데이트하여 Azure 검색 및 Bing 지도 API 정보를 추가합니다. 검색 서비스 이름의 경우 서비스가 "mysearch.search.windows.net"이면 "mysearch"를 입력합니다.

9. **StoreIndexer** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그** | **새 인스턴스 시작**을 선택하여 프로젝트를 실행합니다.

<a id="sub-4"></a>
## Azure 검색 및 Bing 지도를 사용하여 MVC4 응용 프로그램에 매핑 추가

이 단계에서는 매장 검색을 로드한 다음 Bing 지도 위에 검색한 내용을 플로팅하는 검색 응용 프로그램을 웹 브라우저에서 빌드 및 실행합니다.

1.	Visual Studio에서 AdventureWorksGeo.sln이라는 Azure 검색 데모 솔루션을 엽니다. 
	
2.	솔루션 탐색기에서 **AdventureWorksWebGeo**를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
	
3.	이 프로젝트의 Web.config를 열고 "SearchServiceName", "SearchServiceApiKey" 및 "BingMapsAPI"의 값을 Azure 검색 서비스 및 Bing 지도 API의 값으로 업데이트합니다. 검색 서비스 이름의 경우 서비스가 "mysearch.search.windows.net"이면 "mysearch"를 입력합니다.

4.	Web.config를 저장합니다.
	
5.	**F5** 키를 눌러 프로젝트를 시작합니다. 빌드 오류가 발생하는 경우 [문제 해결](#err-mvc) 단계를 따르세요.

매장이 지도 위에 점으로 겹쳐져 표시됩니다. 매장 중 하나를 클릭하면 매장 세부 정보를 보여 주는 팝업이 표시됩니다. 이러한 모든 정보는 이전 단계에서 만든 "stores"라는 Azure 검색 인덱스에서 가져온 것입니다.

<a id="sub-5"></a>
## AdventureWorksWebGeo 살펴보기

**AdventureWorksWebGeo** 프로젝트에서는 ASP.NET MVC 4를 통해 Azure 검색과 상호 작용하여 지리적 검색을 활용하는 매핑 응용 프로그램을 빌드하는 방법을 보여 줍니다. 이 섹션에서는 응용 프로그램 코드의 개별 부분을 검토하여 각 코드의 용도를 파악합니다.

1.	솔루션 탐색기에서 **AdventureWorksWebGeo** | **컨트롤러**를 확장하고 HomeController.cs를 엽니다. **index()** 함수는 응용 프로그램이 시작되고 인덱스 페이지를 로드할 때 호출됩니다. 이 함수에서는 Bing 지도 API가 Web.config에서 로드되어 ViewBag.BingAPI로 인덱스 뷰에 전달됩니다.

2.	**뷰** | **홈**에서 Index.cshtml을 엽니다.

3.	이 파일은 Bing 지도를 웹 응용 프로그램에 추가하는 일반적인 방식을 따르지만 다음의 몇 가지 사항에 주의해야 합니다.

+	컨트롤러에서 ViewBag은 자격 증명: '@ViewBag.BingAPI'를 사용하여 지도에 대한 자격 증명을 로드하기 위해 사용됩니다. 

+	지도가 로드되면 /home/search를 참조하여 HomeController **Search** 함수에 대한 JQuery $.post를 수행합니다. 이때

+	**Search** 함수가 매장 위치를 검색하면 해당 정보가 Bing 지도에 수신되어 압정 모양으로 추가됩니다.

4.	**컨트롤러**에서 HomeController.cs를 열고 **Search** 함수를 찾습니다. 그런 다음 해당 함수가 \_storeSearch.Search(lat, lon, 10000)를 호출하는 방법을 확인합니다. 이 호출로 인해 쿼리가 실행되면서 지정한 위도(lat) 및 경도(long)에서 10,000km 이내의 모든 매장을 찾습니다. 이 쿼리의 결과는 처리된 다음 인덱스 뷰로 다시 전송되어 Bing 지도 위에 겹쳐진 압정 모양으로 처리됩니다.

이제 데모가 완료되었습니다. 지금까지 Azure 검색을 사용하는 ASP.NET MVC4 응용 프로그램을 기반으로 지도를 작성하기 전에 파악해야 하는 모든 주요 작업을 연습했습니다.


<a id="err-mvc"></a>
## "파일 또는 어셈블리 'System.Web.Mvc'를 로드하지 못했습니다." 오류를 해결하는 방법

AdventureWorksWeb을 빌드할 때 "파일이나 어셈블리 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 또는 여기에 종속되어 있는 파일이나 어셈블리 중 하나를 로드할 수 없습니다." 오류가 발생하는 경우 다음 단계를 수행하여 오류를 해결합니다.

1. **도구** | **NuGet 패키지 관리자 ** | ** 패키지 관리자 콘솔**을 선택하여 패키지 관리자 콘솔을 엽니다.
2. PM> 프롬프트에 "Update-package -reinstall Microsoft.AspNet.Mvc"를 입력합니다.
3. 파일을 다시 로드할 것인지 묻는 메시지가 표시되면 **모두 예**를 선택합니다.
4. 솔루션을 다시 빌드하고 **F5** 키를 다시 눌러 봅니다.

<a id="next-steps"></a>
## 다음 단계

추가 자가 학습을 진행하려는 경우 매핑 응용 프로그램에 기능을 더 추가할 수 있습니다. 예를 들어 다음과 같은 항목을 추가할 수 있습니다.

+ 사용자가 특정 매장을 검색하는 데 사용할 수 있는 검색 상자

+ 사용자가 국가나 시/도별로 필터링하는 데 사용할 수 있는 패싯

+ 사용자가 지도의 영역을 끌어 검색할 영역을 지정할 수 있는 사용자 그리기 선택 영역. 이렇게 그린 영역은 Azure 검색에서 지리적 교차 API를 사용하여 필터링된 다음 지도에 플로팅됩니다.


<!--Anchors-->
[Prerequisites]: #sub-1
[Bing Maps]: #sub-2
[Geocode Addresses in C# using Bing Maps DataFlow API]: #sub-3
[Add Mapping to an MVC4 Application using Azure Search and Bing Maps]: #sub-4
[Explore AdventureWorksWebGeo]: #sub-5
[Next steps]: #next-steps


<!--Image references-->
[7]: ./media/search-create-geospatial/AzureSearch-geo1-App.PNG
[12]: ./media/search-create-geospatial/AzureSearch_Create2_CodeplexDownload.PNG

<!---HONumber=Oct15_HO3-->