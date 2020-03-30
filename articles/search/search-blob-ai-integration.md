---
title: AI를 사용하여 Blob 스토리지 데이터 이해
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 파이프라인을 사용하여 의미 체계, 자연어 처리 및 이미지 분석을 Azure Blob에 추가합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496437"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>AI를 사용하여 Blob 스토리지 데이터 이해

Azure Blob 저장소의 데이터는 이미지, 긴 텍스트, PDF 및 Office 문서와 같은 다양한 구조화되지 않은 콘텐츠입니다. Azure Cognitive Search에서 AI 기능을 사용하면 다양한 방법으로 Blob에서 중요한 정보를 이해하고 추출할 수 있습니다. Blob 콘텐츠에 AI를 적용하는 예는 다음과 같습니다.

+ 광학 문자 인식(OCR)을 사용하여 이미지에서 텍스트 추출
+ 사진에서 장면 설명 또는 태그 생성
+ 언어 감지 및 텍스트 번역
+ 명명된 엔터티 인식(NER)이 있는 텍스트를 처리하여 사람, 날짜, 장소 또는 조직에 대한 참조를 찾습니다. 

이러한 AI 기능 중 하나만 필요할 수 있지만 여러 기능을 동일한 파이프라인에 결합하는 것이 일반적입니다(예: 스캔한 이미지에서 텍스트를 추출한 다음 참조된 모든 날짜와 장소를 찾는 경우). 

AI 보강은 필드에 저장된 텍스트로 캡처된 새 정보를 만듭니다. 보강 후 전체 텍스트 검색을 통해 검색 인덱스에서 이 정보에 액세스하거나 보강된 문서를 Azure 저장소로 다시 보내 검색 또는 분석 시나리오에 대한 데이터 탐색을 포함하는 새 응용 프로그램 환경을 강화할 수 있습니다. 

이 문서에서는 와이드 렌즈를 통해 AI 보강을 통해 Blob의 원시 데이터 변환에서 검색 인덱스 또는 지식 저장소의 쿼리 가능한 정보에 이르기까지 전체 프로세스를 신속하게 파악할 수 있습니다.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>AI로 Blob 데이터를 "보강"하는 의미

*AI 보강은* Microsoft의 기본 제공 AI 또는 제공하는 사용자 지정 AI를 통합하는 Azure Cognitive Search의 인덱싱 아키텍처의 일부입니다. 이를 통해 Blob(기존 및 새 항목모두 들어오거나 업데이트될 때)을 처리하고, 모든 파일 형식을 열어 이미지와 텍스트를 추출하고, 다양한 AI 기능을 사용하여 원하는 정보를 추출해야 하는 종단 간 시나리오를 구현하는 데 도움이 됩니다. 빠른 검색, 검색 및 탐색을 위한 검색 인덱스로 인덱싱합니다. 

입력은 Azure Blob 저장소의 단일 컨테이너에 있는 Blob입니다. Blob은 거의 모든 종류의 텍스트 또는 이미지 데이터일 수 있습니다. 

출력은 항상 빠른 텍스트 검색, 검색 및 클라이언트 응용 프로그램에서 탐색에 사용되는 검색 인덱스입니다. 또한 출력은 Power BI 또는 데이터 과학 워크로드와 같은 도구에서 다운스트림 분석을 위해 보강된 문서를 Azure Blob 또는 Azure 테이블로 프로젝트하는 *지식 저장소일* 수도 있습니다.

그 사이에는 파이프라인 아키텍처 자체가 있습니다. 파이프라인은 AI를 제공하는 하나 이상의 *기술로* 구성된 *기술 집합을*할당할 수 있는 *인덱서* 기능을 기반으로 합니다. 파이프라인의 목적은 원시 콘텐츠로 입력하지만 파이프라인을 통해 이동하는 동안 추가 구조, 컨텍스트 및 정보를 선택하는 *보강된 문서를* 생성하는 것입니다. 보강된 문서는 인덱싱 중에 전체 텍스트 검색 또는 탐색 및 분석에 사용되는 반전된 인덱스 및 기타 구조를 만드는 데 사용됩니다.

## <a name="start-with-services"></a>서비스 시작

Azure 인지 검색 및 Azure Blob 저장소가 필요합니다. Blob 저장소 내에 원본 콘텐츠를 제공하는 컨테이너가 필요합니다.

Storage 계정 포털 페이지에서 직접 시작할 수 있습니다. 왼쪽 탐색 페이지에서 **Blob 서비스** 아래에서 **Azure 인지 검색 추가를** 클릭하여 새 서비스를 만들거나 기존 서비스를 선택합니다. 

