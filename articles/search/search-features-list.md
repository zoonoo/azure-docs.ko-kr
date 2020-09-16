---
title: 기능 개요
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 기능 범주를 탐색 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 21f5a9f58acbaf3d0444d7da10ad489ade99cd76
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604192"
---
# <a name="features-of-azure-cognitive-search"></a>Azure Cognitive Search의 기능

Azure Cognitive Search는 전체 텍스트 검색 엔진, 검색 인덱스의 영구 저장소, 인덱싱 중에 사용 되는 통합 AI를 사용 하 여 더 많은 텍스트와 구조를 추출 하 고 Api 및 도구를 제공 합니다. 다음 표에는 범주별 기능이 요약 되어 있습니다. Cognitive Search 다른 검색 기술과 비교 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search 란?](search-what-is-azure-search.md)을 참조 하세요.

## <a name="indexing-features"></a>인덱싱 기능

| 범주&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 기능 |
|-------------------|----------|
| 데이터 원본 | JSON 문서로 제출 된 경우 검색 인덱스는 모든 원본의 텍스트를 허용할 수 있습니다. <br/><br/> [**인덱서**](search-indexer-overview.md) 는 지원 되는 Azure 데이터 원본에서 데이터 수집을 자동화 하 고 JSON serialization을 처리 합니다. [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) 또는 [Azure Blob 스토리지](search-howto-indexing-azure-blob-storage.md)에 연결하여 주 데이터 저장소에서 검색 가능한 콘텐츠를 추출합니다. Azure Blob 인덱서는 Microsoft Office, PDF 및 HTML 문서를 비롯한 [주요 파일 형식에서 텍스트를 추출](search-howto-indexing-azure-blob-storage.md)하기 위해 *문서 크래킹*을 수행할 수 있습니다. |
| 계층형 및 중첩 데이터 구조 | [**복합 형식**](search-howto-complex-data-types.md) 및 컬렉션을 사용 하면 검색 인덱스 내에서 거의 모든 유형의 JSON 구조를 모델링할 수 있습니다. 일대다 및 다대다 카디널리티는 컬렉션, 복합 형식 및 복합 형식 컬렉션을 통해 고유하게 표현할 수 있습니다.|
| 언어 분석 | 분석기는 인덱싱 및 검색 작업 중 텍스트 처리에 사용되는 구성 요소입니다. 기본적으로 범용 표준 Lucene 분석기를 사용 하거나, 언어 분석기, 구성한 사용자 지정 분석기 또는 필요한 형식으로 토큰을 생성 하는 미리 정의 된 다른 분석기를 사용 하 여 기본값을 재정의할 수 있습니다. <br/><br/>Lucene 또는 Microsoft의 [**언어 분석기**](index-add-language-analyzers.md)는 동사 시제, 성, 불규칙 복수 명사(예: ‘mouse’와 ‘mice’), 단어 분해, 단어 분철(띄어쓰기가 없는 언어의 경우) 등을 비롯한 언어별 언어 체계를 지능적으로 처리할 수 있습니다. <br/><br/>[**사용자 지정 어휘 분석기**](index-add-custom-analyzers.md) 는 표음 일치 및 정규식과 같은 복잡 한 쿼리 폼에 사용 됩니다.<br/><br/> |

## <a name="ai-enrichment-and-knowledge-mining"></a>AI 보강 및 지식 마이닝

