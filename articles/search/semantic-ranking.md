---
title: 의미 등급 순위
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 의미 체계 순위 알고리즘이 작동 하는 방식에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562038"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 순위

> [!IMPORTANT]
> 의미 체계 검색 기능은 preview REST API 및 포털을 통해 공개 미리 보기로 제공 됩니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있는 그대로 제공 되며 일반 공급 시 동일한 구현을 보장 하지 않습니다. 이러한 기능은 청구 가능 합니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

의미 등급 순위는 초기 결과 집합과 가장 일치 하는 항목을 검색 하 여 전체 자릿수를 향상 시키는 쿼리 실행 파이프라인의 확장입니다. 의미 등급 순위는 키워드에 대 한 언어적 일치가 아닌 쿼리 용어의 의미 체계를 캡처하기 위해 학습 된, 많은 변환기 기반 네트워크에서 지원 됩니다. [기본 유사성 순위 알고리즘과](index-ranking-similarity.md)달리 의미 체계 ranker는 단어의 컨텍스트와 의미를 사용 하 여 관련성을 결정 합니다.

의미 체계 순위는 리소스 및 시간 집약적입니다. 쿼리 작업의 예상 되는 대기 시간 내에 처리를 완료 하기 위해 의미 체계 ranker에 대 한 입력이 통합 및 감소 되어 기본 요약 및 작성 된 단계가 최대한 빠르게 완료 될 수 있습니다.

## <a name="pre-processing"></a>전처리

관련성을 평가 하기 전에 의미 체계 ranker 효율적으로 처리할 수 있는 관리 가능한 입력 수로 콘텐츠를 줄여야 합니다.

1. 첫째, 콘텐츠 감소는 키워드 검색에 사용 되는 기본 [유사성 순위 알고리즘](index-ranking-similarity.md) 에서 반환 하는 초기 결과 집합으로 시작 합니다. 지정 된 쿼리의 경우 결과는 최대 제한인 1000까지 몇 개의 문서로 구성 될 수 있습니다. 많은 수의 일치 항목을 처리 하는 데 너무 오래 걸리므로 의미 체계 순위에는 상위 50 진행률이 있습니다.

   문서 수에 관계 없이 1 또는 50 인지 여부에 관계 없이 초기 결과 집합은 모음 문서의 첫 번째 반복을 설정 합니다.

1. 그런 다음, 모음에서 "searchFields"에 있는 각 필드의 내용을 추출 하 여 긴 문자열로 결합 합니다.

1. 문자열 통합 후에는 너무 긴 문자열은 전체 길이가 요약 단계의 입력 요구 사항을 충족 하는지 확인 하기 위해 잘립니다.

   이 트리밍 연습은 문자열에 포함 되도록 간결한 필드를 먼저 "searchFields"에 배치 하는 것이 중요 한 이유입니다. 텍스트를 많이 사용 하는 필드가 있는 매우 큰 문서를 사용 하는 경우 최대 제한 이후의 모든 항목은 무시 됩니다.

이제 각 문서는 단일 긴 문자열로 표시 됩니다.

> [!NOTE]
> 문자열은 문자 또는 단어가 아닌 토큰으로 구성 됩니다. 토큰화는 검색 가능한 필드에 대 한 분석기 할당에 의해 부분적으로 결정 됩니다. NGram 또는 EdgeNGram 같은 특수 분석기를 사용 하는 경우 searchFields에서 해당 필드를 제외 하는 것이 좋습니다. 문자열을 토큰화 하는 방법에 대 한 자세한 내용은 [REST API Test analyzer](/rest/api/searchservice/test-analyzer)를 사용 하 여 분석기의 토큰 출력을 검토 하면 됩니다.

## <a name="extraction"></a>추출

문자열을 줄인 후에는 컴퓨터 읽기 이해 및 언어 표현 모델을 통해 축소 된 입력을 전달 하 여 문서를 가장 잘 요약 하 여 쿼리를 기준으로 하는 문장 및 구를 결정할 수 있습니다. 이 단계에서는 의미 체계 ranker 앞으로 이동할 문자열에서 콘텐츠를 추출 합니다.

요약에 대 한 입력은 준비 단계에서 각 문서에 대해 가져온 긴 문자열입니다. 각 문자열에서 요약 모델은 가장 대표적인 통로를 찾습니다. 또한이 통로는 문서에 대 한 [의미 체계 캡션을](semantic-how-to-query-request.md) 구성 합니다. 각 캡션은 일반 텍스트 버전 및 강조 표시 버전으로 사용할 수 있으며 문서 당 200 단어 미만으로 자주 사용 됩니다.

"Answer" 매개 변수를 지정 하 고, 쿼리가 질문으로 발생 한 경우 및 질문에 대 한 답을 제공 하는 긴 문자열에서 통로를 찾을 수 있는 경우에도 [의미 대답이](semantic-answers.md) 반환 됩니다.

## <a name="semantic-ranking"></a>의미 등급 순위

1. 캡션은 제공 된 쿼리를 기준으로 개념 및 의미 체계 관련성에 대해 평가 됩니다.

   다음 다이어그램에서는 "의미 체계 관련성"의 의미에 대 한 설명을 제공 합니다. 재무, 법률, 지리 또는 문법의 컨텍스트에서 사용할 수 있는 "대문자" 라는 용어를 생각해 보세요. 쿼리에 동일한 벡터 공간의 용어 (예: "대문자" 및 "투자")가 포함 되어 있는 경우 동일한 클러스터에도 토큰을 포함 하는 문서는 그렇지 않은 것 보다 점수가 높습니다.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="컨텍스트에 대 한 벡터 표현" border="true":::

1. 는 @search.rerankerScore 캡션의 의미 체계에 따라 각 문서에 할당 됩니다.

1. 모든 문서가 점수가 매겨진 후에는 점수를 기준으로 내림차순으로 나열 되 고 쿼리 응답 페이로드에 포함 됩니다. 페이로드에는 대답, 일반 텍스트 및 강조 표시 된 캡션과 select 절에서 검색할 수 있거나 지정 된 것으로 표시 된 모든 필드가 포함 됩니다.

## <a name="next-steps"></a>다음 단계

의미 등급 순위는 특정 지역의 표준 계층에서 제공 됩니다. 가용성 및 등록에 대 한 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요. 새 쿼리 유형을 사용 하면 의미 체계 검색의 순위 및 응답 구조를 사용할 수 있습니다. 시작 하려면 [의미 체계 쿼리를 만듭니다](semantic-how-to-query-request.md).

또는 기본 순위에 대 한 다음 문서를 검토 합니다. 의미 등급 순위는 첫 번째 결과를 반환 하기 위한 유사성 ranker에 따라 달라 집니다. 쿼리 실행과 순위에 대해 알고 있으면 전체 프로세스가 작동 하는 방식을 광범위 하 게 이해 하 게 됩니다.

+ [Azure Cognitive Search의 전체 텍스트 검색](search-lucene-query-architecture.md)
+ [Azure Cognitive Search의 유사성 및 점수 매기기](index-similarity-and-scoring.md)
+ [Azure Cognitive Search에서 텍스트 처리를 위한 분석기](search-analyzers.md)