저장소 계정에 Azure Cognitive Search를 추가하면 표준 프로세스를 따라 모든 Azure 데이터 원본의 데이터를 보강할 수 있습니다. Azure Cognitive Search의 **데이터 가져오기** 마법사를 사용하여 AI 보강에 대한 초기 도입을 쉽게 할 수 있습니다. 이 빠른 시작 단계를 안내합니다: [포털에서 AI 보강 파이프라인 만들기.](cognitive-search-quickstart-blob.md) 

다음 섹션에서는 더 많은 구성 요소와 개념을 살펴보겠습니다.

## <a name="use-a-blob-indexer"></a>Blob 인덱서 사용

AI 보강은 인덱싱 파이프라인에 대한 추가 기능이며 Azure Cognitive Search에서는 이러한 파이프라인이 *인덱서*위에 빌드됩니다. 인덱서에는 데이터 샘플링, 메타데이터 데이터 읽기, 데이터 검색, 네이티브 형식의 데이터를 JSON 문서로 직렬화하여 이후 가져오기를 위한 내부 논리가 장착된 데이터 원본 인식 하위 서비스입니다. 인덱서를 가져오는 데 는 AI와 별도로 자체적으로 사용되는 경우가 많지만 AI 보강 파이프라인을 구축하려면 인덱서와 기술 집합이 필요합니다. 이 섹션에서는 인덱서를 강조 합니다. 다음 섹션에서는 기술 집합에 중점을 둡니다.

Azure 저장소의 Blob은 Azure [인지 검색 Blob 저장소 인덱서를](search-howto-indexing-azure-blob-storage.md)사용하여 인덱싱됩니다. **가져오기 데이터** 마법사, REST API 또는 .NET SDK를 사용하여 이 인덱서를 호출할 수 있습니다. 코드에서는 형식을 설정하고 Blob 컨테이너와 함께 Azure Storage 계정을 포함하는 연결 정보를 제공하여 이 인덱서를 사용합니다. 그런 다음 매개 변수로 전달할 수 있는 가상 디렉터리를 만들거나 파일 형식 확장명에 필터링하여 Blob을 하위집합할 수 있습니다.

