---
title: REST API에서 기능 미리 보기
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 서비스 REST API 버전 2019-05-06-미리 보기에는 증분 보강을 위한 지식 저장소 및 인덱서 캐싱과 같은 실험적인 기능이 포함되어 있습니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162279"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Azure 인지 검색의 기능 미리 보기

이 문서에는 현재 미리 보기 의 기능이 나열되어 있습니다. 미리 보기에서 일반 공급으로 전환하는 기능은 이 목록에서 제거됩니다. 서비스 [업데이트](https://azure.microsoft.com/updates/?product=search) 또는 일반 공급에 관한 공지 사항에 대한 [새로운](whats-new.md) 소식을 확인할 수 있습니다.

일부 미리 보기 기능은 포털 및 .NET SDK에서 사용할 수 있지만 REST API에는 항상 미리 보기 기능이 있습니다.

+ 검색 작업의 [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) 경우 현재 미리 보기 버전입니다.
+ 관리 작업의 [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) 경우 현재 미리 보기 버전입니다.

> [!IMPORTANT]
> 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="ai-enrichment-features"></a>AI 강화 기능

미리 보기 검색 API를 통해 AI 보강에 대한 최신 개선 사항 [살펴보기.](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)

|||
|-|-|
| [사용자 지정 엔터티 조회 기술(미리 보기)](cognitive-search-skill-custom-entity-lookup.md ) | 사용자 정의 단어와 구 목록에서 텍스트를 찾는 인지 기술입니다. 이 목록을 사용하면 일치하는 엔터티로 모든 문서에 레이블을 지정합니다. 이 기술은 유사하지만 정확하지 않은 일치 항목을 찾기 위해 적용할 수 있는 퍼지 매칭도 지원합니다. | 
| [PII 탐지 기술(미리 보기)](cognitive-search-skill-pii-detection.md) | 인덱싱 중에 사용되는 인지 기술로 입력 텍스트에서 개인 식별 정보를 추출하고 다양한 방법으로 해당 텍스트에서 마스크할 수 있는 옵션을 제공합니다.| 
| [증분 보강(미리 보기)](cognitive-search-incremental-indexing-conceptual.md) | 보강 파이프라인에 캐싱을 추가하여 기술 집합이나 다른 개체에 대한 업데이트와 같은 대상 수정 사항이 콘텐츠를 변경하지 않는 경우 기존 출력을 재사용할 수 있습니다. 캐싱은 기술 집합에서 생성된 보강 된 문서에만 적용됩니다.| 
| [지식 저장소(미리 보기)](knowledge-store-concept-intro.md) | AI 기반 보강 파이프라인의 새로운 대상입니다. 물리적 데이터 구조는 Azure Blob 저장소 및 Azure Table 저장소에 존재하며 연결된 인지 기술 집합이 있는 인덱서를 실행할 때 생성되고 채워집니다. 기술 저장소 자체의 정의는 기술 집합 정의 내에서 지정됩니다. 지식 저장소 정의 내에서 데이터의 모양, 데이터가 테이블 저장소 또는 Blob 저장소에 저장되는지 여부, 여러 뷰가 있는지 여부를 결정하는 *프로젝션* 요소를 통해 데이터의 물리적 구조를 제어할 수 있습니다.| 

## <a name="indexing-and-query-features"></a>인덱싱 및 쿼리 기능

인덱서 미리 보기 기능은 미리 보기 검색 API에서 사용할 수 있습니다. 

|||
|-|-|
| [Cosmos DB 인덱서](search-howto-index-cosmosdb.md) | MongoDB API(미리 보기), 그렘린 API(미리 보기) 및 카산드라 API(미리 보기) API 형식에 대한 지원. | 
|  [Azure 데이터 레이크 스토리지 Gen2 인덱서(미리 보기)](search-howto-index-azure-data-lake-storage.md) | 데이터 레이크 스토리지 Gen2에서 콘텐츠 및 메타데이터를 인덱싱합니다.| 
| [더좋아이 쿼리 매개 변수 (미리 보기)](search-more-like-this.md) | 특정 문서와 관련된 문서를 찾습니다. 이 기능은 이전 미리 보기에 있었습니다. | 

## <a name="management-features"></a>관리 기능

|||
|-|-|
| [개인 엔드포인트 지원](service-create-private-endpoint.md) | 보안 클라이언트(예: 가상 시스템)를 사용하여 가상 네트워크를 만든 다음 개인 엔드포인트를 사용하는 검색 서비스를 만들 수 있습니다. |
| IP 액세스 제한 | 관리 [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) REST API를 사용하여 액세스가 허용되는 IP 주소에 제한이 있는 서비스를 만들 수 있습니다. |

## <a name="earlier-preview-features"></a>이전 미리 보기 기능

이전 미리 보기에 발표된 기능은 일반 공급으로 전환되지 않은 경우 여전히 공개 미리 보기상태입니다. 이전 미리 보기 API 버전에서 API를 호출하는 경우 계속 해당 버전을 사용하거나 예상되는 동작을 변경하지 않고 `2019-05-06-Preview`로 전환할 수 있습니다.

## <a name="how-to-call-a-preview-api"></a>미리 보기 API를 호출하는 방법

이전 미리 보기가 여전히 작동하지만 시간이 지남에 따라 부실해집니다. 코드가 `api-version=2016-09-01-Preview` 또는 `api-version=2017-11-11-Preview`를 호출하는 경우 이러한 호출은 여전히 유효합니다. 그러나 최신 미리 보기 버전만 향상된 기능으로 새로 고쳐집니다. 

다음 예제 구문은 미리 보기 API 버전에 대한 호출을 설명합니다.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure 인지 검색 서비스는 여러 버전으로 제공됩니다. 자세한 내용은 [API 버전](search-api-versions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

REST API 참조 설명서를 검토합니다. 문제가 발생하면 [StackOverflow에](https://stackoverflow.com/) 대한 도움을 요청하거나 지원 팀에 [문의하십시오.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [검색 서비스 REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)