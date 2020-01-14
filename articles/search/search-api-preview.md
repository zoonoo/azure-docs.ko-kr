---
title: REST API 버전 2019-05-06-미리 보기
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search service REST API 버전 2019-05-06-미리 보기에는 증분 보강에 대 한 지식 저장소 및 인덱서 캐싱과 같은 실험적 기능이 포함 되어 있습니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: eb73d614ca94bc1fa007a14f3705e50c74ab9e4f
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922469"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Cognitive Search 서비스 REST api-버전 2019-05-06-미리 보기

이 문서에서는 아직 일반 공급 되지 않는 실험적 기능을 제공 하는 검색 서비스 REST API `api-version=2019-05-06-Preview` 버전을 설명 합니다.

> [!NOTE]
> 미리 보기 기능은 사용자 의견을 수집하고 변경하기 위해 테스트 및 실험에 사용할 수 있습니다. 프로덕션 애플리케이션에서 미리 보기 API를 사용하지 않는 것이 좋습니다.


## <a name="new-in-2019-05-06-preview"></a>2019-05-06의 새로운 기능-미리 보기

+ [증분 보강 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 는 보강 파이프라인에 캐싱을 추가 하 여 기술 또는 다른 개체에 대 한 업데이트와 같은 대상 수정에서 콘텐츠를 변경 하지 않는 경우 기존 출력을 다시 사용할 수 있도록 합니다. 캐싱은 기술에서 생성 된 보강 문서에만 적용 됩니다.

+ [Cosmos DB 인덱서](search-howto-index-cosmosdb.md) 는 MongoDB api (미리 보기), Gremlin api (미리 보기) 및 Cassandra API (미리 보기)를 지원 합니다.

+ [Azure Data Lake Storage Gen2 인덱서 (미리 보기)](search-howto-index-azure-data-lake-storage.md) 는 Data Lake Storage Gen2에서 콘텐츠 및 메타 데이터를 인덱싱할 수 있습니다.

+ [문서 추출 (미리 보기)](cognitive-search-skill-document-extraction.md) 은 기술 내에서 파일의 콘텐츠를 추출할 수 있는 인덱싱 중에 사용 되는 인식 기술입니다. 이전에는 문서 크랙 기술 실행 되기 전에 발생 했습니다. 이 기술 외에도 기술 실행 내에서이 작업을 수행할 수 있습니다.

+ [텍스트 번역 (미리 보기)](cognitive-search-skill-text-translation.md) 은 텍스트를 평가 하는 인덱싱 중에 사용 되는 인식 기술 이며, 각 레코드에 대해 지정 된 대상 언어로 변환 된 텍스트를 반환 합니다.

+ [기술 자료 저장소](knowledge-store-concept-intro.md) 는 AI 기반 보강 파이프라인의 새로운 대상입니다. 물리적 데이터 구조는 Azure Blob storage 및 Azure Table storage에 존재 하며, 연결 된 인지 기술 있는 인덱서를 실행할 때 생성 되 고 채워집니다. 기술 자료 저장소 자체의 정의는 기술 정의 내에서 지정 됩니다. 기술 자료 저장소 정의 내에서 데이터의 모양을 결정 하는 프로젝션 요소, 데이터가 테이블 저장소 또는 Blob 저장소에 저장 되는지 여부 및 여러 뷰가 있는지 여부를 결정 하는 *프로젝션* 요소를 통해 데이터의 물리적 구조를 제어 합니다.

## <a name="earlier-preview-features"></a>이전 미리 보기 기능

이전 미리 보기에서 공지된 기능이 여전히 공개 미리 보기 상태로 제공됩니다. 이전 미리 보기 API 버전에서 API를 호출하는 경우 계속 해당 버전을 사용하거나 예상되는 동작을 변경하지 않고 `2019-05-06-Preview`로 전환할 수 있습니다.

+ [moreLikeThis 쿼리 매개 변수](search-more-like-this.md)는 특정 문서와 관련된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. 

+ [CSV blob 인덱싱은](search-howto-index-csv-blobs.md) 텍스트 blob 당 하나의 문서와는 달리 한 줄에 하나씩 문서를 만듭니다.

## <a name="how-to-call-a-preview-api"></a>미리 보기 API를 호출하는 방법

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드가 `api-version=2016-09-01-Preview` 또는 `api-version=2017-11-11-Preview`를 호출하는 경우 이러한 호출은 여전히 유효합니다. 그러나 최신 미리 보기 버전만 향상된 기능으로 새로 고쳐집니다. 

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Cognitive Search 서비스는 여러 버전에서 사용할 수 있습니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

검색 REST API 참조 설명서를 검토 합니다. 문제가 발생 하는 경우 [Stackoverflow](https://stackoverflow.com/) 또는 [지원 담당자에 게](https://azure.microsoft.com/support/community/?product=search)도움을 요청 합니다.

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)