---
title: "Azure Search이란? | Microsoft Docs"
description: "사용자 지정 웹 사이트, 앱 및 회사 데이터/파일 저장소에서 사용되며 검색 창의 고급 검색 기능을 포함하는 검색 엔진입니다."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: heidist
experiment_id: heidist-20170221
translationtype: Human Translation
ms.sourcegitcommit: 71694028366f48682881523c0220a4158078a76c
ms.openlocfilehash: b851f906b57225dce516d9704fdace80c44c4897
ms.lasthandoff: 02/23/2017

---
# <a name="what-is-azure-search"></a>Azure 검색이란?

Azure Search는 웹 사이트, 앱 및 회사 파일/데이터 저장소의 콘텐츠 검색을 위한 검색 창을 지원할 수 있는 지능적인 검색 동작을 갖춘 프로그래밍 가능 전용 검색 엔진을 제공합니다. 

![Azure Search 기반 검색 창](./media/search-what-is-azure-search/search-powered-by-azsearch3.png)

대부분의 웹 및 Mobile Apps에서는 안정적인 검색 환경이 필수적입니다. 자동 완성 쿼리 용어, 맞춤법 검사 및 의미상 동일하지만 형태가 다른 입력(예: "car" 및 "auto")을 기준으로 하는 검색 기능을 비롯한 전체 텍스트 검색은 해당 환경을 지원하는 기술적 복잡성에 비하면 거의 최소 크기 창에 제공됩니다. 검색 및 백 엔드 데이터 저장소 간의 확장성, 안정성 및 동기화에 대한 운영 요구 사항은 동일하게 중요합니다. 

Azure Search는 검색 및 운영 요구 사항을 충족할 수 있도록 포괄적인 기능을 제공합니다.

![일반적인 검색 기능을 제공하는 검색 창 및 사용자 지정 검색 페이지](./media/search-what-is-azure-search/search-page-callouts3.png)

## <a name="how-it-works"></a>작동 방법

Azure Search를 사용하려면 Azure 구독에서 무료 또는 유료 서비스를 프로비전하고, 검색 가능한 콘텐츠를 포함하는 인덱스를 만들고 로드한 다음, API를 호출하여 검색 요청을 실행하고 결과를 처리합니다. 전용 리소스가 필요한 경우 유료 서비스가 필요합니다.

|계층 |설명 |
|-----|------------|
|[Free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) | 신속하게 프로비전하고 학습 및 평가 시나리오에서 일반적인 소규모 워크로드에 사용될 수 있는 공유 서비스입니다. |
|[청구 가능](search-sku-tier.md) | Basic에서 Standard High Density까지 용량이 점차적으로 커지는 전용 서비스로, 다양한 가격 책정 계층에서 광범위한 배포 구성을 수용합니다.|

Azure Search는 Microsoft의 관리되는 서비스로서 클라우드에서 실행됩니다. 이 기능은 모든 응용 프로그램 플랫폼에서 사용자 지정 코드와 통합될 수 있습니다. 완전히 관리되는 서비스 및 개인 콘텐츠는 전역적으로 사용할 수 있고, 프로그래밍 가능하고, 확장성과 회복력도 뛰어납니다. 

전용 서비스는 Microsoft Azure에서 지원하는 [99.9% 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/search/v1_0/)에 따라 연중무휴로 대규모로 실행됩니다.

## <a name="how-it-compares"></a>비교 결과

