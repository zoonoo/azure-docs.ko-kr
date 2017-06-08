---
title: "Azure Search이란? | Microsoft Docs"
description: "Azure 검색은 완벽하게 관리되는 호스트된 클라우드 검색 서비스입니다. 이 기능 개요에 대해 자세히 알아보세요."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: db227bfea10255322c090e68b197cfb2dd1cf15b
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="what-is-azure-search"></a>Azure 검색이란?
Azure Search는 서버 및 인프라 관리를 Microsoft에 의한 클라우드 SaaS(Search-as-a-Service) 솔루션으로, 사용자는 데이터를 채운 다음 웹 또는 모바일 응용 프로그램에 검색을 추가하는 데 사용할 수 있는 즉시 사용 가능한 서비스입니다. Azure Search를 사용하면 검색 인프라를 관리할 필요가 없고 검색 전문가가 아니라도 간단한 [REST API](/rest/api/searchservice/) 또는 [.NET SDK](search-howto-dotnet-sdk.md)를 사용하여 응용 프로그램에 강력한 검색 환경을 쉽게 추가할 수 있습니다.

<a name="feature-drilldown"></a>

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>앱 또는 사이트에서 강력한 검색 환경 포함 

Azure Search의 기능에 대해 알아봅니다.

### <a name="full-text-search-and-text-analysis"></a>전체 텍스트 검색 및 텍스트 분석

