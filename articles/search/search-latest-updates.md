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
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#Azure 검색에 최신 업데이트된 새로운 기능#

Azure 검색은 Microsoft Azure에서 클라우드 호스팅되는 검색 서비스입니다. 일반적으로 [2015-02-28 버전의 API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 구성을 지원하는 99.9% 가용성 서비스 수준 계약 (SLA)이 가능합니다.

##다양한 버전으로 출시된 기능

기능은 [Azure 포털](https://portal.azure.com) 내에서 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx), [.NET SDK](http://go.microsoft.com/fwlink/?LinkId=528216), 또는 서비스 대시보드를 통해 별도 혹은 통합되어 출시됩니다.

.NET 라이브러리 및 REST API에는 다양한 버전이 있습니다. 새로운 기능을 출시하는 경우 오래된 API도 그대로 운영됩니다. 본사의 버전 관리 정책에 대한 자세한 내용을 보려면 [검색 서비스 버전 관리](https://msdn.microsoft.com/library/azure/dn864560.aspx)를 방문해 주세요.


##Api-버전 2015-02-28-미리 보기
**다시 릴리스된 날짜: 2015년 9월**

이 버전은 [Azure 검색 서비스 REST API의 미리 보기 버전](search-api-2015-02-28-preview.md)에 대해 사용할 수 있는 새 [Lucene 쿼리 구문 지원](https://msdn.microsoft.com/library/azure/mt589323.aspx)을 추가합니다. 새 구문을 사용하려면 검색 문서 작업에서 `queryType`을(를) 지정해야 합니다.

또한 다음 두 기능이 모두 미리 보기에서 전환되며 이제 MSDN에서 공식 API의 일부가 됩니다. - [자연어 프로세서](search-language-support.md) - 검색, 제안 및 조회 쿼리의 POST

##.NET SDK 0.10.0-preview
**릴리스 날짜: 2015년 8월**

본 업데이트는 .NET 클라이언트 라이브러리, Microsoft.Azure.Search.dll의 2차 버전입니다. 이 버전은 .NET 클래스를 통한 새로 만들기, 관리 및 인덱서 사용에 대한 지원을 추가합니다. 추가로 Azure SQL 인덱서용 지형 지점 인덱싱이 새롭게 지원됩니다.

- [인덱서 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx)
- [데이터 소스 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.datasource.aspx)

##.NET SDK 0.9.6-preview
**릴리스 날짜: 2015년 3월 5일**

이 업데이트는 Azure 검색용 .NET SDK의 1차 공개 릴리스입니다. 업데이트에는 두 가지 네임스페이스가 담긴 클라이언트 라이브러리, Microsoft.Azure.Search.dll가 포함됩니다:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

제외된 사항:

- [인덱서](http://go.microsoft.com/fwlink/p/?LinkId=528173) (이 기능은 0.10.0-미리 보기 버전에서 제외되지 않습니다)
- [관리 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)
- [2015-02-28-미리 보기](search-api-2015-02-28-Preview.md) 기능 (현재 미리 보기 전용 기능은 Microsoft 자연어 프로세서 및 `moreLikeThis`을 구성함).

SDK의 설치 및 사용에 대한 안내를 원하시면 [.NET에서 Azure 검색 사용법](http://go.microsoft.com/fwlink/p/?LinkId=528088)을 방문해 주세요.

##Api-버전 2015-02-28-미리 보기
**릴리스 날짜: 2015년 4월 22일**

- 외부 데이터베이스와 Azure 검색 인덱스 간에 실제 필드 이름이 서로 다를 때, 이제 인덱서가 필드 할당을 제공하는 필드 매핑 구성을 지원합니다. `2015-02-28-preview` 버전의 인덱서 설명서는 [인덱서](search-api-indexers-2015-02-28-Preview.md)를 참조하세요..

- 또한 해당 미리 보기 업데이트에서 인덱서가 이제 필드 형식 변환을 지원하기 때문에 사용자가 소스 필드가 JSON 배열을 가정하여 SQL 테이블에서 스트링 필드를 검색 인덱스의 스트링 컬렉션으로 매핑할 수 있게 됩니다.

**릴리스 날짜: 2015년 3월 5일**

- [Microsoft 자연어 프로세서](search-api-2015-02-28-Preview.md)는 더 많은 언어를 지원하며 Office 및 Bing가 지원하는 모든 언어를 포괄적으로 제어합니다.

- [moreLikeThis=](search-api-2015-02-28-Preview.md)는 `search=`와 상호 배타적이며 대체 쿼리 실행 경로를 트리거하는 검색 매개 변수입니다. 검색 용어 입력에 따라 `search=`로 전체 텍스트를 검색하는 대신, `moreLikeThis=`가 검색할 수 있는 해당 필드를 비교함으로써 지정된 문서와 유사한 문서를 찾습니다.

##Api-버전 2015-02-28
**릴리스 날짜: 2015년 3월 5일**

- [인덱서](http://go.microsoft.com/fwlink/p/?LinkID=528210)는 Azure SQL 데이터베이스, Azure DocumentDB 및 Azure VM에서 SQL Server에 있는 데이터 소스의 인덱싱을 크게 단순화하는 새로운 기능입니다.

- 접사 일치에 대한 지원을 추가함으로써 [서제스터](https://msdn.microsoft.com/library/azure/dn798936.aspx)는 (접두사를 일치시킨) 이전 구현보다 더 제한적인 자동 완성 쿼리 지원을 대체합니다. 이를 구현하면 용어의 어디에서든 일치 항목을 찾고 유사 항목 일치를 지원할 수 있습니다.

- [태그 가속](http://go.microsoft.com/fwlink/p/?LinkId=528212)으로 점수 매기기 프로필용 새로운 시나리오가 가능해 집니다. 특히, 개인 설정된 정보를 기반으로 (쇼핑 기본 설정과 같은) 지속된 데이터를 활용하여 개별 사용자에 대한 검색 결과를 높일 수 있습니다.

이런 기능을 모두 설명하는 Azure 블로그에서 서비스 공지를 보려면 [Azure 검색이 이제 일반적으로 사용 가능합니다](http://go.microsoft.com/fwlink/p/?LinkId=528211)에 방문해 주세요.

##Api-버전 2014-10-20-미리 보기
**릴리스 날짜: 2014년 11월, 2015년 1월**

- Lucene로 배포된 사용자 지정 언어 분석기에서 다국어 지원을 제공하기 위해 [Lucene 언어 분석기](search-api-2014-10-20-preview.md)가 추가되었습니다. 

- [Azure 관리 포털](https://portal.azure.com)에서 점수 매기기 프로필을 포함하여 인덱스 작성을 위해 도구 지원이 도입되었습니다.

##Api-버전 2014-07-31-미리 보기
**릴리스 날짜: 2014년 8월 21일**

해당 버전은 다음과 같은 핵심 기능을 제공하여 Azure 검색을 위한 미리 보기를 공개적으로 릴리스했습니다:

- 인덱스 및 문서 작업에 대한 REST API입니다. 이 API 버전은 대부분 2015-02-28 버전 그대로 유지됩니다. `2014-07-31-Preview` 버전에 대한 설명서는 [Azure 검색 서비스 REST API 버전 2014-07-31](search-api-2014-07-31-preview.md)에서 찾을 수 있습니다.

- 튜닝용 점수 매기기 프로필의 검색 결과입니다. 점수 매기기 프로필은 검색 점수를 계산 하는데 사용되는 기준을 추가합니다. 해당 기능에 대한 설명서는 [Azure 검색 서비스 점수 매기기 프로필 REST API 버전 2014-07-31](search-api-scoring-profiles-2014-07-31-preview.md)에서 찾을 수 있습니다.

- 지리 공간적 지원은 처음부터 Azure 검색의 일부인 `Edm.GeographyPoint` 데이터 형식을 통해 제공되며 시작부터 사용 가능합니다.

- [Azure 관리 포털](https://portal.azure.com)의 미리보기 버전에서 대비합니다. Azure 검색은 새 포털에서 사용할 수 있는 몇 가지 서비스 중 하나였습니다.

##관리 api-version 2015-08-19
**릴리스 날짜: 2015년 9월 11일**

[관리 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx)에는 다음 업데이트가 포함됩니다.

- checkNameAvailability는 지정된 서비스 이름이 이미 사용 중인지를 확인합니다.
- 복제본 범위가 이전에는 1-6이었지만 이제 1-12입니다.
- SKU 속성이 속성 모음에서 서비스 페이로드의 최상위 수준으로 이동되었습니다.
- 검색 서비스 만들기 작업의 응답 본문이 SKU 설정의 위치 변경을 반영하도록 업데이트되었습니다.

##관리 api-2015-02-28 버전
**릴리스 날짜: 2015년 3월 5일**

[관리 REST API](search-management-api-2014-02-28.md)가 Azure 검색을 일반적으로 사용할 수 있는 릴리스에 속한 관리 API의 1차 버전을 표시합니다. 이전의 미리 보기 및 해당 버전 간의 기능에는 차이가 없습니다.

##관리 api-버전 2014-07-31-미리 보기
**릴리스 날짜: 2014 10월**

프로그램 상 서비스 관리를 지원하기 위해 [관리 REST API](search-management-api-2014-07-31-preview.md)의 미리 보기 릴리스가 추가되었습니다. 서비스 REST API에서 독립적인 버전입니다.


 

<!---HONumber=Nov15_HO3-->