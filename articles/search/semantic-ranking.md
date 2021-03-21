---
title: 의미 등급 순위
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 의미 체계 순위 알고리즘이 작동 하는 방식에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bb65a53f1ba6e97a39bd0c0170c5c41da38aee8b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720511"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 순위

> [!IMPORTANT]
> 의미 체계 검색 기능은 미리 보기 상태 이며 미리 보기 REST API 통해서만 사용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있는 그대로 제공 되며 일반 공급 시 동일한 구현을 보장 하지 않습니다. 이러한 기능은 청구 가능 합니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

의미 등급 순위는 초기 결과 집합과 가장 일치 하는 항목을 검색 하 여 전체 자릿수를 높이고 회수 하는 쿼리 실행 파이프라인의 확장입니다. 의미 등급 순위는 키워드에 대 한 언어적 일치와 달리 자연어로 표현 되는 쿼리에 대해 학습 된 최신 기계 읽기 이해 모델에 의해 지원 됩니다. [기본 유사성 순위 알고리즘과](index-ranking-similarity.md)달리 의미 체계 ranker는 단어의 컨텍스트와 의미를 사용 하 여 관련성을 결정 합니다.

의미 체계 순위는 리소스 및 시간 집약적입니다. 쿼리 작업의 예상 되는 대기 시간 내에 처리를 완료 하기 위해 입력은 최대한 빠르게 요약 및 분석을 완료할 수 있도록 통합 및 간소화 됩니다.

## <a name="preparation-for-semantic-ranking"></a>의미 등급 순위 준비

관련성을 평가 하기 전에 의미 체계 ranker 효율적으로 처리할 수 있는 매개 변수 수량으로 콘텐츠를 줄여야 합니다. 콘텐츠 축소는 다음과 같은 일련의 단계를 포함 합니다.

1. 콘텐츠 감소는 키워드 검색에 사용 되는 기본 [유사성 순위 알고리즘](index-ranking-similarity.md) 에서 반환 된 초기 결과를 사용 하 여 시작 됩니다. 검색 결과에는 최대 1000 일치 항목이 포함 될 수 있지만 의미 체계 순위는 상위 50만 처리 합니다. 

   쿼리가 제공 된 경우 일치 하는 항목의 수에 따라 초기 결과가 50 보다 훨씬 적을 수 있습니다. 문서 수에 관계 없이 초기 결과 집합은 의미 체계 순위에 대 한 문서 모음입니다.

1. 문서 모음 "searchFields"에 있는 각 필드의 내용을 추출 하 여 긴 문자열로 결합 합니다.

1. 과도 한 긴 문자열은 전체 길이가 요약 모델의 입력 요구 사항을 충족 하는지 확인 하기 위해 잘립니다. 이 트리밍 연습은 문자열에 포함 되도록 간결한 필드를 먼저 "searchFields"에 배치 하는 것이 중요 한 이유입니다. 텍스트를 많이 사용 하는 필드가 있는 매우 큰 문서를 사용 하는 경우 최대 제한 이후의 모든 항목은 무시 됩니다.

이제 각 문서는 단일 긴 문자열로 표시 됩니다.

> [!NOTE]
> 모델에 대 한 매개 변수 입력은 문자 또는 단어가 아닌 토큰입니다. 토큰화는 검색 가능한 필드에 대 한 분석기 할당에 의해 부분적으로 결정 됩니다. 문자열을 토큰화 하는 방법에 대 한 자세한 내용은 [REST API Test analyzer](/rest/api/searchservice/test-analyzer)를 사용 하 여 분석기의 토큰 출력을 검토 하면 됩니다.
>
> 현재이 미리 보기에서 긴 문자열은 최대 8000 토큰 크기를 사용할 수 있습니다. 검색이 문서 내에서 예상 된 대답을 제공 하지 못하는 경우 콘텐츠 트리밍을 알면 이유를 이해 하는 데 도움이 됩니다. 

## <a name="summarization"></a>요약

문자열을 축소 한 후에는 쿼리를 기준으로 하 여 모델을 가장 잘 요약 한 문장 및 구를 결정 하기 위해 기계 읽기 이해력 및 언어 표현을 통해 매개 변수를 전달할 수 있습니다.

요약에 대 한 입력은 준비 단계에서 긴 문자열입니다. 이 입력에서 요약 모델은 콘텐츠를 평가 하 여 가장 대표적인 통로를 찾습니다.

출력은 [의미 체계 캡션](semantic-how-to-query-request.md)(일반 텍스트 및 강조 표시)입니다. 캡션은 긴 문자열 (일반적으로 문서 당 200 단어 미만) 보다 작으며 문서를 가장 하는 것으로 간주 됩니다. 

"Answer" 매개 변수를 지정 하 고, 쿼리가 질문으로 발생 한 경우 및 질문에 대 한 타당 대답 처럼 보이는 긴 문자열에서 통로를 찾을 수 있는 경우에도 [의미 대답이](semantic-answers.md) 반환 됩니다.

## <a name="scoring-and-ranking"></a>점수 매기기 및 순위 지정

이제 각 문서에 대 한 캡션이 있습니다. 캡션은 쿼리와 관련성이 높은 것으로 평가 됩니다.

1. 점수 매기기는 제공 된 쿼리를 기준으로 개념 및 의미 체계 관련성에 대 한 각 캡션을 평가 하 여 결정 됩니다.

   다음 다이어그램에서는 "의미 체계 관련성"의 의미에 대 한 설명을 제공 합니다. 재무, 법률, 지리 또는 문법의 컨텍스트에서 사용할 수 있는 "대문자" 라는 용어를 생각해 보세요. 쿼리에 동일한 벡터 공간의 용어 (예: "대문자" 및 "투자")가 포함 되어 있는 경우 동일한 클러스터에도 토큰을 포함 하는 문서는 그렇지 않은 것 보다 점수가 높습니다.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="컨텍스트에 대 한 벡터 표현" border="true":::

1. 이 단계의 출력은 @search.rerankerScore 각 문서에 할당 됩니다. 모든 문서가 점수가 매겨진 후에는 내림차순으로 나열 되 고 쿼리 응답 페이로드에 포함 됩니다. 페이로드에는 대답, 일반 텍스트 및 강조 표시 된 캡션과 select 절에서 검색할 수 있거나 지정 된 것으로 표시 된 모든 필드가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

의미 등급 순위는 특정 지역의 표준 계층에서 제공 됩니다. 사용 가능 및 등록에 대 한 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요. 새 쿼리 유형을 사용 하면 의미 체계 검색의 관련성 순위 및 응답 구조를 사용할 수 있습니다. 시작 하려면 [의미 체계 쿼리를 만듭니다](semantic-how-to-query-request.md).

또는 기본 순위에 대 한 다음 문서를 검토 합니다. 의미 등급 순위는 첫 번째 결과를 반환 하기 위한 유사성 ranker에 따라 달라 집니다. 쿼리 실행과 순위에 대해 알고 있으면 전체 프로세스가 작동 하는 방식을 광범위 하 게 이해 하 게 됩니다.

+ [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md)
+ [Azure Cognitive Search의 유사성 및 점수 매기기](index-similarity-and-scoring.md)
+ [Azure Cognitive Search에서 텍스트 처리를 위한 분석기](search-analyzers.md)