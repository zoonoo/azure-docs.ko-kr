<properties title="Create your first search solution using Azure Search" pageTitle="Azure 검색을 사용하는 첫 검색 솔루션 만들기" description="Azure 검색을 사용하는 첫 검색 솔루션 만들기" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Azure 검색을 사용하는 첫 검색 솔루션 만들기

+ [필수 조건](#sub-1)
+ [Azure 검색 인덱스 만들기](#sub-2)
+ [CatalogIndexer 살펴보기](#sub-3)
+ [Azure 검색을 사용하여 MVC4 응용 프로그램 빌드](#sub-4)
+ [AdventureWorksWeb 살펴보기](#sub-5)
+ [다음 단계](#next-steps)

<h2>개요</h2>

<p>이 샘플에서는 Adventure Works 자전거 회사의 검색 응용 프로그램을 보여 줍니다. 이 자습서를 완료하면 패싯 탐색, 검색 결과에 적용할 수 있는 여러 정렬 옵션 및 자동 완성 쿼리 추천 단어를 비롯한 효율적인 검색 환경이 포함된 온라인 제품 카탈로그가 완성됩니다.

   ![][7]

데모에서는 Azure 검색을 시작할 수 있도록 다음 연습을 안내합니다.

+	Azure 검색 인덱스 만들기
+	SQL Server 데이터베이스에서 Azure 검색 인덱스로 문서(데이터) 로드
+	Azure 검색을 사용하여 다음을 수행하는 ASP.NET MVC4 응용 프로그램 빌드
	+	Azure 검색 인덱스에서 결과 검색 및 표시
	+	검색 용어를 입력하는 동안 검색 상자에 자동 완성 추천 단어 표시
	+	패싯을 사용하여 검색 결과 필터링


<h2 id="sub-1">필수 조건</h2>

+	ASP.NET MVC 4 및 SQL Server가 설치된 Visual Studio 2012 이상 버전. 소프트웨어를 아직 설치하지 않았다면 무료 Express 버전인 [Visual Studio 2013 Express](http://www.visualstudio.com/ko-kr/products/visual-studio-express-vs.aspx) 및 [Microsoft SQL Server 2014 Express](http://msdn.microsoft.com/ko-kr/evalcenter/dn434042.aspx).
+	Azure 검색 서비스. 검색 서비스 이름과 관리 키가 필요합니다. 자세한 내용은 [Azure 검색 시작](../search-get-started/) 을 참조하세요.
+	[CodePlex의 Adventure Works Azure 검색 데모 프로젝트](http://go.microsoft.com/fwlink/p/?LinkID=510972). 소스 탭에서 **다운로드**를 클릭하여 솔루션 zip 파일을 다운로드합니다. 

    ![][12]


이 솔루션에는 다음의 두 프로젝트가 포함되어 있습니다.

+	**CatalogIndex**는 Azure 검색 인덱스를 만들고 프로젝트에 포함된 SQL Server 데이터베이스에서 데이터를 로드합니다.
+	**AdventureWorksWeb**은 Azure 검색 인덱스를 쿼리하는 MVC4 기반 응용 프로그램입니다. 이 자습서에서는 사용자에게 ASP.NET MVC에 대한 실용적인 지식이 있다고 가정합니다.

[WACOM.INCLUDE [You need an Azure account to complete this tutorial:](../includes/free-trial-note.md)]


<h2 id="sub-2">Azure 검색 인덱스 만들기</h2>

이 단계에서는 **CatalogIndex**를 사용하여 AdventureWorks SQL Server 데이터베이스의 데이터를 기반으로 새 Azure 검색 인덱스 "catalog"를 만듭니다.

1.	Visual Studio에서 Azure 검색 데모 솔루션 **AdventureWorksCatalog.sln**을 엽니다.  
2.	솔루션 탐색기에서 **CatalogIndexer**를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 선택합니다. 그러면 **F5** 키를 누를 때 **AdventureWorksWeb** 프로젝트가 아닌 이 응용 프로그램이 실행됩니다.
3.	이 프로젝트의 **App.config**를 열고 "SearchServiceName" 및 "SearchServiceApiKey"의 값을 Azure 검색 서비스의 값으로 업데이트합니다. 검색 서비스 이름의 경우 서비스가 "mysearch.search.windows.net"이면 "mysearch"를 입력합니다.
4.	App.config에는 SQL Server 2014 Express LocalDB를 사용한다고 가정하는 "SourceSqlConnectionString"의 항목(Server=(LocalDB)\v11.0)도 선택적으로 포함됩니다. 다른 SQL Server 버전을 사용하는 경우에는 해당 버전에 따라 서버 이름을 업데이트합니다. 예를 들어 로컬 기본 인스턴스가 있으면 (local) 또는 localhost를 사용할 수 있습니다.
5.	**App.config**를 저장합니다.
6.	**F5** 키를 눌러 프로젝트를 시작합니다.

명령 프롬프트가 열리고 "인덱스를 만드는 중..." 텍스트가 표시됩니다.

몇 분 후에 명령 프롬프트가 완료되고 "완료되었습니다.  계속하려면 <enter> 키를 누르세요." 텍스트가 표시됩니다.

   ![][8]

**Enter** 키를 눌러 응용 프로그램을 닫습니다. 이제 Azure 검색 인덱스가 작성되었습니다. 

> [WACOM.NOTE] "키 'attachdbfilename'에 대한 값이 잘못되었습니다."라는 메시지가 포함된 오류 또는 기타 데이터베이스 연결 오류가 표시되는 경우에는 UAC 충돌이 발생한 것일 수 있습니다. 이 데모에서는 다음을 수행하여 이 오류를 해결합니다. 
> 인증된 사용자에 대해 액세스 권한을 제공하는 솔루션을 새 폴더나 기존 폴더(예: Temp)로 복사합니다. 
> **관리자 권한으로 실행**을 사용하여 Visual Studio를 시작합니다. 
> 솔루션을 열고 빌드한 다음 **F5** 키를 눌러 인덱스를 만듭니다.

인덱스 만들기 및 문서 업로드를 확인하려면 [Azure 미리 보기 포털](https://portal.azure.com)에서 검색 서비스 대시보드로 이동합니다. 사용량에서 인덱스 수가 1개 늘어나야 하며 데이터베이스의 각 제품에 대해 하나씩 문서가 294개 있어야 합니다.

**인덱스** 타일을 클릭하여 인덱스 목록을 표시합니다. 인덱스 목록이 이동하면서 새 인덱스 및 문서 수가 표시됩니다.

   ![][9]


<h2 id="sub-3">CatalogIndexer 살펴보기</h2>

이제 **CatalogIndexer** 프로젝트를 자세히 살펴보면서 해당 작동 방식을 파악해 보겠습니다.

1.	솔루션 탐색기에서 **Program.cs**를 열고 `Main(string[] args)` 함수로 이동합니다.

    이 함수가 특정 Azure 검색 서비스를 기준으로 '_serviceURI'라는 URI를 작성한 다음 API 키를 사용하여 이 검색 서비스에 인증하는 '_httpClient'라는 HttpClient를 만드는 방법을 살펴보세요.

2.	'ChangeEnumeratorSql' 및 'ChangeSet'는 SQL Server AdventureWorks 데이터베이스의 Products 테이블에서 데이터를 열거하는 데 사용됩니다. 

3.	이 테이블에서 수집되는 데이터를 기준으로 'ApplyChanges'를 호출하여 Azure 검색 인덱스에 해당 데이터를 적용합니다.

4.	같은 파일의 'ApplyChanges'로 이동합니다. 인덱스가 이미 있는 경우 이 함수가 인덱스를 삭제한 다음('DeleteCatalogIndex') 새 인덱스인 "catalog"를 만드는 방법('CreateCatalogIndex')을 살펴보세요.  

5.	'CreateCatalogIndex' 함수로 이동한 다음 SQL Server의 Products 테이블 열과 일치하는 스키마를 사용하여 인덱스가 작성되는 방법을 살펴보세요. 각 필드에는 유형('Edm.String' 또는 'Edm.Double')과 해당 필드의 용도를 정의하는 특성이 있습니다. 이러한 특성에 대한 자세한 내용은 [Azure 검색 REST API 문서](http://msdn.microsoft.com/ko-kr/library/azure/dn798935.aspx)를 참조하세요.

6.	'ApplyChanges' 함수로 돌아간 다음 이 함수가 'ChangeSet'에서 열거된 변경 내용의 모든 데이터를 반복하는 방법을 살펴보세요. 변경 내용은 하나씩 적용되지 않고 1,000개 단위의 배치 그룹으로 구성된 다음 검색 서비스에 적용됩니다. 따라서 문서를 하나씩 적용하는 것보다 훨씬 효율적입니다.

지금까지 SQL Server의 관계형 데이터를 사용하여 인덱스를 만들고 채우는 방법을 살펴보았습니다. 다음으로는 검색 데이터를 사용하는 제품 카탈로그를 만드는 방법을 알아보겠습니다.


<h2 id="sub-4">Azure 검색을 사용하여 MVC4 응용 프로그램 빌드</h2>

이 단계에서는 웹 브라우저에서 검색 응용 프로그램을 빌드 및 실행합니다.

1.	Visual Studio에서 Azure 검색 데모 솔루션 **AdventureWorksCatalog.sln**을 엽니다.  

2.	솔루션 탐색기에서 **AdventureWorksWeb**을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.

3.	이 프로젝트의 **Web.config**를 열고 "SearchServiceName" 및 "SearchServiceApiKey"의 값을 Azure 검색 서비스의 값으로 업데이트합니다. 검색 서비스 이름의 경우 서비스가 "mysearch.search.windows.net"이면 "mysearch"를 입력합니다.

4.	**Web.config**를 저장합니다.

5.	**F5** 키를 눌러 프로젝트를 시작합니다. 빌드 오류가 발생하는 경우 이 [문제 해결](#err-mvc) 단계를 따르세요. 

    ![][10]

6.	검색 상자를 비워 두고 **검색**을 클릭하여 294개 제품을 모두 반환합니다.

7.	왼쪽의 패싯 목록을 확인한 다음 패싯 중 하나를 클릭해 봅니다. 검색이 다시 실행되지만 이번에는 결과를 필터링하기 위해 선택한 패싯이 추가됩니다. **HomeController.cs** `Search` 함수의 첫 줄에 중단점을 추가하여 각 줄을 단계별로 실행(F11)할 수 있습니다.

7.	"mountain bikes" 등의 검색 용어를 입력하면서 추천 쿼리의 드롭다운 목록을 살펴봅니다.

    ![][11]

아래에는 지금까지 다룬 내용을 다시 확인할 수 있도록 이 데모 시작 부분에 나왔던 스크린샷이 다시 표시되어 있습니다. 이전 섹션에서는 왼쪽에 표시되는 패싯 탐색, 페이지 위쪽의 문서 수, 추천 단어, 그리고 관련성/목록/가격을 기준으로 정렬하기 위한 정렬 옵션에 대해 설명했습니다.

   ![][7]


<h2 id="sub-5">AdventureWorksWeb 살펴보기</h2>

AdventureWorksWeb 프로젝트에서는 ASP.NET MVC 4를 통해 웹 응용 프로그램에서 Azure 검색과 상호 작용하는 방법을 보여 줍니다. 이 섹션에서는 응용 프로그램 코드의 개별 부분을 검토하여 각 코드의 용도를 파악합니다.

1.	솔루션 탐색기에서 **AdventureWorksWeb** | **컨트롤러**를 확장하고 **HomeController.cs**를 엽니다.

    이 컨트롤러는 인덱스 보기에서 상호 작용을 관리하는 MVC 컨트롤러입니다.  컨트롤러 맨 위에는 Azure 검색 서비스에 대해 HttpClient 연결 개체를 만드는 'CatalogSearch _catalogSearch'에 대한 참조가 있습니다. 이 'CatalogSearch'의 코드는 **CatalogSeach.cs**에 있습니다.

2. **HomeController.cs** 내에는 다음 두 개의 main 함수가 있습니다.

	**Search**: 사용자가 검색 단추를 클릭하거나 패싯을 선택하면 이 함수가 호출되어 결과를 검색한 다음 표시를 위해 인덱스 보기로 다시 보냅니다.

	**Suggest**: 사용자가 검색 상자에 내용을 입력하면 이 함수가 호출되어 Azure 검색 인덱스의 내용을 기준으로 추천 단어 목록을 반환합니다.

그러면 이 두 함수에 대해 좀 더 자세히 알아보겠습니다.  

3.	**HomeController.cs** `Search` 함수에는 Azure 검색 서비스에 대한 연결 개체를 포함하는 `_catalogSearch.Search` 호출이 있습니다. **CatalogSearch.cs**에 있는 Search 함수를 호출하는 경우 해당 함수는 이러한 매개 변수를 사용하여 Azure 검색 쿼리를 작성합니다. 쿼리의 결과는 'result'라는 JSON 개체에 저장된 다음 'Index' 보기로 다시 전달됩니다. 이 보기에서 결과가 구문 분석되어 웹 페이지에 표시됩니다.

4.	보기 | 홈에서 **Index.cshtml**을 열면 이러한 결과를 구문 분석하는 데 사용되는 코드를 확인할 수 있습니다.

5.	응용 프로그램이 아직 실행되고 있으면 중지하고 보기 | 홈에서 **Index.cshtml** 파일을 엽니다.  이 파일 끝에서 'JQuery $(function ())'을 사용하는 JavaScript 함수를 확인할 수 있습니다. 페이지 로드 시 호출되는 이 함수는 JQuery 자동 완성 함수를 사용하며 이 함수를 검색 텍스트 상자로부터의 콜백으로 연결합니다("q"로 식별됨). 사용자가 텍스트 상자에 텍스트를 입력할 때마다 이 자동 완성 함수가 호출되며, 그러면 입력한 내용을 사용하여 /home/suggest가 호출됩니다.  '/home/suggest'는 'Suggest'라는 **HomeController.cs**의 함수에 대한 참조입니다.

6.	**HomeController.cs**를 열고 Suggest 함수로 이동합니다. 이 코드는 '_catalogSearch' 개체를 사용하여 **CatalogSearch.cs**에서 'Suggest'라는 함수를 호출하는 Search 함수와 매우 비슷합니다. 그러나 'Suggest' 함수는 검색 쿼리를 만드는 대신 [추천 단어 API](http://msdn.microsoft.com/ko-kr/library/azure/dn798936.aspx)를 호출합니다. 이때 텍스트 상자에 입력된 용어를 사용하여 추천 가능한 단어의 목록을 작성합니다. 값은 **Index.cshtml** 파일로 반환되며 검색 상자에 자동 완성 옵션으로 자동 나열됩니다.

Azure 검색에서 추천 단어를 작성하는 데 사용하는 필드를 확인하는 방법은 인덱스를 만들 때 지정합니다. **CatalogIndexer** 프로젝트의 Program.cs 파일 내 'CreateCatalogIndex' 함수에는 'Suggestions'라는 특성이 있습니다.  이 특성을 'True'로 설정할 때마다 Azure 검색은 해당 특성을 추천 단어 검색용 필드로 사용할 수 있습니다.

그러면 이 작업을 수행해 보겠습니다.  

7.	응용 프로그램을 빌드한 다음 **F5** 키를 눌러 응용 프로그램을 실행합니다.

8.	검색 상자에 "Road"라는 단어를 입력합니다.  그러면 잠시 후 텍스트 상자 아래에 항목 목록과 사용자가 선택할 수 있는 추천 단어가 표시됩니다.  

**HomeController.cs** 내의 'Suggest' 함수에 중단점을 추가하여 추천 단어 목록을 작성하기 위해 수행되는 각 호출을 단계별로 실행(F11)할 수 있습니다.

이제 데모가 완료되었습니다. 지금까지 Azure 검색을 사용하는 ASP.NET MVC4 응용 프로그램을 빌드하기 전에 파악해야 하는 모든 주요 작업을 연습했습니다.


<h2 id="err-mvc">"파일 또는 어셈블리 'System.Web.Mvc'를 로드하지 못했습니다." 오류를 해결하는 방법</h2>

AdventureWorksWeb을 빌드할 때 "파일이나 어셈블리 'System.Web.Mvc, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 또는 여기에 종속되어 있는 파일이나 어셈블리 중 하나를 로드할 수 없습니다." 오류가 발생하는 경우 다음 단계를 수행하여 오류를 해결합니다.

1. 패키지 관리자 콘솔을 열기 위해 **도구** | **NuGet 패키지 관리자** | **패키지 관리자 콘솔**을 선택합니다.
2. PM> 프롬프트에 "Update-package -reinstall Microsoft.AspNet.Mvc"를 입력합니다.
3. 파일을 다시 로드할 것인지 묻는 메시지가 표시되면 **모두 예**를 선택합니다.
4. 솔루션을 다시 빌드하고 **F5** 키를 다시 눌러 봅니다.


<h2 id="next-steps">다음 단계</h2>

자가 학습을 추가로 진행하려면 사용자가 검색 결과 중 하나를 클릭할 때 열리는 세부 정보 페이지를 추가해 보세요. 이 작업을 준비하기 위해 다음을 수행할 수 있습니다.

+	특정 문서를 반환하도록 Azure 검색을 쿼리할 수 있는 [조회 API](http://msdn.microsoft.com/ko-kr/library/azure/dn798929.aspx) 관련 문서를 확인합니다(예: productID를 전달할 수 있습니다).
+	**HomeController.cs** 파일에 Details라는 새 함수를 추가하고, 이 조회의 결과를 받아서 표시하는 해당 **Details.cshtml** 보기를 추가해 봅니다.
+	지리 공간 검색에 대한 추가 코드 샘플과 비디오인 [Channel 9 - Azure 검색 및 지리 공간 데이터 ](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) 및 [CodePlex: Azure 검색 지리적 검색 샘플](http://azuresearchgeospatial.codeplex.com)을 확인해 보세요.

MSDN에서 [Azure 검색 REST API](http://msdn.microsoft.com/ko-kr/library/azure/dn798935.aspx)를 검토할 수도 있습니다.


<!--Anchors-->
[필수 조건]: #sub-1
[Azure 검색 인덱스 만들기]: #sub-2
[CatalogIndexer 살펴보기]: #sub-3
[Azure 검색을 사용하여 MVC4 응용 프로그램 빌드]: #sub-4
[AdventureWorksWeb 살펴보기]: #sub-5
[다음 단계]: #next-steps


<!--Image references-->
[7]: ./media/search-create-first-solution/AzureSearch_Create1_WebApp.PNG
[8]: ./media/search-create-first-solution/AzureSearch_Create1_ConsoleMsg.PNG
[9]: ./media/search-create-first-solution/AzureSearch_Create1_DashboardIndexes.PNG
[10]: ./media/search-create-first-solution/AzureSearch_Create1_WebAppEmpty.PNG
[11]: ./media/search-create-first-solution/AzureSearch_Create1_Suggestions.PNG
[12]: ./media/search-create-first-solution/AzureSearch_Create1_CodeplexDownload.PNG

<!--HONumber=35.2-->
