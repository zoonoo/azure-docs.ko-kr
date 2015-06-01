<properties 
	pageTitle=".NET에서 첫 Azure 검색 응용 프로그램 만들기" 
	description="Azure 검색 .NET SDK의 .NET 클라이언트 라이브러리를 사용하여 솔루션을 빌드하는 자습서입니다." 
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
	ms.date="03/05/2015" 
	ms.author="heidist"/>

#.NET에서 첫 Azure 검색 응용 프로그램 만들기#

이 자습서에는 Visual Studio 2013 이상에서 검색 환경에 Azure 검색을 사용하는 사용자 지정 웹 검색 응용 프로그램을 빌드합니다. 이 자습서에서는 [Azure 검색 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)를 사용하여 샘플에서 사용되는 개체 및 작업에 대한 클래스를 만듭니다.

[USGS 데이터를 사용하는 Azure 검색 데모](https://azsearchdemos.codeplex.com/SourceControl/latest)에서 Codeplex의 샘플을 다운로드하여 이 자습서의 단계를 따를 수 있습니다. 샘플 응용 프로그램에서는 Washington 주에 대해 필터링된 [USGS(United States Geological Services)](http://geonames.usgs.gov/domestic/download_data.htm)의 데이터를 사용합니다. 이 데이터를 사용하여 병원 및 학교와 같은 랜드마크 빌딩뿐만 아니라 강, 호수, 산 등의 지질학적 특징에 대한 데이터를 기반으로 검색 응용 프로그램을 빌드합니다.

이 샘플을 실행하려면 Azure 검색 서비스가 있어야 합니다. 이 서비스는 [Azure 포털](https://portal.azure.com)에서 등록할 수 있습니다.

프로비전 및 서비스 가용성 확인에 대한 도움이 필요한 경우 [포털에서 서비스 만들기](../search-create-service-portal/)부터 시작할 수 있습니다. 또한 이 문서에서는 Azure 검색을 포함하는 모든 자습서 및 솔루션에서 사용되는 서비스 이름 및 관리 키를 찾는 방법도 설명합니다.

> [AZURE.TIP]빌드 오류를 방지하려면 프로젝트를 빌드하기 전에 NuGet 패키지를 업데이트하는 것이 좋습니다. 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 이 솔루션에서 사용되는 모든 패키지를 업데이트합니다.

##인덱스 작성##

1. [Azure 포털](https://portal.azure.com)에서 서비스 이름과 관리 키를 복사하여 **DataIndexer** | **App.config**에 붙여 넣습니다.
1. **DataIndexer** 프로젝트를 마우스 오른쪽 단추로 클릭하여 시작 프로젝트로 설정합니다.
1. 프로젝트를 빌드 및 실행합니다.

다음과 같은 메시지가 표시된 콘솔 창이 나타납니다.

![][1]

포털에서 xx 및 xx가 표시된 새 기능 인덱스를 볼 수 있습니다. 포털을 불러오는 데 몇 분 정도 걸리므로 결과를 보려면 몇 분 후에 화면을 새로 고쳐야 합니다.

![][2]

##응용 프로그램 빌드##


1. [Azure 포털](https://portal.azure.com)에서 서비스 이름과 관리 키를 복사하여 **SimpleSearchMVCApp** | **Web.config**에 붙여 넣습니다.
1. **SimpleSearchMVCApp** 프로젝트를 마우스 오른쪽 단추로 클릭하여 시작 프로젝트로 설정합니다.
1. 프로젝트를 빌드 및 실행합니다.

Azure 검색 서비스의 인덱스에 저장된 USG 데이터에 액세스할 수 있는 검색 상자를 제공하는 웹 페이지가 기본 브라우저에 표시됩니다.

![][3]

##USGS 데이터 검색##

USGS 데이터 집합에는 Washington 주와 관련된 레코드가 포함되어 있습니다. 빈 검색 상자에서 **검색**을 클릭하면 기본적으로 상위 50개 항목을 가져옵니다.

검색 용어를 입력하면 검색 엔진이 실행됩니다. 지역 이름을 입력해 봅니다. "Snoqualmie"는 Washington 주의 도시입니다. 또한 강, 폭포, 산길 및 주립 공원의 이름이기도 합니다.

![][4]

다음과 같은 용어를 입력해 볼 수도 있습니다.

- Seattle
- Rainier
- Seattle and Rainier
- Seattle +Rainier -Mount(Seattle이라는 도시 내에 있는 Rainier avenue 또는 Rainier club의 랜드마크에 대한 결과를 가져옴)

##코드 탐색##

.NET SDK의 기본 사항을 알아보려면 [.NET에서 Azure 검색을 사용하는 방법](../search-howto-dotnet-sdk/)에서 클라이언트 라이브러리의 가장 일반적으로 사용되는 클래스에 대한 설명을 참조하세요.

이 섹션의 나머지 부분에서는 각 프로젝트에 대한 몇 가지 사항을 알아봅니다. 적절한 경우 보다 고급 기능을 사용하는 몇 가지 다른 방법을 소개해 드리겠습니다.

**DataIndexer 프로젝트**

단순화하기 위해 데이터가 [USGS(United States Geological Services) 웹 사이트](http://geonames.usgs.gov/domestic/download_data.htm)의 데이터에서 생성된 텍스트 파일로 솔루션에 포함되어 있습니다.

데이터를 포함하는 대신 [DocumentDB용 인덱서](documentdb-search-indexer.md) 또는 [Azure SQL 데이터베이스용 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)를 사용할 수도 있습니다. 인덱서는 데이터를 Azure 검색 인덱스로 가져오므로 작성 및 유지 관리해야 하는 코드를 크게 간소화할 수 있습니다.

온-프레미스 SQL Server 데이터베이스에서 데이터를 로드할 수도 있습니다. [이 자습서](http://azure.microsoft.com/blog/2014/11/10/how-to-sync-sql-server-data-with-azure-search/)에서는 작업 방법을 보여 줍니다.

**DataIndexer** 프로그램에는 데이터를 검색 및 필터링하는 **SearchDocuments** 메서드가 포함되어 있습니다. 이 메서드는 콘솔 창에 상태 메시지를 출력하도록 지원하는 확인 단계로 존재합니다. 즉, 검색 결과와 필터 동작을 보여 줍니다. 대부분의 경우 인덱스를 만들고 로드하기 위해 작성하는 코드에는 이와 같은 메서드가 필요 없습니다.

**SimpleSearchMVCApp 프로젝트**

MVC 프로젝트는 뷰와 컨트롤러를 사용하여 입력 및 출력을 프레젠테이션 계층으로 라우팅합니다. **Index.cshtml**은 검색 결과를 렌더링하는 데 사용되는 HTML을 제공합니다. 현재는 데이터 집합의 데이터를 구성하는 간단한 테이블에 불과합니다. 프로토타입 생성 및 테스트에 유용하지만 프레젠테이션 시 쉽게 개선할 수 있습니다. 결과를 일괄 처리하고 페이지에 개수를 표시하는 방법에 대한 팁은 [Azure 검색에서 결과 페이지 매김](search-pagination-page-layout.md)을 참조하세요.

Azure 검색에 대한 연결 및 쿼리 요청 실행은 **FeatureSearch.cs** 파일에 정의됩니다.

마지막으로, .NET SDK의 가치와 간소성에 대한 확신이 아직 없는 경우 이 샘플의 원본 파일을 REST API: [Azure 검색 Adventure Works 데모](https://azuresearchadventureworksdemo.codeplex.com/) 기반의 파일과 비교해 보세요. 이 자습서에 설명된 .NET SDK 버전이 훨씬 단순하며 코드 줄이 더 적습니다.

##다음 단계##

이것은 USGS 데이터 집합을 기반으로 하는 첫 번째 Azure 검색 자습서입니다. 앞으로 이 자습서를 확장하고 사용자 지정 솔루션에서 사용할 수 있는 검색 기능을 보여 주는 새 자습서를 만들 예정입니다.

Azure 검색에 대한 약간의 배경 지식이 이미 있는 경우 이 샘플을 기반으로 suggesters(사전 입력 또는 자동 완성 쿼리), 필터 및 패싯 탐색을 시작할 수 있습니다. 또한 사용자가 결과 페이지를 차례로 탐색할 수 있도록 개수를 추가하고 문서를 일괄 처리하여 검색 결과 페이지를 개선할 수 있습니다.

Azure 검색을 처음 사용하세요? 다른 자습서를 통해 만들 수 있는 항목에 대한 이해를 높여 보세요. 더 많은 리소스를 보려면 [설명서 페이지](http://azure.microsoft.com/documentation/services/search/)를 방문하세요. [비디오 및 자습서](https://msdn.microsoft.com/library/azure/dn798933.aspx)의 링크를 통해 추가 정보를 확인할 수도 있습니다.

<!--Image references-->
[1]: ./media/search-get-started-20150228/consolemessages.png
[2]: ./media/search-get-started-20150228/portalindexstatus.png
[3]: ./media/search-get-started-20150228/usgssearchbox.png
[4]: ./media/search-get-started-20150228/snoqualmie.png
<!--HONumber=54-->