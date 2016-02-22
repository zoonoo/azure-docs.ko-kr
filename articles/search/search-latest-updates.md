<properties 
	pageTitle="Azure 검색의 최신 업데이트의 새로운 기능 | Microsoft Azure| 호스트된 클라우드 검색 서비스" 
	description="서비스에 최신 업데이트를 설명하는 Azure 검색용 릴리스 정보" 
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
	ms.date="02/10/2016" 
	ms.author="heidist"/>

#Azure 검색에 최신 업데이트된 새로운 기능#

Azure 검색은 Microsoft Azure에서 클라우드 호스팅되는 검색 서비스입니다. [2015-02-28 버전의 검색 서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 또는 [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 구성에 대한 99.9% 가용성 SLA(서비스 수준 계약)가 제공되어 일반적으로 사용 가능합니다.

##2016년의 새로운 기능

기능|릴리스 날짜|상태|세부 정보
-------|--------|------|-------
[.NET SDK 1.1](https://msdn.microsoft.com/library/azure/dn951165.aspx)|2016년 2월|GA|일반적으로 .NET 클라이언트 라이브러리 `Microsoft.Azure.Search.dll`에서 처음 사용할 수 있는 릴리스입니다. 이 버전은 주요 변경 사항을 소개합니다. 마이그레이션 참고 자료는 [Azure 검색 .NET SDK 버전 1.1로 업그레이드](search-dotnet-sdk-migration.md)를 참조하세요.
[Lucene 쿼리 구문 지원](https://msdn.microsoft.com/library/azure/mt589323.aspx)|2016년 2월|[GA](search-api-2015-02-28.md)|이제 Lucene 쿼리 구문을 REST API 및 .NET SDK에서 일반적으로 사용할 수 있습니다. REST API에서 `queryType` 매개 변수를 `full`로 설정하고 .NET SDK에서 `SearchParameters.QueryType` 속성을 `QueryType.Full`로 지정하여 Lucene 구문을 사용할 수 있도록 설정합니다.
[사용자 지정 분석기](https://azure.microsoft.com/blog/custom-analyzers-in-azure-search/)|2016년 1월|[미리 보기](search-api-2015-02-28-preview.md)|토크나이저 및 토큰 파일의 사용자 정의 구성입니다. MSDN의 [Azure 검색의 분석](https://msdn.microsoft.com/library/azure/mt605304.aspx)을 참조하세요.
[Azure Blob 저장소 인덱서](search-howto-indexing-azure-blob-storage.md)|2016년 1월|[미리 보기](search-api-2015-02-28-preview.md)|PDF, Office 문서, XML, HTML 또는 비디오 및 오디오 파일도 Azure 검색 인덱스에 병합되거나 수집될 수 있습니다.
[검색 탐색기](search-explorer.md)|2016년 1월|[포털](https://portal.azure.com)|인덱스에 대한 임시 쿼리를 위한 기본 제공 쿼리 도구입니다.
[Azure 검색용 Power BI 콘텐츠 팩](http://blogs.msdn.com/b/powerbi/archive/2016/01/19/visualizing-azure-search-data-with-power-bi.aspx)|2016년 1월|도구|새로운 Azure 검색용 Power BI 콘텐츠 팩을 사용한 서비스 데이터 가상화 및 분석입니다. 검색 분석을 통해 사용할 수 있습니다.
[검색 분석](https://azure.microsoft.com/blog/analyzing-your-azure-search-traffic/)|2016년 1월|포털|사용자의 검색 작업에 대한 통찰력을 위한 데이터 수집이 가능합니다.

##2015년의 새로운 기능

기능|릴리스 날짜|상태|세부 정보
-------|--------|------|-------
Lucene 언어 분석기|2015년 10월|GA|이 기능은 이제 GA이며 서비스 REST API 및 .NET SDK에서 사용할 수 있습니다.
[Microsoft 자연어 프로세서](search-api-2015-02-28-Preview.md)|2015년 10월|GA|이 기능은 이제 GA이며 서비스 REST API 및 .NET SDK에서 사용할 수 있습니다. 
[Lucene 쿼리 구문 지원](https://msdn.microsoft.com/library/azure/mt589323.aspx)|2015년 9월|[미리 보기](search-api-2015-02-28-preview.md)|Lucene 쿼리 분석기를 추가합니다. 새 구문을 사용하려면 검색 문서 작업에서 `queryType`을 지정해야 합니다.
[자연어 프로세서](search-language-support.md)|2015년 9월|[미리 보기](search-api-2015-02-28-preview.md)|Microsoft 언어 프로세서가 추가되어 전반적인 언어의 수가 증가되고 다른 프로세서에 대한 대체 구현이 제공됩니다.
검색, 제안 및 조회 쿼리의 POST|2015년 9월|[미리 보기](search-api-2015-02-28-preview.md)|서비스 REST API에 적용됩니다.
[관리 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015년 9월|GA|관리 REST API의 두 번째 버전입니다. 지정된 서비스 이름이 이미 사용 중인지, 복제본 범위가 이전에는 1-6이었지만 이제 1-12인지, SKU 속성이 속성 모음에서 서비스 페이로드의 최상위 수준으로 이동되었는지, 검색 서비스 만들기 작업의 응답 본문이 SKU 설정의 위치 변경을 반영하도록 업데이트되었는지 여부에 대한 checkNameAvailability 확인을 포함합니다.
.NET SDK 0.10.0-preview|2015년 8월|미리 보기|본 업데이트는 .NET 클라이언트 라이브러리, Microsoft.Azure.Search.dll의 2차 버전입니다. 이 버전은 .NET 클래스를 통한 [데이터 원본 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx) 및 [인덱서 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) 만들기, 관리 및 사용에 대한 지원을 추가합니다. 추가로 Azure SQL 인덱서용 지형 지점 인덱싱이 새롭게 지원됩니다.
필드 매핑 구성|2015년 4월|미리 보기|외부 데이터베이스와 Azure 검색 인덱스 간에 실제 필드 이름이 서로 다를 때, 이제 인덱서가 필드 할당을 제공하는 필드 매핑 구성을 지원합니다. `2015-02-28-preview` 버전의 인덱서 설명서는 [인덱서](search-api-indexers-2015-02-28-Preview.md)를 참조하세요..
필드 형식 변환|2015년 4월|미리 보기|인덱서가 이제 필드 형식 변환을 지원하기 때문에 사용자가 소스 필드가 JSON 배열을 가정하여 SQL 테이블에서 스트링 필드를 검색 인덱스의 스트링 컬렉션으로 매핑할 수 있게 됩니다.
[서비스 REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015년 3월|GA|Azure 검색 서비스 REST API의 첫 번째 일반 공급 버전입니다. 이 버전은 이전 기능을 포함합니다. 또한 [인덱서](http://go.microsoft.com/fwlink/p/?LinkID=528210)를 포함합니다. 접사 일치에 대한 지원을 추가함으로써 [서제스터](https://msdn.microsoft.com/library/azure/dn798936.aspx)는 (접두사를 일치시킨) 이전 구현보다 더 제한적인 자동 완성 쿼리 지원을 대체합니다. 이를 구현하면 용어의 어디에서든 일치 항목을 찾고 유사 항목 일치를 지원할 수 있습니다. [태그 가속](http://go.microsoft.com/fwlink/p/?LinkId=528212)으로 점수 매기기 프로필용 새로운 시나리오가 가능해 집니다. 특히, 개인 설정된 정보를 기반으로 (쇼핑 기본 설정과 같은) 지속된 데이터를 활용하여 개별 사용자에 대한 검색 결과를 높일 수 있습니다. 
.NET SDK 0.9.6-preview|2015년 3월|미리 보기|이 업데이트는 Azure 검색용 .NET SDK의 1차 공개 릴리스입니다. 두 가지 네임스페이스([Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx) 및 [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx))가 있는 클라이언트 라이브러리, Microsoft.Azure.Search.dll이 포함됩니다.
관리 REST API|2015년 3월|GA|Azure 검색의 일반 공급 릴리스에 속하는 관리 REST API의 첫 번째 버전입니다. 이전의 미리 보기 및 해당 버전 간의 기능에는 차이가 없습니다.
[Microsoft 자연어 프로세서](search-api-2015-02-28-Preview.md)|2015년 3월|미리 보기|Office 및 Bing에 지원되는 모든 언어에 더 많은 언어와 포괄적인 형태소 분석을 추가합니다.
[moreLikeThis=](search-api-2015-02-28-Preview.md)|2015년 3월|미리 보기|`search=`와 상호 배타적이며 대체 쿼리 실행 경로를 트리거하는 검색 매개 변수입니다. 검색 용어 입력에 따라 `search=`로 전체 텍스트를 검색하는 대신, `moreLikeThis=`가 검색할 수 있는 해당 필드를 비교함으로써 지정된 문서와 유사한 문서를 찾습니다.

##2014년의 새로운 기능

기능|릴리스 날짜|상태|세부 정보
-------|--------|------|-------
Lucene 언어 분석기|2014년 11월|미리 보기|Lucene로 배포된 사용자 지정 언어 분석기에서 다국어 지원을 제공하기 위해 추가되었습니다. 
인덱스 작성을 위한 포털 지원 도입|2014년 11월|[포털](https://portal.azure.com)|인덱스, 분석기 및 점수 매기기 프로필을 포털에서 생성할 수 있습니다.
관리 api-버전 2014-07-31-미리 보기|2014년 10월|미리 보기|관리 REST API의 첫 번째 공개 미리 보기 릴리스입니다. 이 api-version 에 대한 설명서는 요청에 따라 제공됩니다.
검색 서비스 REST API|2014년 8월|미리 보기|REST API의 첫 번째 공개 미리 보기 릴리스(api-version-2014-07-31-Preview)입니다. 인덱스 및 문서 작업용, 검색 결과, 지리 공간적 지원 튜닝을 위한 점수 매기기 프로필용 REST API입니다. 이 릴리스는 Azure 포털에서 서비스 프로비전을 지원합니다. 이 api-version 에 대한 설명서는 요청에 따라 제공됩니다. 서비스 REST API에서 독립적인 버전입니다.

##다양한 버전으로 출시된 기능

기능은 [Azure 포털](https://portal.azure.com) 내에서 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx), [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216) 또는 서비스 대시보드를 통해 별도로 또는 통합되어 출시됩니다. .NET 라이브러리 및 REST API에는 다양한 버전이 있습니다. 새로운 기능을 출시하는 경우 오래된 API도 그대로 운영됩니다. 본사의 버전 관리 정책에 대한 자세한 내용을 보려면 [검색 서비스 버전 관리](https://msdn.microsoft.com/library/azure/dn864560.aspx)를 방문해 주세요.

미리 보기 또는 일반 공급(GA) 기능은 동일한 범주의 API 버전에 연결됩니다.

- 미리 보기 기능은 실험적이며 GA로 조정되기 전에 변경되거나 중단될 수 있습니다. 미리 보기 기능은 [REST API 미리 보기 버전](search-api-2015-02-28-preview.md)에서 언제나 사용할 수 있으며 경우에 따라 [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216)에서 사용할 수 있습니다. 기능 설명서에는 문제가 있는 기능에 액세스하는 방법이 항상 설명됩니다.
- GA 기능은 안정적이며 변경될 가능성이 적습니다. GA 기능이 변경되는 경우에는 주요 변경 내용으로 발표됩니다.

전적으로 포털 또는 툴에 기반하는 기능은 시간이 지나면 변경될 수 있으며 미리 보기 또는 GA로 분류되지 않습니다.














 

<!---HONumber=AcomDC_0211_2016-->