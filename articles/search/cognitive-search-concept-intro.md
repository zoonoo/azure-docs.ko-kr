---
title: Cognitive Search, 데이터 추출, 자연어 AI 프로세스 - Azure Search
description: 인식 기술 및 AI 알고리즘을 사용하여 Azure Search 인덱싱에서 검색 가능한 콘텐츠를 만들기 위한 콘텐츠 추출, NLP(자연어 처리) 및 이미지 처리입니다.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/28/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8af927bee11d66c473707b603951fa693f6840e3
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299025"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Azure Search에서 "Cognitive Search"란?

Cognitive Search는 이미지, Blob 및 기타 비정형 데이터 원본에서 텍스트를 추출하는 데 사용되는 Azure Search의 AI 기능으로서, Azure Search 인덱스에서 더 쉽게 검색할 수 있도록 콘텐츠를 보강합니다. 추출 및 보강은 인덱싱 파이프라인에 연결된 ‘인식 기술’을 통해 구현됩니다.  AI 보강은 다음과 같은 방법으로 지원됩니다. 

+ **자연어 처리** 기술에는 [엔터티 인식](cognitive-search-skill-entity-recognition.md), [언어 감지](cognitive-search-skill-language-detection.md), [핵심 구 추출](cognitive-search-skill-keyphrases.md), 텍스트 조작 및 [감성 인식](cognitive-search-skill-sentiment.md)이 포함됩니다. 해당 기술을 사용하면 비정형 텍스트가 인덱스에서 검색 및 필터링 가능한 필드로 매핑되는 새 양식을 가정할 수 있습니다.

+ **이미지 처리** 기술에는 [OCR(광학 인식)](cognitive-search-skill-ocr.md)을 비롯하여 얼굴 감지, 이미지 해석, 이미지 인식(유명한 사람 및 랜드마크) 또는 색상이나 이미지 방향과 같은 특성 등의 [시각적 특징](cognitive-search-skill-image-analysis.md) 식별이 포함됩니다. Azure Search의 모든 쿼리 기능을 사용하여 검색 가능한 이미지 콘텐츠의 텍스트 표현을 만들 수 있습니다.

![Cognitive 검색 파이프라인 다이어그램](./media/cognitive-search-intro/cogsearch-architecture.png "Cognitive 검색 파이프라인 개요")

Azure Search의 인식 기술은 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) 및 [Text Analysis](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)와 같은 Cognitive Services API의 기계 학습 모델을 기반으로 합니다. 