[전체 텍스트 검색](https://en.wikipedia.org/wiki/Full_text_search)은 대부분의 검색 기반 앱에서 기본적으로 사용되는 검색 방식입니다. Azure Search에서 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)을 사용하여 쿼리를 공식화할 수 있습니다. 뿐만 아니라 [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 은 유사 항목 검색, 근접 검색, 용어 승격 및 정규식을 사용할 수 있습니다. 또한 Azure Search는 응용 프로그램에서 음성 일치를 사용하는 복잡한 검색 쿼리와 정규식을 처리할 수 있도록 [사용자 지정 어휘 분석기](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)를 지원합니다.

### <a name="language-support"></a>언어 지원

Azure Search에서는 [56개 언어](https://docs.microsoft.com/rest/api/searchservice/language-support)에 대한 어휘 분석기를 지원합니다. Azure Search는 Lucene 분석기와 Microsoft 분석기를 사용하여(Office 및 Bing에서 자연어를 처리해 온 연 수만큼 개선됨) 응용 프로그램의 검색 상자에서 텍스트를 분석하여 동사 시제, 성, 불규칙 복수 명사(예: '쥐들'과 '쥐'), 분리된 단어, 단어 분철(띄어쓰기가 없는 언어의 경우) 등을 포함한 언어별 언어를 지능적으로 처리할 수 있습니다.

### <a name="data-integration"></a>데이터 통합

JSON 데이터 구조를 밀어넣어 Azure Search 인덱스를 채울 수 있습니다. 또한 지원되는 데이터 원본에 대해 [인덱서](search-indexer-overview.md)를 통해 자동으로 [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-documentdb.md) 또는 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)를 크롤링하여 검색 인덱스의 콘텐츠를 주 데이터 저장소와 동기화할 수 있습니다.

*문서 해독*을 사용하여 Microsoft Office, PDF 및 HTML 문서를 포함한 [주요 파일 형식을 인덱싱](search-howto-indexing-azure-blob-storage.md)할 수 있습니다.

### <a name="search-experience"></a>검색 환경

+ **제안 검색**은 자동 완성 검색 표시줄과 자동 완성 쿼리에 사용할 수 있습니다. [인덱스의 실제 문서가 제안됩니다](https://docs.microsoft.com/rest/api/searchservice/suggesters) .

+ **패싯 탐색**은 [단일 쿼리 매개 변수로](https://docs.microsoft.com/azure/search/search-faceted-navigation) 사용하도록 설정됩니다. Azure Search는 자기 주도형 필터링(예: 가격 범위 또는 브랜드별로 카탈로그 항목 필터링)을 위해 코드 숨김 카탈로그 목록으로 사용할 수 있는 패싯 탐색 구조를 반환합니다.

+ **필터**를 사용하여 패싯 탐색을 응용 프로그램 UI에 통합하고, 쿼리 작성 능력을 향상하고, 사용자 또는 개발자가 지정한 기준에 따라 필터링할 수 있습니다. [OData 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)을 사용하여 필터를 만듭니다.

+ **적중 항목 강조 표시**[는 검색 결과의 일치하는 키워드에 시각적 서식을 적용](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)합니다. 강조 표시된 조각을 반환할 필드를 선택할 수 있습니다.

+ **간단한 점수 매기기** 는 Azure 검색의 핵심적인 장점입니다. [점수 매기기 프로필](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)은 문서 자체의 값에 대한 함수로써 관련성을 모델링할 수 있도록 허용하는 데 사용됩니다. 예를 들어 최신 제품 또는 할인 제품을 검색 결과의 더 높은 부분에 나타내려고 할 수 있습니다. 또한 따로 추적하고 저장한 고객 검색 기본 설정에 따라 개인화된 점수에 태그를 사용하여 점수 매기기 프로필을 만들 수도 있습니다.

+ **정렬** 은 인덱스 스키마를 통해 여러 필드에 제공된 후 쿼리 시 단일 검색 매개 변수를 통해 전환됩니다.

+ **정교하게 조정된 컨트롤을 사용** 하면 검색 결과를 간편하게 [페이징](search-pagination-page-layout.md) 및 제한할 수 있습니다.  

### <a name="geosearch"></a>지리적 검색

Azure Search는 지리적 위치를 지능적으로 처리하고, 필터링하고, 표시합니다. 이 기능을 통해 사용자는 실제 위치에 대한 검색 결과의 근접도에 따라 데이터를 검색할 수 있습니다. [채널 9: Azure 검색 및 지리 공간적 데이터](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)동영상에서 작동 방법을 설명합니다.

### <a name="monitoring-and-reporting"></a>모니터링 및 보고

+ **검색 트래픽 분석**을 [수집 및 분석](search-traffic-analytics.md)하여 사용자가 검색 상자에 입력하는 내용으로부터 정보를 얻을 수 있습니다.

+ 필요한 추가 구성 없이 포털 페이지에서 초당 쿼리 수, 대기 시간 및 제한에 대한 메트릭이 캡처되고 보고됩니다. 인덱스 및 문서 수를 쉽게 모니터링할 수 있으므로 필요에 따라 용량을 조정할 수 있습니다. 

### <a name="tools-for-prototyping-and-inspection"></a>프로토타입 및 검사용 도구

포털에서 **데이터 가져오기** 마법사를 사용하여 인덱서를 구성하고, 인덱스 디자이너를 사용하여 인덱스를 나타내고, **검색 탐색기**를 사용하여 쿼리를 테스트하고 점수 매기기 프로필을 구체화할 수 있습니다. 인덱스를 열어 해당 스키마를 볼 수도 있습니다.

<a name="cloud-service-advantage"></a>

### <a name="cloud-service-advantages"></a>클라우드 서비스의 장점

+ **고가용성** 은 매우 안정적인 검색 서비스 환경을 보장합니다. 규모를 적절하게 조정하면 [Azure 검색은 99.9%의 SLA를 제공](https://azure.microsoft.com/support/legal/sla/search/v1_0/)합니다.

+ **완벽하게 관리 및 확장되는** 종합 솔루션인 Azure Search는 인프라 관리가 전혀 필요 없습니다. 더 많은 문서 저장소, 더 많은 쿼리 부하 또는 둘 모두를 처리할 수 있도록 두 가지 크기를 조정하여 요구 사항에 따라 서비스를 맞춤 구성할 수 있습니다.

## <a name="how-it-works"></a>작동 방법
### <a name="step-1-provision-service"></a>1단계: 서비스 프로비전
[Azure Portal](https://portal.azure.com/) 또는 [Azure 리소스 관리 API](/rest/api/searchmanagement/)에서 Azure Search 서비스를 스핀업할 수 있습니다. 다른 구독자와 공유되는 무료 서비스 또는 서비스에만 사용되는 전용 리소스를 제공하는 [유료 계층](https://azure.microsoft.com/pricing/details/search/) 중에서 선택할 수 있습니다.

유료 계층의 경우 다음 2가지 차원에서 서비스를 확장할 수 있습니다. 

- 복제본을 추가하여 과도한 쿼리 부하를 처리하도록 용량을 늘립니다.   
- 파티션을 추가하여 더 많은 문서를 위해 저장소를 늘립니다. 

문서 저장소 및 쿼리 처리량을 별도로 처리하여 프로덕션 요구에 따라 리소스 관리를 조정할 수 있습니다.

### <a name="step-2-create-index"></a>2단계: 인덱스 만들기
검색 가능한 콘텐츠를 업로드하려면 먼저 Azure Search 인덱스를 정의해야 합니다. 인덱스는 데이터가 보관된 데이터베이스 테이블과 비슷하며 검색 쿼리를 수용할 수 있습니다. 데이터베이스의 필드와 마찬가지로, 매핑할 인덱스 스키마를 정의하여 검색하려는 문서의 구조를 반영합니다.

스키마는 Azure Portal에서 만들거나 [.NET SDK](search-howto-dotnet-sdk.md) 또는 [REST API](/rest/api/searchservice/)를 사용하여 프로그래밍 방식으로 만들 수 있습니다.

### <a name="step-3-index-data"></a>3단계: 인덱스 데이터
인덱스를 정의했으면 콘텐츠를 업로드할 준비가 된 것입니다. 밀어넣기 또는 끌어오기 모델을 사용할 수 있습니다.

끌어오기 모델은 외부 데이터 원본에서 데이터를 검색합니다. 이 방식은 데이터에 연결, 데이터 읽기 및 데이터 직렬화 등의 데이터 수집 측면을 능률화하고 자동화하는 *인덱서*를 통해 지원됩니다. [인덱서](/rest/api/searchservice/Indexer-operations)는 Azure Cosmos DB, Azure SQL Database, Azure Blob Storage 및 Azure VM에 호스트된 SQL Server에서 사용할 수 있습니다. 필요 시 또는 예약된 데이터 새로 고침을 위해 인덱서를 구성할 수 있습니다.

밀어넣기 모델은 SDK 또는 업데이트된 문서를 인덱스에 보내기 위해 사용되는 REST API를 통해 제공됩니다. JSON 형식을 사용하여 거의 모든 데이터 집합에서 데이터를 밀어넣을 수 있습니다. 데이터 로드에 대한 지침은 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents) 또는 [.NET SDK를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

### <a name="step-4-search"></a>4단계: 검색
인덱스를 채운 후에 REST API 또는 .NET SDK와 함께 간단한 HTTP 요청을 사용하여 서비스 끝점에 [검색 쿼리를 실행](/rest/api/searchservice/Search-Documents) 할 수 있습니다.

## <a name="how-it-compares"></a>비교 결과

고객은 [Azure Search의 전체 텍스트 검색](search-lucene-query-architecture.md)이 데이터베이스 제품의 [전체 텍스트 검색](https://en.wikipedia.org/wiki/Full_text_search)과 어떻게 다른지 종종 질문합니다. Azure Search 언어 기능은 Lucene 쿼리, Lucene 및 Microsoft의 언어 분석기, 음성 또는 기타 특수한 입력에 대한 사용자 지정 분석기 및 검색 인덱스의 여러 원본 데이터를 병합하는 기능의 지원을 통해 훨씬 풍부하고 유연한 기능을 제공한다고 말할 수 있습니다. 

또 다른 차별적 요인은 검색 솔루션이 전체 검색 환경을 좌우한다는 것입니다. 예를 들어 결과의 사용자 지정 점수 매기기, 자기 주도형 필터링을 위한 패싯 탐색, 적중 항목 강조 표시 및 자동 완성 쿼리 추천 단어 등의 기능이 필요할 수 있습니다. 

쿼리 활동을 모니터링 및 이해하기 위한 도구도 검색 솔루션을 결정할 때 중요하게 고려될 수 있습니다. 예를 들어 Azure Search는 클릭률, 상위 검색, 클릭 없는 검색 등의 메트릭에 대해 [트래픽 분석 검색](search-traffic-analytics.md)을 지원합니다. 검색이 추가 기능으로 제공되는 제품에서는 이러한 기능을 찾기 어렵습니다.

리소스 사용률도 고려됩니다. 자연어 검색은 종종 계산 집약적입니다. 일부 고객은 Azure Search를 통해 검색 작업을 수행하여 트랜잭션 처리를 위한 컴퓨터 리소스를 유지하기도 합니다. 검색 과정을 외부에서 진행하면 규모를 쿼리 볼륨에 맞게 쉽게 조정할 수 있습니다.

전용 검색을 사용하기로 결정한 경우 클라우드 서비스를 사용할지 또는 온-프레미스 서버를 사용할지를 결정해야 합니다. 클라우드 서비스는 [최소한의 오버헤드 및 유지 관리, 규모 조정이 가능한 턴키 솔루션](#cloud-service-advantage)을 원하는 경우에 적합합니다.

클라우드 패러다임 내에서는 일부 공급자가 전체 텍스트 검색, 지리적 검색 및 특정 수준의 검색 입력 모호성을 처리하는 기능을 비롯하여 비슷한 기준 기능을 제공합니다. 일반적으로 이러한 기능은 [특수 기능](#feature-drilldown) 또는 자동 맞춤을 결정하는 API, 도구 및 관리의 전반적인 편리성 및 간편성을 나타냅니다.

클라우드 공급자 중에서 Azure Search는 정보 검색 및 콘텐츠 탐색 작업을 주로 검색 기능에 의존하는 앱을 위한 Azure의 콘텐츠 저장소 및 데이터베이스에 대한 전체 텍스트 검색 워크로드에 가장 강력합니다. 주요 장점은 다음과 같습니다.

+ 인덱싱 계층에서의 Azure 데이터 통합(크롤러)
+ 중앙 관리를 위한 Azure Portal
+ Azure 규모, 안정성 및 세계 수준의 가용성
+ 56개 언어로 안정적인 전체 텍스트 검색을 수행하기 위한 분석기를 사용한 언어 및 사용자 지정 분석
+ [검색 중심 앱에 공통적인 핵심 기능](#feature-drilldown): 점수 매기기, 패싯, 제안, 동의어, 지리적 검색 기능 등

> [!Note]
> 분명히 말해서, Azure 이외 데이터 원본은 완전히 지원되지만 이 경우 인덱서가 아닌 좀 더 코드 집약적인 푸시 방법이 사용됩니다. API를 사용하면 어떤 JSON 문서 컬렉션도 Azure Search 인덱스로 파이프할 수 있습니다.

Azure Search의 가장 광범위한 기능을 활용할 수 있는 고객 사용 사례로는 온라인 카탈로그, 기간 업무 응용 프로그램 및 문서 검색 응용 프로그램이 포함됩니다.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

포털에서 다양한 작업을 수행할 수 있으나 Azure Search는 검색 기능을 기존 응용 프로그램에 통합하려는 개발자를 위한 것입니다. 다음과 같은 프로그래밍 인터페이스를 사용할 수 있습니다.

|플랫폼 |설명 |
|-----|------------|
|[REST (영문)](/rest/api/searchservice/) | 모든 프로그래밍 플랫폼 및 언어(Xamarin, Java 및 JavaScript 포함)에서 지원하는 HTTP 명령|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API에 대한 .NET 래퍼는 C# 및 .NET Framework를 대상으로 하는 다른 관리되는 코드 언어로 효율적인 코딩 제공 |

## <a name="free-trial"></a>무료 평가판
Azure 구독자는 [무료 계층에서 서비스를 프로비전](search-create-service-portal.md)할 수 있습니다.

구독자가 아닌 경우 [Azure 계정을 무료로 개설](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있습니다. 유료 Azure 서비스를 사용해볼 수 있는 크레딧이 제공됩니다. 크레딧이 소진되더라도 계정이 유지되므로 [무료 Azure 서비스](https://azure.microsoft.com/free/)를 계속 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.

또는 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음: MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다. 

## <a name="watch-a-short-video"></a>짧은 비디오 시청

검색 엔진은 Mobile Apps, 웹 및 회사 데이터 저장소에서 일반적으로 사용되는 정보 검색 드라이버입니다. Azure Search는 대규모 상업용 웹 사이트와 유사한 검색 환경을 만들기 위한 도구를 제공합니다.

프로그램 관리자인 Liam Cavanagh가 제공하는 이 9분짜리 비디오에서 검색 엔진을 통합할 때 앱이 얻는 이점을 알아보세요. 짧은 데모에 Azure Serach의 핵심 기능과 일반적인 워크플로가 제시됩니다. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3분 구간에서는 주요 기능 및 사용 사례가 나옵니다.
+ 3-4분 구간에서는 서비스 프로비전을 다룹니다. 
+ 4-6분 구간에서는 기본 제공 부동산 데이터 집합을 사용하여 인덱스를 만드는 데 사용되는 데이터 가져오기 마법사를 다룹니다.
+ 6-9분 구간에서는 검색 탐색기 및 다양한 쿼리를 다룹니다.



