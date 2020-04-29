---
title: 유사성 및 점수 매기기 개요
titleSuffix: Azure Cognitive Search
description: 유사성 및 점수 매기기의 개념과 개발자가 점수 매기기 결과를 사용자 지정 하기 위해 수행할 수 있는 작업을 설명 합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 9f9cc4c29b117c83595a36c4e28b1edb428c3cde
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254123"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure Cognitive Search의 유사성 및 점수 매기기

점수 매기기는 전체 텍스트 검색 쿼리에 대 한 검색 결과에서 반환 되는 모든 항목에 대 한 검색 점수 계산을 나타냅니다. 점수는 현재 검색 작업의 컨텍스트에서 항목의 관련성을 나타내는 표시기입니다. 점수가 높을수록 항목의 관련성도 높습니다. 검색 결과에서 항목은 각 항목에 대해 계산된 검색 점수를 기준으로 높은 순위부터 낮은 순위로 정렬됩니다. 

기본적으로 상위 50는 응답에 반환 되지만 **$top** 매개 변수를 사용 하 여 더 작거나 더 많은 항목 (최대 1000 개의 항목)을 반환 하 고 **$skip** 하 여 다음 결과 집합을 가져올 수 있습니다.

검색 점수는 쿼리와 데이터의 통계 속성에 따라 계산됩니다. Azure Cognitive Search는 검색 용어의 여러 인스턴스를 포함 하는 문서를 찾은 다음 검색 용어 (일부 또는 [모두)와](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional)일치 하는 문서를 찾습니다. 데이터가 데이터 인덱스 전체에서 드물게 발생 하지만 문서 내에서 자주 발생 하는 경우 검색 점수가 더 높아집니다. 이러한 접근 방식에 대 한 이러한 접근 방식의 기준은 *TF-IDF 또는* 용어 빈도-역 문서 빈도 라고 합니다.

전체 결과 집합에서 검색 점수 값이 반복될 수 있습니다. 여러 적중 항목의 검색 점수가 동일한 경우 점수가 매겨진 동일한 항목의 순서는 정의 되지 않으며 안정적이 지 않습니다. 쿼리를 다시 실행 하면 특히 무료 서비스를 사용 하는 경우 또는 여러 복제본이 있는 청구 가능한 서비스를 사용 하는 경우 항목 이동 위치가 표시 될 수 있습니다. 즉, 두 항목의 점수가 같은 경우 어떤 항목이 먼저 표시되는지 보장되지 않습니다.

반복 점수 간에 연결을 중단 하려면 먼저 **$orderby** 절을 점수를 기준으로 정렬 한 다음 정렬 가능한 다른 필드 (예: `$orderby=search.score() desc,Rating desc`)로 정렬 합니다. 자세한 내용은 [$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby)를 참조 하세요.

> [!NOTE]
> 는 `@search.score = 1.00` 점수가 매겨지지 않거나 순위가 지정 되지 않은 결과 집합을 나타냅니다. 점수는 모든 결과에서 균일 합니다. 점수를 매긴 결과는 쿼리 양식이 유사 항목 검색, 와일드 카드 또는 regex 쿼리 또는 **$filter** 식일 때 발생 합니다. 

## <a name="scoring-profiles"></a>점수 매기기 프로필

사용자 지정 *점수 매기기 프로필*을 정의 하 여 여러 필드의 순위가 지정 되는 방식을 사용자 지정할 수 있습니다. 점수 매기기 프로필을 사용하면 검색 결과에서 항목의 순위를 보다 강력하게 제어할 수 있습니다. 예를 들어 잠재 수익을 기준으로 하여 특정 항목을 상승시키거나, 새 항목을 프로모션하거나, 너무 오랫동안 재고에 포함되어 있던 항목을 상승시킬 수 있습니다. 

점수 매기기 프로필은 가중 필드, 함수 및 매개 변수로 구성된 인덱스 정의의 일부입니다. 정의에 대 한 자세한 내용은 [점수 매기기 프로필](index-add-scoring-profiles.md)을 참조 하세요.

## <a name="scoring-statistics"></a>점수 매기기 통계

확장성을 위해 Azure Cognitive Search는 분할 프로세스를 통해 각 인덱스를 가로로 분산 합니다. 즉, 인덱스의 일부가 물리적으로 분리 되어 있습니다.

기본적으로 문서 *점수는 분할 된 데이터의*통계 속성에 따라 계산 됩니다. 이 방법은 일반적으로 많은 양의 데이터에 대 한 문제가 아니라 모든 분할에 대 한 정보를 기준으로 점수를 계산 하는 것 보다 더 나은 성능을 제공 합니다 모음. 즉, 이러한 성능 최적화를 사용 하면 매우 유사한 두 개의 문서 (또는 동일한 문서)가 다른 분할에서 종료 되는 경우 서로 다른 관련성 점수를 사용 하 여 종료 될 수 있습니다.

모든 분할에서 통계 속성을 기준으로 점수를 계산 하려는 경우 *scoringStatistics = global* 을 [쿼리 매개 변수로](https://docs.microsoft.com/rest/api/searchservice/search-documents) 추가 하거나, [쿼리 요청의](https://docs.microsoft.com/rest/api/searchservice/search-documents)본문 매개 변수로 *"scoringStatistics": "global"* 을 추가 하 여이 작업을 수행할 수 있습니다.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global
  Content-Type: application/json
  api-key: [admin key]  
```

> [!NOTE]
> `scoringStatistics` 매개 변수에는 admin api 키가 필요 합니다.

## <a name="similarity-ranking-algorithms"></a>유사성 순위 알고리즘

Azure Cognitive Search는 두 가지 유사성 순위 알고리즘을 지원 합니다. *클래식 유사성* 알고리즘과 *okapi BM25* 알고리즘 (현재 미리 보기)의 공식 구현입니다. 기존 유사성 알고리즘은 기본 알고리즘 이지만 7 월 15 일부 터 해당 날짜 이후에 만들어진 모든 새 서비스는 새 BM25 알고리즘을 사용 합니다. 새 서비스에서 유일 하 게 사용할 수 있는 알고리즘입니다.

지금은 사용할 유사성 순위 알고리즘을 지정할 수 있습니다. 자세한 내용은 [순위 알고리즘](index-ranking-similarity.md)을 참조 하세요.

## <a name="watch-this-video"></a>이 동영상 시청

이 16 분 분량의 비디오에서는 소프트웨어 엔지니어 Raouf Merouche에서 인덱싱, 쿼리 및 점수 매기기 프로필을 만드는 방법에 대해 설명 합니다. 문서를 인덱싱하고 검색할 때 내부적으로 진행 되는 상황을 이해 하는 데 도움이 됩니다.

>[!VIDEO https://channel9.msdn.com/Shows/AI-Show/Similarity-and-Scoring-in-Azure-Cognitive-Search/player]

+ 2-3 분의 인덱싱: 텍스트 처리 및 어휘 분석
+ 3-4 분 (인덱스 포함): 반전 인덱스.
+ 4-6 분 쿼리: 검색 및 순위 지정
+ 7-16 분은 점수 매기기 프로필을 포함 합니다.

## <a name="see-also"></a>참조

 [점수 매기기 프로필](index-add-scoring-profiles.md) [REST API 참조](https://docs.microsoft.com/rest/api/searchservice/)   
 [문서 검색 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