| 범주&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 기능 |
|-------------------|----------|
|인덱싱 중 AI 처리 | 이미지 및 텍스트 분석을 위한 [**AI 보강**](cognitive-search-concept-intro.md)은 인덱싱 파이프라인에 적용하여 원시 콘텐츠에서 텍스트 정보를 추출할 수 있습니다. [기본 제공 기술](cognitive-search-predefined-skills.md)의 몇 가지 예제에는 광학 문자 인식(스캔된 JPEG를 검색 가능하게 만듬), 엔터티 인식(조직, 이름 또는 위치 식별) 및 키 구문 인식이 포함됩니다. [사용자 정의 기술을 코딩](cognitive-search-create-custom-skill-example.md)하여 파이프라인에 연결수도 있습니다. [Azure Machine Learning 제작 기술을 통합](./cognitive-search-tutorial-aml-custom-skill.md)할 수도 있습니다. |
| 비검색 시나리오에서 분석하고 사용할 수 있도록 보강된 콘텐츠 저장 | [**지식 저장소**](knowledge-store-concept-intro.md) 는 인덱싱 파이프라인의 대체 출력입니다. 인덱스에 토큰화 된 용어를 전송 하는 대신, 구성에 따라 인덱싱 파이프라인에서 만든 보강 문서를 Azure Blob storage 또는 Table storage에 있는 기술 자료 저장소로 보낼 수 있습니다. 지식 저장소는 AI 기반 인덱싱 (기술력과)에서 생성 됩니다. 기술 자료 저장소의 목적은 다운스트림 분석 또는 처리를 지 원하는 것입니다. 기술 자료 저장소의 새 정보 및 구조를 사용 하 여 기계 학습 프로세스에 연결 하거나 Power BI에서 연결 하 여 데이터를 탐색할 수 있습니다.<br/><br/> |
| 캐시된 콘텐츠 | [**증분 보강 (미리 보기)**](cognitive-search-incremental-indexing-conceptual.md) 는 변경 되지 않는 파이프라인 부분에 대해 캐시 된 콘텐츠를 사용 하 여 파이프라인에 대 한 특정 편집에 의해 변경 되는 문서만 처리 하는 것으로 제한 합니다. |

## <a name="query-and-user-experience"></a>쿼리 및 사용자 환경

| 범주&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 기능 |
|-------------------|----------|
|자유 형식 텍스트 검색 | [**전체 텍스트 검색**](search-lucene-query-architecture.md)은 대부분의 검색 기반 앱에서 기본적으로 사용되는 검색 방식입니다. 쿼리는 지원되는 구문을 사용하여 작성할 수 있습니다. <br/><br/>[**간단한 쿼리 구문**](query-simple-syntax.md)은 논리 연산자, 구 검색 연산자, 후위 연산자, 선행 연산자를 제공합니다.<br/><br/>[**Full Lucene 쿼리 구문**](query-lucene-syntax.md) 에는 간단한 구문의 모든 작업과 유사 항목 검색, 근접 검색, 용어 상승 및 정규식에 대 한 확장이 포함 되어 있습니다.|
| 관련성 | [**간단한 점수 매기기**](index-add-scoring-profiles.md)는 Azure Cognitive Search의 주요 이점입니다. 점수 매기기 프로필은 문서 자체의 값에 대한 함수로서 관련성을 모델링하는 데 사용됩니다. 예를 들어 최신 제품 또는 할인 제품을 검색 결과의 더 높은 부분에 나타내려고 할 수 있습니다. 또한 따로 추적하고 저장한 고객 검색 기본 설정에 따라 개인화된 점수에 태그를 사용하여 점수 매기기 프로필을 만들 수도 있습니다. |
| 지리적 검색 | Azure Cognitive Search는 지리적 위치를 처리하고, 필터링하고, 표시합니다. 이 기능을 통해 사용자는 실제 위치에 대한 검색 결과의 근접도에 따라 데이터를 검색할 수 있습니다. [이 동영상을 시청](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)하거나 [이 예제를 검토](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)하여 자세히 알아보세요. |
| 필터 및 패싯 | [**패싯 탐색**](search-faceted-navigation.md)은 단일 쿼리 매개 변수를 통해 사용하도록 설정됩니다. Azure Cognitive Search는 자기 주도형 필터링(예: 가격 범위 또는 브랜드별로 카탈로그 항목 필터링)을 위해 카탈로그 목록 이면의 코드로 사용할 수 있는 패싯 탐색 구조를 반환합니다. <br/><br/> [**필터**](query-odata-filter-orderby-syntax.md)를 사용하여 패싯 탐색을 애플리케이션 UI에 통합하고, 쿼리 작성을 향상하고, 사용자 또는 개발자가 지정한 기준에 따라 필터링할 수 있습니다. OData 구문을 사용하여 필터를 만듭니다. |
| 사용자 환경 | [**자동 완성**](search-autocomplete-tutorial.md)은 검색 창의 자동 완성 쿼리에 사용할 수 있습니다. <br/><br/>[**검색 제안**](/rest/api/searchservice/suggesters) 도 검색 표시줄 결과에서 일부 텍스트 입력에서 작동에 실제 문서 쿼리를 사용 하지 않고 인덱스 사용 약관이 있습니다. <br/><br/>[**동의어**](search-synonyms.md)는 사용자가 대체 용어를 제공할 필요 없이 쿼리의 범위를 암시적으로 확장하는 동등한 용어를 연결합니다. <br/><br/>[**적중 항목 강조 표시**](/rest/api/searchservice/Search-Documents)는 검색 결과의 일치하는 키워드에 텍스트 서식을 적용합니다. 강조 표시된 조각을 반환할 필드를 선택할 수 있습니다.<br/><br/>[**정렬**](/rest/api/searchservice/Search-Documents)은 인덱스 스키마를 통해 여러 필드에 제공된 후 쿼리 시 단일 검색 매개 변수를 통해 전환됩니다.<br/><br/> 검색 결과의 [**페이징**](search-pagination-page-layout.md) 및 제한은 Azure Cognitive Search에서 검색 결과에 대해 제공하는 세밀하게 튜닝된 컨트롤을 사용하여 간단하게 수행할 수 있습니다.  <br/><br/>|

