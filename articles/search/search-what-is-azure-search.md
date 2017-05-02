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
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 3720aed95706f6e8dd9a28085a6de0bc1f756a9b
ms.lasthandoff: 04/26/2017

---
# <a name="what-is-azure-search"></a>Azure 검색이란?
Azure Search는 서버 및 인프라 관리를 Microsoft에 의한 클라우드 SaaS(Search-as-a-Service) 솔루션으로, 사용자는 데이터를 채운 다음 웹 또는 모바일 응용 프로그램에 검색을 추가하는 데 사용할 수 있는 즉시 사용 가능한 서비스입니다. Azure Search를 사용하면 검색 인프라를 관리할 필요가 없고 검색 전문가가 아니라도 간단한 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 또는 [.NET SDK](search-howto-dotnet-sdk.md)를 사용하여 응용 프로그램에 강력한 검색 환경을 쉽게 추가할 수 있습니다.

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>앱 또는 사이트에서 강력한 검색 환경 포함


### <a name="full-text-search-and-text-analysis"></a>전체 텍스트 검색 및 텍스트 분석

논리 연산자, 구문 검색 연산자, 후위 연산자, 선행 연산자를 제공하는 [단순 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)을 사용하여 쿼리를 공식화할 수 있습니다. 뿐만 아니라 [Lucene 쿼리 구문](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 은 유사 항목 검색, 근접 검색, 용어 승격 및 정규식을 사용할 수 있습니다. 또한 Azure Search는 응용 프로그램에서 음성 일치를 사용하는 복잡한 검색 쿼리와 정규식을 처리할 수 있도록 [사용자 지정 어휘 분석기](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)를 지원합니다.

### <a name="language-support"></a>언어 지원

Azure Search에서는 [56개 언어](https://docs.microsoft.com/rest/api/searchservice/language-support)에 대한 어휘 분석기를 지원합니다. Azure Search는 Lucene 분석기와 Microsoft 분석기를 사용하여(Office 및 Bing에서 자연어를 처리해 온 연 수만큼 개선됨) 응용 프로그램의 검색 상자에서 텍스트를 분석하여 동사 시제, 성, 불규칙 복수 명사(예: '쥐들'과 '쥐'), 분리된 단어, 단어 분철(띄어쓰기가 없는 언어의 경우) 등을 포함한 언어별 언어를 지능적으로 처리할 수 있습니다.

### <a name="data-integration"></a>데이터 통합

JSON 데이터 구조를 밀어넣어 Azure Search 인덱스를 채울 수 있습니다. 또한 지원되는 데이터 원본에 대해 [인덱서](search-indexer-overview.md)를 통해 자동으로 Azure SQL Database, Azure DocumentDB 또는 [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)를 크롤링하여 검색 인덱스의 콘텐츠를 주 데이터 저장소와 동기화할 수 있습니다.

**문서 해독**을 사용하여 Microsoft Office, PDF 및 HTML 문서를 포함한 [주요 파일 형식을 인덱싱](search-howto-indexing-azure-blob-storage.md)할 수 있습니다.

### <a name="search-experience"></a>검색 환경

+ **제안 검색**은 자동 완성 검색 표시줄과 자동 완성 쿼리에 사용할 수 있습니다. [인덱스의 실제 문서가 제안됩니다](https://docs.microsoft.com/rest/api/searchservice/suggesters) .

+ **패싯 탐색**은 [단일 쿼리 매개 변수만으로](https://docs.microsoft.com/azure/search/search-faceted-navigation) 사용하도록 설정됩니다. Azure Search는 자기 주도형 필터링(예: 가격 범위 또는 브랜드별로 카탈로그 항목 필터링)을 위해 코드 숨김 카탈로그 목록으로 사용할 수 있는 패싯 탐색 구조를 반환합니다.

+ **필터**를 사용하여 패싯 탐색을 응용 프로그램 UI에 통합하고, 쿼리 작성 능력을 향상하고, 사용자 또는 개발자가 지정한 기준에 따라 필터링할 수 있습니다. [OData 구문](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)을 사용하여 필터를 만듭니다.

+ **적중 항목 강조 표시**[는 검색 결과의 일치하는 키워드에 시각적 서식을 적용](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)합니다. 강조 표시된 조각을 반환할 필드를 선택할 수 있습니다.

+ **간단한 점수 매기기** 는 Azure 검색의 핵심적인 장점입니다. [점수 매기기 프로필](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)은 문서 자체의 값에 대한 함수로써 관련성을 모델링할 수 있도록 허용하는 데 사용됩니다. 예를 들어 최신 제품 또는 할인 제품을 검색 결과의 더 높은 부분에 나타내려고 할 수 있습니다. 또한 따로 추적하고 저장한 고객 검색 기본 설정에 따라 개인화된 점수에 태그를 사용하여 점수 매기기 프로필을 만들 수도 있습니다.

+ **정렬** 은 인덱스 스키마를 통해 여러 필드에 제공된 후 쿼리 시 단일 검색 매개 변수를 통해 전환됩니다.

+ **정교하게 조정된 컨트롤을 사용** 하면 검색 결과를 간편하게 [페이징](search-pagination-page-layout.md) 및 제한할 수 있습니다.  

### <a name="geosearch"></a>지리적 검색

Azure Search는 지리적 위치를 지능적으로 처리하고, 필터링하고, 표시합니다. 이 기능을 통해 사용자는 지정된 위치와 검색 결과의 근접도에 따라 또는 특정 지리적 영역에 따라 데이터를 검색할 수 있습니다. [채널 9: Azure 검색 및 지리 공간적 데이터](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)동영상에서 작동 방법을 설명합니다.

### <a name="cloud-service-advantages"></a>클라우드 서비스의 장점

+ **고가용성** 은 매우 안정적인 검색 서비스 환경을 보장합니다. 규모를 적절하게 조정하면 [Azure 검색은 99.9%의 SLA를 제공](https://azure.microsoft.com/support/legal/sla/search/v1_0/)합니다.

+ **완벽하게 관리되는** 종합 솔루션인 Azure 검색은 인프라 관리가 전혀 필요 없습니다. 더 많은 문서 저장소, 더 많은 쿼리 부하 또는 둘 모두를 처리할 수 있도록 두 가지 크기를 조정하여 요구 사항에 따라 서비스를 맞춤 구성할 수 있습니다.

### <a name="monitoring-and-reporting"></a>모니터링 및 보고

+ **검색 트래픽 분석**을 [수집 및 분석](search-traffic-analytics.md)하여 사용자가 검색 상자에 입력하는 내용으로부터 정보를 얻을 수 있습니다.

+ 필요한 추가 구성 없이 포털 페이지에서 초당 쿼리 수, 대기 시간 및 제한에 대한 메트릭이 캡처되고 보고됩니다. 인덱스 및 문서 수를 쉽게 모니터링할 수 있으므로 필요에 따라 용량을 조정할 수 있습니다. 

### <a name="tools-for-prototyping-and-inspection"></a>프로토타입 및 검사용 도구

포털에서 **데이터 가져오기** 마법사를 사용하여 인덱서를 구성하고, 인덱스 디자이너를 사용하여 인덱스를 나타내고, **검색 탐색기**를 사용하여 계정의 Azure Portal에서 바로 모든 인덱스에 대해 쿼리를 실행할 수 있으므로 쿼리를 테스트하고 점수 매기기 프로필을 구체화할 수 있습니다. 인덱스를 열어 해당 스키마를 볼 수도 있습니다.

## <a name="how-it-works"></a>작동 방법
### <a name="step-1-provision-service"></a>1단계: 서비스 프로비전
[Azure Portal](https://portal.azure.com/) 또는 [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn832684.aspx)를 사용하여 Azure Search 서비스를 스핀업할 수 있습니다.

검색 서비스를 구성하는 방법에 따라 다른 Azure 검색 구독자와 공유되는 서비스의 무료 계층 또는 서비스에만 사용하는 전용 리소스를 제공하는 [유료 계층](https://azure.microsoft.com/pricing/details/search/) 을 사용합니다. 서비스를 프로비전 할 때 서비스를 호스트하는 데이터 센터의 위치도 선택합니다.

선택한 서비스 계층에 따라 1) 더 많은 쿼리 부하를 처리할 수 있도록 복제본을 추가하여 용량을 확장하고 2) 더 많은 문서를 저장할 수 있도록 파티션을 추가하여 저장소를 추가하는 두 가지 방법으로 서비스를 확장할 수 있습니다. 문서 저장소 및 쿼리 처리량을 따로 처리하면 요구 사항에 맞게 검색 서비스를 사용자 지정할 수 있습니다.

### <a name="step-2-create-index"></a>2단계: 인덱스 만들기
Azure 검색 서비스에 콘텐츠를 업로드하려면 먼저 Azure 검색 인덱스를 정의해야 합니다. 인덱스는 데이터가 보관된 데이터베이스 테이블과 비슷하며 검색 쿼리를 수용할 수 있습니다. 데이터베이스의 필드와 마찬가지로, 검색하려는 문서의 구조에 매핑할 인덱스 스키마를 정의합니다.

이러한 인덱스의 스키마는 Azure Portal에서 만들거나 [.NET SDK](search-howto-dotnet-sdk.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 사용하여 프로그래밍 방식으로 만들 수 있습니다. 인덱스를 정의한 다음 데이터를 Azure 검색 서비스에 업로드할 수 있으며 이후에는 이 서비스에서 데이터가 인덱싱됩니다.

### <a name="step-3-index-data"></a>3단계: 인덱스 데이터
인덱스의 필드 및 특성이 정의되면 인덱스에 콘텐츠를 업로드할 준비가 완료된 것입니다. 밀어넣기 또는 끌어오기 모델을 사용하여 데이터를 인덱스에 업로드할 수 있습니다.

끌어오기 모델은 요청 시 또는 예약 업데이트에 맞게 구성할 수 있는 인덱서를 통해 제공되며( [인덱서 작업(Azure 검색 서비스 REST API)](https://msdn.microsoft.com/library/azure/dn946891.aspx)참조), Azure DocumentDB, Azure SQL 데이터베이스, Azure Blob 저장소 또는 Azure VM에서 호스트되는 SQL Server에서 데이터 및 데이터 변경 내용을 쉽게 수집할 수 있습니다.

밀어넣기 모델은 SDK 또는 업데이트된 문서를 인덱스에 보내기 위해 사용되는 REST API를 통해 제공됩니다. JSON 형식을 사용하여 거의 모든 데이터 집합에서 데이터를 밀어넣을 수 있습니다. 데이터 로드에 대한 지침은 [문서 추가, 업데이트 또는 삭제](https://msdn.microsoft.com/library/azure/dn798930.aspx) 또는 [.NET SDK를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

### <a name="step-4-search"></a>4단계: 검색
Azure 검색 인덱스를 채웠으면 REST API 또는 .NET SDK와 함께 간단한 HTTP 요청을 사용하여 서비스 끝점에 [검색 쿼리를 실행](https://msdn.microsoft.com/library/azure/dn798927.aspx) 할 수 있습니다.

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

포털에서 다양한 작업을 수행할 수 있으나 Azure Search는 검색 기능을 기존 응용 프로그램에 통합하려는 개발자를 위한 것입니다. 다음과 같은 프로그래밍 인터페이스를 사용할 수 있습니다.

|플랫폼 |설명 |
|-----|------------|
|[REST (영문)](https://docs.microsoft.com/rest/api/searchservice/) | 모든 프로그래밍 플랫폼 및 언어(Xamarin, Java 및 JavaScript 포함)에서 지원하는 HTTP 명령|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API에 대한 .NET 래퍼는 C# 및 .NET Framework를 대상으로 하는 다른 관리되는 코드 언어로 효율적인 코딩 제공 |

## <a name="free-trial"></a>무료 평가판
Azure 구독자는 [무료 계층에서 서비스를 프로비전](search-create-service-portal.md)할 수 있습니다.

구독자가 아닐 경우 [Azure 계정을 무료로 개설](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)할 수 있음: 유료 Azure 서비스를 사용해볼 수 있는 크레딧을 받게 되며 크레딧을 모두 사용한 후에도 계정을 유지하고 무료 Azure 서비스(예: 웹 서비스)를 사용할 수 있습니다. 설정을 명시적으로 변경하여 결제를 요청하지 않는 한 신용 카드로 결제되지 않습니다.

또는 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있음: MSDN 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다. 

## <a name="watch-a-short-video"></a>짧은 비디오 시청

검색 엔진은 Mobile Apps, 웹 및 회사 데이터 저장소에서 일반적으로 사용되는 정보 검색 드라이버입니다. Azure Search는 대규모 상업용 웹 사이트와 유사한 검색 환경을 만들기 위한 도구를 제공합니다. 프로그램 관리자인 Liam Cavanagh씨가 제작한 이 9분짜리 비디오는 검색 엔진 통합이 앱에 주는 혜택, Azure Search의 주요 기능 및 일반적인 워크플로의 형태를 설명합니다. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3분 구간에서는 주요 기능 및 사용 사례가 나옵니다.
+ 3-4분 구간에서는 서비스 프로비전을 다룹니다. 
+ 4-6분 구간에서는 기본 제공 부동산 데이터 집합을 사용하여 인덱스를 만드는 데 사용되는 데이터 가져오기 마법사를 다룹니다.
+ 6-9분 구간에서는 검색 탐색기 및 다양한 쿼리를 다룹니다.