자연어 및 이미지 처리는 Azure Search에서 검색 가능한 인덱스에 문서의 컴퍼지션의 일부가 되는 결과를 사용하여 데이터 수집 단계 동안 적용됩니다. 데이터는 Azure 데이터 집합으로 소싱된 다음, 필요한 [기본 제공 기술](cognitive-search-predefined-skills.md)을 사용하여 인덱싱 파이프라인을 통해 푸시됩니다. 아키텍처는 확장이 가능하므로 기본 제공 기술이 충분하지 않은 경우 사용자 지정 처리를 통합하도록 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)을 만들고 연결할 수 있습니다. 예에는 금융, 과학 출판물 또는 의약품과 같은 특정 도메인을 대상으로 하는 사용자 지정 엔터티 모듈 또는 문서 분류자가 있습니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출할 때와 Azure Search에서 문서 해독 단계의 일부로 이미지를 추출할 때는 요금이 누적됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정 정보는 [Azure Search 가격 페이지](https://go.microsoft.com/fwlink/?linkid=2042400)에 설명되어 있습니다.
## <a name="components-of-cognitive-search"></a>Cognitive Search의 구성 요소

Cognitive Search 파이프라인은 데이터 원본을 탐색하고 엔드투엔드 인덱스 프로세싱을 제공하는 [Azure Search *인덱서*](search-indexer-overview.md)를 기반으로 합니다. 기술이 인덱서에 연결되어 문서를 가로채서 사용자가 정의한 기술에 따라 문서를 보강합니다. 인덱스가 완료되면 [Azure Search에서 지원하는 모든 쿼리 유형](search-query-overview.md)에서 검색 요청을 통해 콘텐츠에 액세스할 수 있습니다.  인덱서를 처음 접하는 경우, 이 섹션의 단계별 안내를 참조하세요.

### <a name="step-1-connection-and-document-cracking-phase"></a>1단계: 연결 및 문서 크래킹 단계

파이프라인의 시작 부분에는 구조화되지 않은 텍스트 또는 텍스트가 아닌 콘텐츠(예: 이미지 및 스캔한 문서 JPEG 파일)가 있습니다. 데이터는 인덱서가 액세스할 수 있는 Azure 데이터 저장소 서비스에 있어야 합니다. 인덱서는 원본 문서에서 텍스트를 추출하기 위해 원본 문서를 "크래킹"할 수 있습니다.

![문서 크래킹 단계](./media/cognitive-search-intro/document-cracking-phase-blowup.png "문서 크래킹")

 지원되는 원본에는 Azure Blob Storage, Azure Table Storage, Azure SQL Database 및 Azure Cosmos DB가 포함됩니다. 텍스트 기반 콘텐츠는 PDF, Word, PowerPoint, CSV 파일 형식에서 추출할 수 있습니다. 전체 목록은 [지원되는 형식](search-howto-indexing-azure-blob-storage.md#supported-document-formats)을 참조하세요.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2단계: 인지 기술 및 보강 단계

보강은 원자성 작업을 수행하는 인지 기술(*cognitive skills*)을 거칩니다. 예를 들어 PDF에서 텍스트 콘텐츠를 확보하면 엔터티 인식 언어 감지 또는 핵심 구 추출을 적용하여 원본에서 기본적으로 사용할 수 없는 새 필드를 인덱스에 생성합니다. 전체적으로 파이프라인에 사용된 기술 컬렉션을 기술 집합(*skillset*)이라고 합니다.  

![보강 단계](./media/cognitive-search-intro/enrichment-phase-blowup.png "보강 단계")

기술 집합은 [미리 정의된 인지 기술](cognitive-search-predefined-skills.md) 또는 사용자가 제공하고 기술 집합에 연결하는 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)을 기반으로 합니다. 기술 집합은 매우 간단하거나 복잡할 수 있으며 처리 유형뿐만 아니라 작업 순서도 결정합니다. 기술 집합 및 인덱서의 일부로 정의된 필드 매핑은 보강 파이프라인을 완벽하게 지정합니다. 이 모든 부분을 한데 모으는 방법에 대한 자세한 정보 [기술 집합 정의](cognitive-search-defining-skillset.md)를 참조하세요.

내부적으로 파이프라인은 보강된 문서 컬렉션을 생성합니다. 보강된 문서의 어느 부분을 검색 인덱스의 인덱싱 가능 필드에 매핑할지 결정할 수 있습니다. 예를 들어 핵심 구 추출과 엔터티 인식 기술을 적용한 경우 새 필드는 보강된 문서의 일부가 되며 인덱스의 필드에 매핑될 수 있습니다. 입/출력에 대한 자세한 내용은 [주석](cognitive-search-concept-annotations-syntax.md)을 참조하세요.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>보강 내용을 저장하는 knowledgeStore 요소 추가

[Search Service REST api-version=2019-05-06](search-api-preview.md)은 보강 저장 방식을 설명하는 Azure 스토리지 연결 및 프로젝션을 제공하는 knowledgeStore 정의를 통해 기술 세트를 확장합니다. 

기술 세트에 지식 저장소를 추가하면 전체 텍스트 검색 이외의 시나리오에 대한 보강 표현을 예상할 수 있습니다. 자세한 내용은 [지식 저장소란?](knowledge-store-concept-intro.md)을 참조하세요.

### <a name="step-3-search-index-and-query-based-access"></a>3단계: 검색 인덱스 및 쿼리 기반 액세스

처리가 완료되면 Azure Search에서 텍스트 검색이 가능하도록 보강된 문서로 구성된 검색 인덱스가 완성됩니다. [인덱스 쿼리](search-query-overview.md)는 개발자와 사용자가 파이프라인에서 생성된 보강된 콘텐츠에 액세스하는 방법입니다. 

![검색 아이콘이 있는 인덱스](./media/cognitive-search-intro/search-phase-blowup.png "검색 아이콘이 있는 인덱스")

인덱스는 Azure Search에서 만들 수 있는 다른 항목처럼, 사용자 지정 분석기를 보완하거나 유사 항목 검색 쿼리를 호출하거나 필터링된 검색을 추가하거나 채점 프로파일로 실험하여 검색 결과의 모양을 바꿀 수 있습니다.

인덱스는 채점 프로파일 및 동의어 맵과 같은 특정 인덱스에 연결된 필드, 특성 및 기타 구문을 정의하는 인덱스 스키마에서 생성됩니다. 인덱스가 정의되고 채워진 후에는 새 원본 문서나 업데이트된 원본 문서를 가져오기 위해 증분 방식으로 인덱싱할 수 있습니다. 특정 수정에는 전체 다시 빌드가 필요합니다. 스키마 디자인이 안정될 때까지 작은 데이터 집합을 사용해야 합니다. 자세한 내용은 [인덱스를 다시 빌드하는 방법](search-howto-reindex.md)을 참조하세요.

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>주요 기능 및 개념

| 개념 | 설명| 링크 |
|---------|------------|-------|
| 기술 집합 | 기술 컬렉션이 포함된 최상위의 명명된 리소스입니다. 기술 집합은 보강 파이프라인입니다. 인덱서가 인덱싱을 수행하는 동안 호출됩니다. | [기술 집합 정의](cognitive-search-defining-skillset.md) |
| 인지 기술 | 보강 파이프라인의 원자성 변환입니다. 대개, 구조를 추출하거나 유추하는 구성 요소이므로 입력 데이터에 대한 이해를 높여줍니다. 거의 항상 출력은 텍스트 기반이며 처리는 자연어 처리이거나 이미지 입력으로부터 텍스트를 추출하거나 생성하는 이미지 처리입니다. 기술을 통한 출력은 인덱스의 필드로 매핑되거나 다운스트림 보강을 위한 입력으로 사용될 수 있습니다. 기술은 Microsoft가 미리 정의해서 제공하거나 사용자가 지정하여 생성하고 배포합니다. | [미리 정의된 기술](cognitive-search-predefined-skills.md) |
| 데이터 추출 | 광범위한 처리를 포함하지만 인식 서비스에 속하는 명명된 엔터티 인식 기술은 해당 정보를 기본적으로 제공하지 않는 소스에서 데이터(엔터티)를 추출하기 위해 가장 일반적으로 사용됩니다. | [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)| 
| 이미지 처리 | 이정표를 인식하는 기능, 이미지에서 텍스트를 추출하는 기능처럼 이미지의 텍스트를 유추합니다. 일반적인 예에는 스캔한 문서(JPEG) 파일에서 문자를 인식하거나 도로 표지판이 포함된 사진에서 거리 이름을 인식하는 OCR이 있습니다. | [이미지 분석 기술](cognitive-search-skill-image-analysis.md) 또는 [OCR 기술](cognitive-search-skill-ocr.md)
| 자연어 처리 | 입력 텍스트에 대한 정보 및 인사이트를 위한 텍스트 처리입니다. 언어 감지, 감정 분석 및 핵심 구 추출은 자연어 처리에 해당하는 기술입니다.  | [핵심 구 추출 기술](cognitive-search-skill-keyphrases.md), [언어 감지 기술](cognitive-search-skill-language-detection.md), [감정 분석 기술](cognitive-search-skill-sentiment.md) |
| 문서 크래킹 | 인덱싱 중에 텍스트가 아닌 원본에서 텍스트 콘텐츠를 추출하거나 만드는 프로세스입니다. OCR(광학 문자 인식)은 하나의 예이며, 일반적으로 인덱서가 애플리케이션 파일에서 콘텐츠를 추출하는 핵심 인덱서 기능을 말합니다. 원본 파일 위치를 제공하는 데이터 원본과 필드 매핑을 제공하는 인덱서 정의는 문서 크래킹의 핵심 요소입니다. | [인덱서](search-indexer-overview.md)를 참조하세요. |
| 셰이핑 | 추가적인 다운스트림 처리를 위해 텍스트 조각을 더 큰 구조에 통합하거나 반대로 큰 텍스트 청크를 처리할 수 있는 크기로 분해합니다. | [쉐이퍼 기술](cognitive-search-skill-shaper.md), [텍스트 병합기 기술](cognitive-search-skill-textmerger.md), [텍스트 분할 기술](cognitive-search-skill-textsplit.md) |
| 보강된 문서 | 검색 인덱스에 반영된 최종 출력을 사용하여 처리하는 동안 생성된 일시적인 내부 구조입니다. 기술 세트는 어떤 보강이 수행되는지 결정합니다. 필드 매핑은 인덱스에 추가할 데이터 요소를 결정합니다. 선택적으로, Storage Explorer, Power BI 또는 Azure Blob Storage에 연결되는 다른 도구를 사용하여 보강된 문서를 유지하고 살펴보기 위한 기술 저장소를 만들 수 있습니다. | [기술 저장소(미리 보기)](knowledge-store-concept-intro.md)를 참조하세요. |
| 인덱서 |  외부 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하고 문서 크래킹을 위한 인덱스와 데이터 원본 간의 필드 간 매핑에 따라 인덱스를 채우는 크롤러입니다. Cognitive Search 보강을 위해 인덱서는 기술 집합을 호출하고 인덱스의 대상 필드에 보강 출력을 연결하는 필드 매핑을 포함합니다. 인덱서 정의에는 파이프라인 작업에 대한 모든 지침과 참조가 포함되며 인덱서를 실행하면 파이프라인이 호출됩니다. | [인덱서](search-indexer-overview.md) |
| 데이터 원본  | Azure에서 지원되는 유형의 외부 데이터 소스에 연결할 때 인덱서에 사용되는 개체입니다. | [인덱서](search-indexer-overview.md)를 참조하세요. |
| 인덱스 | 필드 구조와 사용법을 정의하는 인덱스 스키마로 작성된 Azure Search의 지속형 검색 인덱스입니다. | [Azure Search의 인덱스](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>시작 단계

**1단계: [Azure Search 리소스 만들기](search-create-service-portal.md)** 

**2단계: 실습 환경에 대한 일부 빠른 시작 및 예제 사용해보기**

+ [빠른 시작(포털)](cognitive-search-quickstart-blob.md)
+ [자습서(HTTP 요청)](cognitive-search-tutorial-blob.md)
+ [예제 사용자 지정 기술(C#)](cognitive-search-create-custom-skill-example.md)

학습 목적이라면 무료 서비스를 권장하지만 무료 트랜잭션 수는 하루 20개 문서로 제한된다는 것을 알고 있어야 합니다. 하루에 빠른 시작과 자습서를 모두 실행하려면 더 작은 파일 세트(10개 문서)를 사용해야 두 연습에 모두 맞출 수 있습니다.

**3단계: API 검토**

요청 또는 .NET SDK에서 REST `api-version=2019-05-06`을 사용할 수 있습니다. 

이 단계에서는 REST API를 사용하여 Cognitive Search 솔루션을 빌드합니다. Cognitive Search를 위해 두 개의 API만 추가되거나 확장됩니다. 다른 API는 일반적으로 사용 가능한 버전과 동일한 구문을 포함합니다.

| REST API | 설명 |
|-----|-------------|
| [데이터 원본 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | 보강된 문서를 만드는 데 사용되는 원본 데이터를 제공하는 외부 데이터 원본을 식별하는 리소스입니다.  |
| [기술 세트 만들기(api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 인덱싱 중에 강화 파이프라인에 사용되는 [미리 정의된 기술](cognitive-search-predefined-skills.md) 및 [사용자 지정 인지 기술](cognitive-search-custom-skill-interface.md)의 사용을 조정하는 리소스입니다. |
| [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Azure Search 인덱스를 표현하는 스키마입니다. 원본 데이터 또는 보강 단계에서 생성되는 필드에 매핑되는 인덱스의 필드(예: 엔터티 인식으로 생성된 조직 이름에 대한 필드)입니다. |
| [인덱서 만들기(api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | 데이터 원본, 기술 집합, 원본 및 중간 데이터 구조에서 대상 인덱스로 필드 연결 및 인덱스 자체를 포함하는 인덱싱 중에 사용되는 리소스를 정의하는 구성 요소입니다. 데이터 수집 및 보강을 위한 트리거가 인덱서를 실행합니다. 출력은 원본 데이터로 채워지고 기술 세트를 통해 보강된 인덱스 스키마 기반의 검색 인덱스입니다.  |

**검사 목록: 일반적인 워크플로**

1. Azure 원본 데이터에서 대표 샘플 하위 집합을 생성합니다. 인덱싱에 시간이 걸리므로 작은 대표 데이터 집합으로 시작한 다음, 솔루션의 완성도가 높아지면 증분 방식으로 빌드합니다.

1. Azure Search에 [데이터 원본 개체](https://docs.microsoft.com/rest/api/searchservice/create-data-source)를 생성하여 데이터 검색을 위한 연결 문자열을 제공합니다.

1. 보강 단계를 통해 [기술 집합](https://docs.microsoft.com/rest/api/searchservice/create-skillset)을 만듭니다.

1. [인덱스 스키마](https://docs.microsoft.com/rest/api/searchservice/create-index)를 정의합니다. 필드(*Field*) 컬렉션에 원본 데이터의 필드를 포함합니다. 보강 과정에서 만들어진 콘텐츠에 대해 생성된 값을 저장할 필드를 더 추가해야 합니다.

1. 데이터 원본, 기술 집합 및 인덱스를 참조하는 [인덱서](https://docs.microsoft.com/rest/api/searchservice/create-skillset)를 정의합니다.

1. 인덱서 내에 *outputFieldMappings*를 추가합니다. 이 섹션에서는 기술 집합(3단계)의 출력을 인덱스 스키마(4단계)의 입력 필드에 매핑합니다.

1. Azure Search에서 인덱서를 표현하기 위해 방금 만든 인덱서 생성(*Create Indexer*) 요청(요청 본문에 인덱서 정의가 있는 POST 요청)을 보냅니다. 이 단계는 인덱서를 실행하여 파이프라인을 호출하는 방법에 대한 내용입니다.

1. 쿼리를 실행하여 결과를 평가하고 코드를 수정하여 기술 집합, 스키마 또는 인덱서 구성을 업데이트합니다.

1. 파이프라인을 다시 빌드하기 전에 [인덱서를 다시 설정](search-howto-reindex.md)합니다.

특정 질문 또는 문제에 대한 자세한 내용은 [문제 해결 팁](cognitive-search-concept-troubleshooting.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

+ [Cognitive Search 설명서](cognitive-search-resources-documentation.md)
+ [빠른 시작: 포털 연습에서 Cognitive Search 시도](cognitive-search-quickstart-blob.md)
+ [자습서: Cognitive Search API 알아보기](cognitive-search-tutorial-blob.md)
+ [지식 저장소 개요](knowledge-store-concept-intro.md)
+ [지식 저장소 연습](knowledge-store-howto.md)