## <a name="security-features"></a>보안 기능

| 범주&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 기능 |
|-------------------|----------|
| 데이터 암호화. | [**Microsoft 관리형 저장 데이터 암호화**](search-security-overview.md#encrypted-transmissions-and-storage)는 내부 스토리지 레이어에 내장되어 있으며 취소할 수 없습니다. <br/><br/>Azure Key Vault에서 만들고 관리 하는 [**고객이 관리 하는 암호화 키는**](search-security-manage-encryption-keys.md) 인덱스 및 동의어 맵의 보충 암호화에 사용할 수 있습니다. 8 월 1 2020 이후에 만들어진 서비스의 경우 CMK 암호화는 인덱싱된 콘텐츠의 전체 이중 암호화를 위해 임시 디스크의 데이터로 확장 됩니다.|
| 엔드포인트 보호 | [**인바운드 방화벽 지원에 대 한 ip 규칙**](service-configure-firewall.md) 을 사용 하면 검색 서비스에서 요청을 수락 하는 ip 범위를 설정할 수 있습니다.<br/><br/>Azure 개인 링크를 사용 하 여 [**개인 끝점을 만들어**](service-create-private-endpoint.md) 가상 네트워크를 통해 모든 요청을 강제로 적용 합니다. |
| 아웃바운드 보안(인덱서) | [**개인 끝점을 통한 데이터 액세스**](search-indexer-howto-access-private.md) 를 통해 인덱서는 Azure 개인 링크를 통해 보호 되는 azure 리소스에 연결할 수 있습니다.<br/><br/>[**신뢰할 수 있는 id를 사용 하는 데이터 액세스**](search-howto-managed-identities-data-sources.md) 는 외부 데이터 원본에 대 한 연결 문자열에서 사용자 이름과 암호를 생략할 수 있음을 의미 합니다. 인덱서가 데이터 원본에 연결 되 면 검색 서비스가 이전에 신뢰할 수 있는 서비스로 등록 된 경우 리소스에서 연결을 허용 합니다. |

## <a name="portal-features"></a>포털 기능

| 범주&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 기능 |
|-------------------|----------|
| 프로토타입 및 검사용 도구 | [**인덱스 추가**](search-what-is-an-index.md) 는 특성 사용 필드와 몇 가지 다른 설정으로 구성 된 기본 스키마를 만드는 데 사용할 수 있는 포털의 인덱스 디자이너입니다. 인덱스를 저장 한 후에는 SDK 또는 REST API를 사용 하 여 데이터를 제공 하는 방법으로 채울 수 있습니다. <br/><br/>[**데이터 가져오기 마법사**](search-import-data-portal.md) 는 인덱스, 인덱서, 기술력과 및 데이터 원본 정의를 만듭니다. 데이터가 Azure에 존재 하는 경우이 마법사는 특히 개념 증명 조사 및 탐색에 대해 상당한 시간과 노력을 절감할 수 있습니다. <br/><br/>[**검색 탐색기**](search-explorer.md) 는 쿼리를 테스트 하 고 점수 매기기 프로필을 구체화 하는 데 사용 됩니다.<br/><br/>[**데모 앱 만들기**](search-create-app-portal.md) 는 검색 환경을 테스트 하는 데 사용할 수 있는 HTML 페이지를 생성 하는 데 사용 됩니다.  |
| 모니터링 및 진단 | 포털에 항상 표시되는 단순한 메트릭 이상을 원하신다면 [**모니터링 기능을 사용하세요**](search-monitor-usage.md). 필요한 추가 구성 없이 포털 페이지에서 초당 쿼리 수, 대기 시간 및 제한에 대한 메트릭이 캡처되고 보고됩니다.|

## <a name="programmability"></a>프로그래밍 기능

| 범주&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | 기능 |
|-------------------|----------|
| REST (영문) | [**서비스 REST API**](https://docs.microsoft.com/rest/api/searchservice/) 는 인덱싱, 쿼리 및 AI 보강 관련 된 모든 작업을 포함 하 여 데이터 평면 작업에 대 한 것입니다. 또한이 클라이언트 라이브러리를 사용 하 여 시스템 정보와 통계를 검색할 수 있습니다. <br/><br/>[**관리 REST API**](https://docs.microsoft.com/rest/api/searchmanagement/) 은 서비스를 만들고 Azure Resource Manager를 통해 정리 하는 데 사용할 수 있습니다. 이 API를 사용 하 여 키를 관리 하 고 서비스를 프로 비전 할 수도 있습니다.|
| Azure SDK for .NET | [**Azure.Search.Documents**](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme) 는 인덱싱, 쿼리 및 AI 보강와 관련 된 모든 작업을 포함 하 여 데이터 평면 작업을 위한 것입니다. 또한이 클라이언트 라이브러리를 사용 하 여 시스템 정보와 통계를 검색할 수 있습니다. <br/><br/>[**Microsoft. Azure. 검색**](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search) 은 서비스를 만들고 Azure Resource Manager를 통해 정리 하는 데 사용할 수 있습니다. 이 API를 사용 하 여 키를 관리 하 고 서비스를 프로 비전 할 수도 있습니다.|
| Java용 Azure SDK | [**com.azure.search.documents**](https://docs.microsoft.com/java/api/com.azure.search.documents) 는 인덱싱, 쿼리 및 AI 보강와 관련 된 모든 작업을 포함 하 여 데이터 평면 작업을 위한 것입니다. 또한이 클라이언트 라이브러리를 사용 하 여 시스템 정보와 통계를 검색할 수 있습니다. <br/><br/>Azure Resource Manager를 통해 서비스를 만들고 정리 하는 데 [**사용할 수 있습니다**](https://docs.microsoft.com/java/api/overview/azure/search/management) . 이 API를 사용 하 여 키를 관리 하 고 서비스를 프로 비전 할 수도 있습니다.|
| Python용 Azure SDK | [**azure-검색-문서**](https://docs.microsoft.com/python/api/overview/azure/search-documents-readme) 는 인덱싱, 쿼리 및 AI 보강 관련 된 모든 작업을 포함 하는 데이터 평면 작업을 위한 문서입니다. 또한이 클라이언트 라이브러리를 사용 하 여 시스템 정보와 통계를 검색할 수 있습니다. <br/><br/>[**azure-mgmt 검색**](https://docs.microsoft.com/python/api/overview/azure/search/management) 은 서비스를 만들고 Azure Resource Manager를 통해 정리 하는 것입니다. 이 API를 사용 하 여 키를 관리 하 고 서비스를 프로 비전 할 수도 있습니다. |
| JavaScript/TypeScript 용 Azure SDK | [**azure/search-문서**](https://docs.microsoft.com/javascript/api/@azure/search-documents/) 는 인덱싱, 쿼리 및 AI 보강 관련 된 모든 작업을 포함 하는 데이터 평면 작업에 대 한 것입니다. 또한이 클라이언트 라이브러리를 사용 하 여 시스템 정보와 통계를 검색할 수 있습니다. <br/><br/>[**azure/arm-검색**](https://docs.microsoft.com/javascript/api/@azure/arm-search/) 은 서비스를 만들고 Azure Resource Manager를 통해 정리 하는 것입니다. 이 API를 사용 하 여 키를 관리 하 고 서비스를 프로 비전 할 수도 있습니다. |

## <a name="see-also"></a>추가 정보

+ [Cognitive Search의 새로운 기능](whats-new.md)

+ [Cognitive Search의 미리 보기 기능](search-api-preview.md)