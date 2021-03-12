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
ms.openlocfilehash: a008551ac6f149617feedd01e256b637f83e975d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235064"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 순위

> [!IMPORTANT]
> 의미 체계 검색 기능은 미리 보기 상태 이며 미리 보기 REST API 통해서만 사용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있는 그대로 제공 되며 일반 공급 시 동일한 구현을 보장 하지 않습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

의미 등급 순위는 초기 결과 집합과 가장 일치 하는 항목을 검색 하 여 전체 자릿수를 높이고 회수 하는 쿼리 실행 파이프라인의 확장입니다. 의미 체계 순위는 키워드에 대 한 언어적 일치와 달리 자연어로 표현 되는 쿼리에 대해 학습 된 최신 심층 컴퓨터 읽기 이해 모델에 의해 지원 됩니다. [기본 유사성 순위 알고리즘과](index-ranking-similarity.md)달리 의미 체계 ranker는 단어의 컨텍스트와 의미를 사용 하 여 관련성을 결정 합니다.

## <a name="how-semantic-ranking-works"></a>의미 등급의 작동 방식

의미 체계 순위는 리소스 및 시간 집약적입니다. 쿼리 작업의 예상 되는 대기 시간 내에 처리를 완료 하기 위해 모델은 기본 [유사성 순위 알고리즘](index-ranking-similarity.md)에서 반환 된 상위 50 문서만 입력으로 사용 합니다. 초기 순위를 지정 하는 결과에는 50 개 이상의 일치 항목이 포함 될 수 있지만 첫 번째 50은 의미상 reranked 됩니다. 

의미 체계 등급의 경우 모델은 컴퓨터 읽기 이해 및 전송 학습을 모두 사용 하 여 각 항목이 얼마나 잘 쿼리 의도에 따라 문서에 대 한 점수를 다시 지정 합니다.

1. 각 문서에 대해 의미 체계 ranker는 searchFields 매개 변수의 필드를 순서 대로 평가 하 여 콘텐츠를 하나의 커다란 문자열로 통합 합니다.

1. 그런 다음 전체 길이가 2만 토큰을 초과 하지 않도록 문자열을 잘라냅니다. 콘텐츠 필드 또는 merged_content 필드의 내용이 많은 문서를 포함 하는 매우 큰 문서를 사용 하는 경우 처음 2만 토큰만 사용 됩니다.

1. 이제 각 50 문서는 최대 2만 토큰의 단일 긴 문자열로 표시 됩니다. 이 문자열은 요약 모델에 전송 됩니다. 요약 모델은 기계 판독값 이해력을 사용 하 여 콘텐츠를 요약 하거나 질문에 대답 하는 통로를 식별 하는 캡션 (및 답변)을 생성 합니다. 요약 모델의 출력은 최대 128 토큰에 해당 하는 더 적은 수의 문자열입니다.

1. 작은 문자열은 문서의 캡션이 되며 큰 문자열에서 찾은 가장 관련성이 높은 통로를 나타냅니다. 그런 다음 50 개 이하의 캡션 집합을 순서 관련성으로 순위가 매겨집니다. 

개념 및 의미 체계 관련성은 벡터 표현과 용어 클러스터를 통해 설정 됩니다. 키워드 유사성 알고리즘은 쿼리의 모든 용어에 동일한 가중치를 제공할 수 있는 반면, 의미 체계 모델은 표면의 상호 종속성 및 관계를 인식 하도록 학습 된 것입니다. 결과적으로, 쿼리 문자열에 동일한 클러스터의 용어가 포함 된 경우 둘 다를 포함 하는 문서는 그렇지 않은 것 보다 우선 순위가 높습니다.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="컨텍스트에 대 한 벡터 표현" border="true":::

## <a name="next-steps"></a>다음 단계

의미 등급 순위는 특정 지역의 표준 계층에서 제공 됩니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

새 쿼리 유형을 사용 하면 의미 체계 검색의 관련성 순위 및 응답 구조를 사용할 수 있습니다. [의미 체계 쿼리를 만들어](semantic-how-to-query-request.md) 시작 합니다.

또는 관련 정보는 다음 문서 중 하나를 참조 하세요.

+ [쿼리 용어에 맞춤법 검사 추가](speller-how-to-add.md)
+ [의미 대답 반환](semantic-answers.md)