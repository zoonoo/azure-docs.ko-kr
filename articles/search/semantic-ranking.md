---
title: 의미 등급 순위
titleSuffix: Azure Cognitive Search
description: Cognitive Search의 의미 체계 순위 알고리즘에 대해 설명 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604290"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 순위

> [!IMPORTANT]
> 의미 체계 검색 기능은 미리 보기 상태 이며 미리 보기 REST API 통해서만 사용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있는 그대로 제공 되며 일반 공급 시 동일한 구현을 보장 하지 않습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

의미 등급 순위는 초기 결과 집합과 가장 일치 하는 항목을 검색 하 여 전체 자릿수를 높이고 회수 하는 쿼리 실행 파이프라인의 확장입니다. 의미 체계 순위는 키워드에 대 한 언어적 일치와 달리 자연어로 표현 되는 쿼리에 대해 학습 된 최신 심층 컴퓨터 읽기 이해 모델에 의해 지원 됩니다. [기본 유사성 순위 알고리즘과](index-ranking-similarity.md)달리 의미 체계 ranker는 단어의 컨텍스트와 의미를 사용 하 여 관련성을 결정 합니다.

## <a name="how-semantic-ranking-works"></a>의미 등급의 작동 방식

의미 체계 순위는 리소스 및 시간 집약적입니다. 쿼리 작업의 예상 되는 대기 시간 내에 처리를 완료 하기 위해 모델은 기본 [유사성 순위 알고리즘](index-ranking-similarity.md)에서 반환 된 상위 50 문서만 입력으로 사용 합니다. 초기 순위를 지정 하는 결과에는 50 개 이상의 일치 항목이 포함 될 수 있지만 첫 번째 50은 의미상 reranked 됩니다. 

의미 체계 등급의 경우 모델은 컴퓨터 읽기 이해 및 전송 학습을 모두 사용 하 여 각 항목이 얼마나 잘 쿼리 의도에 따라 문서에 대 한 점수를 다시 지정 합니다.

### <a name="preparation-passage-extraction-phase"></a>준비 (통로 추출) 단계

초기 결과의 각 문서에 대해 키 통로를 식별 하는 통로 추출 연습이 있습니다. 이는 빠르게 처리할 수 있는 양만큼 콘텐츠를 줄이는 다운 된 크기 조정 연습입니다.

1. 각 50 문서에 대해 searchFields 매개 변수의 각 필드는 연속적인 순서로 계산 됩니다. 각 필드의 콘텐츠는 하나의 긴 문자열로 통합 됩니다. 

1. 긴 문자열은 전체 길이가 8000 토큰을 넘지 않도록 트리밍 됩니다. 따라서 문자열에 포함 되도록 간결한 필드를 먼저 배치 하는 것이 좋습니다. 텍스트를 많이 사용 하는 필드가 있는 매우 큰 문서를 사용 하는 경우 토큰 제한 이후의 모든 항목은 무시 됩니다.

1. 이제 각 문서는 최대 8000 토큰의 단일 긴 문자열로 표시 됩니다. 이러한 문자열은 요약 모델로 전송 되므로 문자열을 더 줄일 수 있습니다. 요약 모델은 문서를 가장 잘 요약 하거나 질문에 대답 하는 주요 문장이 나 통로의 긴 문자열을 평가 합니다.

1. 이 단계의 출력은 캡션 (필요에 따라 대답)입니다. 캡션은 문서당 최대 128 토큰으로, 문서를 가장 하는 것으로 간주 됩니다.

### <a name="scoring-and-ranking-phases"></a>점수 매기기 및 순위 지정 단계

이 단계에서는 관련성을 평가 하기 위해 모든 50 캡션이 평가 됩니다.

1. 점수 매기기는 제공 된 쿼리를 기준으로 개념 및 의미 체계 관련성에 대 한 각 캡션을 평가 하 여 결정 됩니다.

   다음 다이어그램에서는 "의미 체계 관련성"의 의미에 대 한 설명을 제공 합니다. 재무, 법률, 지리 또는 문법의 컨텍스트에서 사용할 수 있는 "대문자" 라는 용어를 생각해 보세요. 쿼리에 동일한 벡터 공간의 용어 (예: "대문자" 및 "투자")가 포함 되어 있는 경우 동일한 클러스터에도 토큰을 포함 하는 문서는 그렇지 않은 것 보다 점수가 높습니다.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="컨텍스트에 대 한 벡터 표현" border="true":::

1. 이 단계의 출력은 @search.rerankerScore 각 문서에 할당 됩니다. 모든 문서가 점수가 매겨진 후에는 내림차순으로 나열 되 고 쿼리 응답 페이로드에 포함 됩니다.

## <a name="next-steps"></a>다음 단계

의미 등급 순위는 특정 지역의 표준 계층에서 제공 됩니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요. 새 쿼리 유형을 사용 하면 의미 체계 검색의 관련성 순위 및 응답 구조를 사용할 수 있습니다. 시작 하려면 [의미 체계 쿼리를 만듭니다](semantic-how-to-query-request.md).

또는 관련 정보는 다음 문서 중 하나를 참조 하세요.

+ [의미 체계 검색 개요](semantic-search-overview.md)
+ [의미 대답 반환](semantic-answers.md)