인덱서가 "문서 크래킹"을 수행하여 Blob을 열어 내용을 검사합니다. 데이터 원본에 연결한 후 파이프라인의 첫 번째 단계입니다. Blob 데이터의 경우 PDF, 사무실 문서, 이미지 및 기타 콘텐츠 형식이 검색되는 위치입니다. 텍스트 추출을 통해 문서 크래킹은 무료입니다. 이미지 추출을 통해 문서 크래킹은 [가격 책정 페이지에서](https://azure.microsoft.com/pricing/details/search/)찾을 수 있는 요금으로 청구됩니다.

모든 문서는 금이 가지만, 보강은 명시적으로 그렇게 할 수 있는 기술을 제공하는 경우에만 발생합니다. 예를 들어 파이프라인이 이미지 분석만으로 구성된 경우 컨테이너 또는 문서의 텍스트는 무시됩니다.

Blob 인덱서에는 구성 매개 변수가 함께 제공되며 기본 데이터가 충분한 정보를 제공하는 경우 변경 사항 추적을 지원합니다. [Azure 인지 검색 Blob 저장소 인덱서에서](search-howto-indexing-azure-blob-storage.md)핵심 기능에 대해 자세히 알아볼 수 있습니다.

## <a name="add-ai-components"></a>AI 구성 요소 추가

AI 보강은 패턴이나 특성을 찾은 다음 그에 따라 작업을 수행하는 모듈을 말합니다. 사진의 얼굴 인식, 사진의 텍스트 설명, 문서의 핵심 구를 감지하는 것, OCR(또는 이진 파일에서 인쇄되거나 필기 텍스트를 인식하는 것)이 예시예입니다.

Azure Cognitive Search에서 *기술은* 독립 실행형 또는 다른 기술과 함께 사용할 수 있는 AI 처리의 개별 구성 요소입니다. 

+ 내장 된 기술은 컴퓨터 비전을 기반으로 이미지 분석, 텍스트 분석을 기반으로 자연어 처리와 코그너티브 서비스에 의해 뒷받침된다. 전체 목록은 콘텐츠 [보강을 위한 기본 제공 기술을](cognitive-search-predefined-skills.md)참조하십시오.

+ 사용자 지정 기술은 파이프라인에 통합할 수 있는 [인터페이스 정의로](cognitive-search-custom-skill-interface.md) 래핑된 사용자 지정 코드입니다. 고객 솔루션에서는 오픈 소스, 타사 또는 자중 AI 모듈을 제공하는 사용자 지정 기술을 사용하여 두 가지 를 모두 사용하는 것이 일반적입니다.

*기술 집합은* 파이프라인에 사용되는 기술의 모음이며 문서 크래킹 단계에서 콘텐츠를 사용할 수 있게 한 후에 호출됩니다. 인덱서에서는 정확히 하나의 기술 집합을 사용할 수 있지만 해당 기술 집합은 인덱서와 독립적으로 존재하므로 다른 시나리오에서 다시 사용할 수 있습니다.

사용자 지정 기술은 복잡하게 들릴 수 있지만 구현 측면에서 간단하고 간단할 수 있습니다. 패턴 일치 또는 분류 모델을 제공하는 기존 패키지가 있는 경우 Blob에서 추출한 콘텐츠를 처리를 위해 이러한 모델로 전달할 수 있습니다. AI 보강은 Azure 기반이므로 모델도 Azure에 있어야 합니다. 몇 가지 일반적인 호스팅 방법론에는 Azure Functions 또는 [컨테이너](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)를 사용하는 [것이 포함됩니다.](cognitive-search-create-custom-skill-example.md)

코그너티브 서비스에서 지원하는 기본 제공 기술에는 리소스에 액세스할 수 있는 [연결된 코그너티브 서비스](cognitive-search-attach-cognitive-services.md) 올인원 구독 키가 필요합니다. 올인원 키는 이미지 분석, 언어 검색, 텍스트 번역 및 텍스트 분석을 제공합니다. 다른 기본 제공 기술은 Azure Cognitive Search의 기능이며 추가 서비스 나 키가 필요하지 않습니다. 텍스트 셰이퍼, 스플리터 및 합병은 파이프라인을 디자인할 때 필요한 도우미 기술의 예입니다.

사용자 지정 기술과 기본 제공 유틸리티 기술만 사용하는 경우 코그너티브 서비스와 관련된 종속성이나 비용은 없습니다.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>다운스트림 솔루션에서 AI가 풍부한 출력 소비

AI 보강의 출력은 Azure 인지 검색의 검색 인덱스 또는 Azure 저장소의 [기술 저장소입니다.](knowledge-store-concept-intro.md)

Azure Cognitive Search에서 검색 인덱스는 클라이언트 앱에서 자유 텍스트 및 필터링된 쿼리를 사용하여 대화형 탐색에 사용됩니다. AI를 통해 생성된 보강 된 문서는 JSON형식으로 서식이 지정되고 모든 문서가 Azure 인지 검색에서 인덱싱되는 것과 동일한 방식으로 인덱싱되어 인덱서가 제공하는 모든 이점을 활용합니다. 예를 들어 인덱싱 중에 Blob 인덱서에서는 모든 필드 매핑을 활용하거나 검색 논리를 변경하기 위해 구성 매개 변수 및 설정을 참조합니다. 이러한 설정은 일반 인덱싱 및 AI 강화 워크로드에서 완전히 사용할 수 있습니다. 인덱싱 후 콘텐츠가 Azure Cognitive Search에 저장되면 풍부한 쿼리를 빌드하고 식을 필터링하여 콘텐츠를 이해할 수 있습니다.

Azure 저장소에는 두 가지 표현이 있습니다. 

+ Blob 컨테이너는 보강된 문서를 전체적으로 캡처하므로 다른 프로세스에 공급하려는 경우에 유용합니다. 

+ 반대로 테이블 저장소는 보강된 문서의 물리적 투영을 수용할 수 있습니다. 특정 부품을 포함하거나 제외하는 보강된 문서의 조각 또는 레이어를 작성할 수 있습니다. Power BI에서 분석할 때 Azure 테이블 저장소의 테이블은 추가 시각화 및 탐색을 위한 데이터 원본이 됩니다.

파이프라인 끝에 있는 보강된 문서는 보강 중에 추출되거나 생성된 새 정보가 포함된 추가 필드가 있으면 원래 입력 버전과 다릅니다. 따라서 사용하는 출력 구조에 관계없이 원본 콘텐츠와 생성된 콘텐츠의 조합으로 작업할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AI 보강을 통해 다양한 방식으로 코그너티브 서비스를 결합하고 시나리오에 대한 기존 코그너티브 서비스가 없는 경우에 대한 사용자 지정 기술을 작성하는 등 Azure Storage에서 데이터를 최대한 최대한 사용할 수 있는 더 많은 방법이 있습니다. 아래 링크를 따라 자세한 내용을 확인할 수 있습니다.

+ [Azure 포털(Azure Blob 저장소)을 통해 Blob을 업로드, 다운로드 및 나열합니다.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob 인덱서 설정(Azure 인지 검색)](search-howto-indexing-azure-blob-storage.md) 
+ [AI 보강 개요(Azure 인지 검색)](cognitive-search-concept-intro.md) 
+ [기술 집합 만들기(Azure 인지 검색)](cognitive-search-defining-skillset.md)
+ [추가 트리의 노드 매핑(Azure 인지 검색)](cognitive-search-output-field-mapping.md)