여러 클라우드 서비스 공급자는 사용자 지정 앱에서 검색 창을 지원하는 기능을 갖춘 사용자 지정 검색 엔진을 제공합니다. 일부 공급자는 전체 텍스트 검색, 지리적 검색 및 특정 수준의 검색 입력 모호성을 처리하는 기능을 비롯하여 비슷한 기준 기능을 제공합니다. 일반적으로 이러한 기능은 [특수 기능](#feature-drilldown) 또는 자동 맞춤을 결정하는 API, 도구 및 관리의 전반적인 편리성 및 간편성을 나타냅니다.

다른 검색 솔루션과 비교할 때, Azure Search는 정보 검색 및 콘텐츠 탐색 작업을 주로 검색 기능에 의존하는 앱에 필요한 추가적인 기능 계층을 갖추고 있으며, Azure의 콘텐츠 저장소 및 데이터베이스에 대한 전체 텍스트 검색 워크로드에 가장 강력합니다. 

+ 인덱싱 계층에서의 Azure 데이터 통합(크롤러)
+ 중앙 관리를 위한 Azure Portal
+ Azure 규모, 안정성 및 세계 수준의 가용성
+ 56개 언어로 안정적인 전체 텍스트 검색을 수행하기 위한 분석기를 사용한 언어 및 사용자 지정 분석
+ 관련성, 패싯, 제안, 동의어, 지리적 검색 기능 등(다음에 제공)을 비롯한 검색 중심 앱에 공통적인 핵심 기능

> [!Note]
> 비 Azure 데이터 원본이 완전히 지원됩니다. 어떤 JSON 문서 컬렉션도 Azure Search 인덱스로 파이프할 수 있습니다.

Azure Search의 가장 광범위한 기능을 활용할 수 있는 사용 사례로는 온라인 카탈로그, 기간 업무 응용 프로그램 및 문서 검색 응용 프로그램이 포함됩니다.

### <a name="feature-drilldown"></a>기능 드릴다운

#### <a name="full-text-search-and-text-analysis"></a>전체 텍스트 검색 및 텍스트 분석

논리 연산자, 구문 검색 연산자, 후위 연산자, 선행 연산자를 제공하는 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)을 사용하여 쿼리를 공식화할 수 있습니다. 뿐만 아니라 [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 은 유사 항목 검색, 근접 검색, 용어 승격 및 정규식을 사용할 수 있습니다. 또한 Azure Search는 응용 프로그램에서 음성 일치를 사용하는 복잡한 검색 쿼리와 정규식을 처리할 수 있도록 [사용자 지정 어휘 분석기](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)를 지원합니다.

#### <a name="language-support"></a>언어 지원

Azure Search에서는 [56개 언어](https://docs.microsoft.com/rest/api/searchservice/language-support)에 대한 어휘 분석기를 지원합니다. Azure Search는 Lucene 분석기와 Microsoft 분석기를 사용하여(Office 및 Bing에서 자연어를 처리해 온 연 수만큼 개선됨) 응용 프로그램의 검색 상자에서 텍스트를 분석하여 동사 시제, 성, 불규칙 복수 명사(예: '쥐들'과 '쥐'), 분리된 단어, 단어 분철(띄어쓰기가 없는 언어의 경우) 등을 포함한 언어별 언어를 지능적으로 처리할 수 있습니다.

#### <a name="data-integration"></a>데이터 통합

JSON 데이터 구조를 밀어넣어 Azure Search 인덱스를 채울 수 있습니다. 또한 지원되는 데이터 원본에 대해 [인덱서](search-indexer-overview.md)를 통해 자동으로 Azure SQL Database, Azure DocumentDB 또는 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)를 크롤링하여 검색 인덱스의 콘텐츠를 주 데이터 저장소와 동기화할 수 있습니다.

**문서 해독**을 사용하여 Microsoft Office, PDF 및 HTML 문서를 포함한 [주요 파일 형식을 인덱싱](search-howto-indexing-azure-blob-storage.md)할 수 있습니다.

#### <a name="search-experience"></a>검색 환경

+ **제안 검색**은 자동 완성 검색 표시줄과 자동 완성 쿼리에 사용할 수 있습니다. [인덱스의 실제 문서가 제안됩니다](https://docs.microsoft.com/rest/api/searchservice/suggesters) .

+ **패싯 탐색**은 [단일 쿼리 매개 변수만으로](https://docs.microsoft.com/azure/search/search-faceted-navigation) 사용하도록 설정됩니다. Azure Search는 자기 주도형 필터링(예: 가격 범위 또는 브랜드별로 카탈로그 항목 필터링)을 위해 코드 숨김 카탈로그 목록으로 사용할 수 있는 패싯 탐색 구조를 반환합니다.

+ **필터**를 사용하여 패싯 탐색을 응용 프로그램 UI에 통합하고, 쿼리 작성 능력을 향상하고, 사용자 또는 개발자가 지정한 기준에 따라 필터링할 수 있습니다. [OData 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)을 사용하여 필터를 만듭니다.

+ **적중 항목 강조 표시**[는 검색 결과의 일치하는 키워드에 시각적 서식을 적용](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)합니다. 강조 표시된 조각을 반환할 필드를 선택할 수 있습니다.

+ **간단한 점수 매기기** 는 Azure 검색의 핵심적인 장점입니다. [점수 매기기 프로필](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)은 문서 자체의 값에 대한 함수로써 관련성을 모델링할 수 있도록 허용하는 데 사용됩니다. 예를 들어 최신 제품 또는 할인 제품을 검색 결과의 더 높은 부분에 나타내려고 할 수 있습니다. 또한 따로 추적하고 저장한 고객 검색 기본 설정에 따라 개인화된 점수에 태그를 사용하여 점수 매기기 프로필을 만들 수도 있습니다.

+ **정렬** 은 인덱스 스키마를 통해 여러 필드에 제공된 후 쿼리 시 단일 검색 매개 변수를 통해 전환됩니다.

+ **정교하게 조정된 컨트롤을 사용** 하면 검색 결과를 간편하게 [페이징](search-pagination-page-layout.md) 및 제한할 수 있습니다.  

#### <a name="geosearch"></a>지리적 검색

Azure Search는 지리적 위치를 지능적으로 처리하고, 필터링하고, 표시합니다. 이 기능을 통해 사용자는 지정된 위치와 검색 결과의 근접도에 따라 또는 특정 지리적 영역에 따라 데이터를 검색할 수 있습니다. [채널 9: Azure 검색 및 지리 공간적 데이터](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)동영상에서 작동 방법을 설명합니다.

#### <a name="cloud-service-advantages"></a>클라우드 서비스의 장점

+ **고가용성** 은 매우 안정적인 검색 서비스 환경을 보장합니다. 규모를 적절하게 조정하면 [Azure 검색은 99.9%의 SLA를 제공](https://azure.microsoft.com/support/legal/sla/search/v1_0/)합니다.

+ **완벽하게 관리되는** 종합 솔루션인 Azure 검색은 인프라 관리가 전혀 필요 없습니다. 더 많은 문서 저장소, 더 많은 쿼리 부하 또는 둘 모두를 처리할 수 있도록 두 가지 크기를 조정하여 요구 사항에 따라 서비스를 맞춤 구성할 수 있습니다.

#### <a name="monitoring-and-reporting"></a>모니터링 및 보고

+ **검색 트래픽 분석**을 [수집 및 분석](search-traffic-analytics.md)하여 사용자가 검색 상자에 입력하는 내용으로부터 정보를 얻을 수 있습니다.

+ 필요한 추가 구성 없이 포털 페이지에서 초당 쿼리 수, 대기 시간 및 제한에 대한 메트릭이 캡처되고 보고됩니다. 인덱스 및 문서 수를 쉽게 모니터링할 수 있으므로 필요에 따라 용량을 조정할 수 있습니다. 

#### <a name="tools-for-prototyping-and-inspection"></a>프로토타입 및 검사용 도구

포털에서 **데이터 가져오기** 마법사를 사용하여 인덱서를 구성하고, 인덱스 디자이너를 사용하여 인덱스를 나타내고, **검색 탐색기**를 사용하여 계정의 Azure Portal에서 바로 모든 인덱스에 대해 쿼리를 실행할 수 있으므로 쿼리를 테스트하고 점수 매기기 프로필을 구체화할 수 있습니다. 인덱스를 열어 해당 스키마를 볼 수도 있습니다.

## <a name="how-to-get-started"></a>시작하는 방법

무료 서비스로 시작한 후 [기본 제공 샘플 데이터를 사용하여 인덱스를 만들고 쿼리합니다](search-get-started-portal.md). 포털에서 모든 작업을 수행할 수 있으므로 코드를 작성하기 전에 Azure Search를 빠르게 사용해볼 수 있습니다.

1. Azure 구독자는 [무료 계층에서 서비스를 프로비전](search-create-service-portal.md)할 수 있습니다.

  비구독자는 [무료 Azure 계정을 열고](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) 무료 크레딧을 사용하여 유료 Azure 서비스를 사용할 수 있습니다. 크레딧이 소진되더라도 계정이 유지되므로 웹 사이트와 같은 무료 Azure 서비스를 계속 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.

  또는 [MSDN 구독자 혜택을 활성화합니다](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다. 

2. [기본 제공 부동산 샘플 데이터 집합](search-get-started-portal.md#create-index)에 대해 **데이터 가져오기** 마법사를 실행합니다.

  이 마법사는 대부분의 Azure 데이터 원본에서 인덱스를 생성하고 로드할 수 있습니다. 특별히 이 마법사에서 지원되지 않은 데이터 원본에 대해서는 코드가 필요합니다.

3. [검색 탐색기](search-get-started-portal.md#query-index)를 사용하여 인덱스를 쿼리합니다.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

포털에서 다양한 작업을 수행할 수 있으나 Azure Search는 검색 기능을 기존 응용 프로그램에 통합하려는 개발자를 위한 것입니다. 다음과 같은 프로그래밍 인터페이스를 사용할 수 있습니다.

|플랫폼 |설명 |
|-----|------------|
|[REST (영문)](https://docs.microsoft.com/rest/api/searchservice/) | 모든 프로그래밍 플랫폼 및 언어(Xamarin, Java 및 JavaScript 포함)에서 지원하는 HTTP 명령|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API에 대한 .NET 래퍼는 C# 및 .NET Framework를 대상으로 하는 다른 관리되는 코드 언어로 효율적인 코딩 제공 |

## <a name="watch-a-short-video"></a>짧은 비디오 시청

검색 엔진은 Mobile Apps, 웹 및 회사 데이터 저장소에서 일반적으로 사용되는 정보 검색 드라이버입니다. Azure Search는 대규모 상업용 웹 사이트와 유사한 검색 환경을 만들기 위한 도구를 제공합니다. 프로그램 관리자인 Liam Cavanagh씨가 제작한 이 9분짜리 비디오는 검색 엔진 통합이 앱에 주는 혜택, Azure Search의 주요 기능 및 일반적인 워크플로의 형태를 설명합니다. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3분 구간에서는 주요 기능 및 사용 사례가 나옵니다.
+ 3-4분 구간에서는 서비스 프로비전을 다룹니다. 
+ 4-6분 구간에서는 기본 제공 부동산 데이터 집합을 사용하여 인덱스를 만드는 데 사용되는 데이터 가져오기 마법사를 다룹니다.
+ 6-9분 구간에서는 검색 탐색기 및 다양한 쿼리를 다룹니다.


