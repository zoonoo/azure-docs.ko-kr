---
title: 의미 체계 검색
titleSuffix: Azure Cognitive Search
description: Bing의 심층 학습 의미 체계 검색 모델을 사용 하 여 검색 결과를 보다 직관적으로 만드는 Cognitive Search 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: references_regions
ms.openlocfilehash: 443d6349aab68fd05edfe4c4007fd043c932f4f0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604273"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 검색

> [!IMPORTANT]
> 의미 체계 검색은 preview REST API 통해서만 사용할 수 있는 공개 미리 보기 상태입니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있는 그대로 제공 되며 일반 공급 시 동일한 구현을 보장 하지 않습니다. 이러한 기능은 청구 가능 합니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

의미 체계 검색은 보다 품질이 우수한 쿼리 환경을 지 원하는 쿼리 관련 기능의 모음입니다. 

이러한 기능에는 관련 용어 및 구에 대 한 의미 체계 강조 표시와 함께 검색 결과의 의미 체계를 비롯 하 여 캡션 및 대답 추출이 포함 됩니다. 사전 학습 된 최신 모델을 추출 및 순위 지정에 사용 합니다. 사용자가 검색에서 필요로 하는 빠른 성능을 유지 하기 위해 의미 체계 요약 및 순위는 [기본 유사성 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)에 따라 점수가 매겨진 상위 50 결과에만 적용 됩니다. 이러한 결과를 문서 모음 사용 하면 의미 체계 순위가 일치의 의미 체계 강도에 따라 결과의 점수를 다시 지정 합니다.

기본 기술은 Bing 및 Microsoft Research에서 추가 기능으로 Cognitive Search 인프라에 통합 되어 있습니다. 의미 체계 검색을 지 원하는 연구 및 AI 투자에 대 한 자세한 내용은 [Bing의 ai가 Azure Cognitive Search를 켜는 방법 (Microsoft Research 블로그)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)을 참조 하세요.

다음 비디오는 기능에 대 한 개요를 제공 합니다.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>구성 요소 및 워크플로

의미 체계 검색은 다음과 같은 기능을 추가 하 여 정밀도 및 회수를 향상 시킵니다.

| 기능 | 설명 |
|---------|-------------|
| [맞춤법 검사](speller-how-to-add.md) | 쿼리 용어가 검색 엔진에 도달 하기 전에 오타를 수정 합니다. |
| [의미 등급 순위](semantic-ranking.md) | 는 컨텍스트나 의미 체계를 사용 하 여 새 관련성 점수를 계산 합니다. |
| [의미 체계 캡션과 강조 표시](semantic-how-to-query-request.md) | 콘텐츠를 가장 잘 요약 한 문서의 문장과 구에, 간편한 검색을 위한 주요 통로를 강조 합니다. 결과 페이지에서 개별 콘텐츠 필드가 너무 조밀한 경우 결과를 요약 하는 캡션을 유용 하 게 사용할 수 있습니다. 강조 표시 된 텍스트는 사용자가 일치 항목으로 간주 되는 이유를 빠르게 확인할 수 있도록 가장 관련성이 높은 용어와 구를 상승 합니다. |
| [의미 체계 대답](semantic-answers.md) | 의미 체계 쿼리에서 반환 되는 선택적 및 추가 하위 구조입니다. 질문 처럼 보이는 쿼리에 직접 대답을 제공 합니다. |

### <a name="order-of-operations"></a>작업 순서

의미 체계 검색의 구성 요소는 기존 쿼리 실행 파이프라인을 양방향으로 확장 합니다. 맞춤법 수정 기능을 사용 하는 경우 쿼리 용어가 검색 엔진에 도달 하기 전에 맞춤법 [검사기](speller-how-to-add.md) 가 시작 시 오타를 수정 합니다.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="쿼리 실행의 의미 체계 구성 요소" border="true":::

쿼리 실행은 일반적인 방식으로 진행 되며, 용어 구문 분석, 분석 및 반전 된 인덱스를 검색 합니다. 엔진은 토큰 일치를 사용 하 여 문서를 검색 하 고 [기본 유사성 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)을 사용 하 여 결과의 점수를 계산 합니다. 점수는 인덱스에서 쿼리 용어와 일치 하는 용어 간의 언어적 유사성을 기준으로 계산 됩니다. 정의한 경우 점수 매기기 프로필도이 단계에서 적용 됩니다. 그런 다음 결과가 의미 체계 검색 하위 시스템에 전달 됩니다.

준비 단계에서 초기 결과 집합에서 반환 된 문서 모음는 각 문서를 요약 하는 통로를 찾기 위해 문장과 단락 수준에서 분석 됩니다. 키워드 검색과 달리이 단계에서는 컴퓨터 읽기와 이해력을 사용 하 여 콘텐츠를 평가 합니다. 결과 컴포지션의 일부로 의미 체계 쿼리는 캡션과 답변을 반환 합니다. 이를 공식화 하기 위해 의미 체계 검색에서는 언어 표현을 사용 하 여 결과를 가장 잘 요약 하는 키 통로를 추출 하 고 강조 표시 합니다. 검색 쿼리가 질문이 고 답변이 요청 된 경우 응답에는 검색 쿼리로 표현 되는 질문에 가장 잘 대답 하는 텍스트 통로가 포함 됩니다. 캡션과 답변 모두에 대해 기존 텍스트는 공식화에서 사용 됩니다. 의미 체계 모델은 사용 가능한 콘텐츠에서 새로운 문장이 나 문구를 작성 하지 않으며 새로운 결론에 도달할 수 있는 논리를 적용 하지 않습니다. 즉, 시스템은 아직 존재 하지 않는 콘텐츠는 반환 하지 않습니다.

그런 다음 쿼리 용어의 [개념적 유사성](semantic-ranking.md) 을 기준으로 결과를 다시 점수가 매겨집니다.

쿼리에서 의미 체계 기능을 사용 하려면 [검색 요청](semantic-how-to-query-request.md)을 약간만 수정 해야 하지만 추가 구성 이나 인덱스는 필요 하지 않습니다.

## <a name="availability-and-pricing"></a>가용성 및 가격 책정

의미 체계 기능은 미국 중 북부, 미국 서 부, 미국 서 부 2, 미국 동부 2, 서유럽, 유럽 서부 등의 지역 중 하나에 있는 표준 계층 (S1, S2, S3)에서 만든 검색 서비스에 대 한 등록 [등록](https://aka.ms/SemanticSearchPreviewSignup)을 통해 사용할 수 있습니다. 

맞춤법 수정은 동일한 지역에서 사용할 수 있지만 계층 제한은 없습니다. 계층 및 지역 조건을 충족 하는 기존 서비스가 있으면 등록만 필요 합니다.

3 월 2 일부 터 3 월 1 일까 지 미리 보기 시작 사이에는 무료로 제공 됩니다. 4 월 1 일부 터이 기능을 실행 하는 계산 비용은 청구 가능 이벤트가 됩니다. 예상 비용은 25만 쿼리의 경우 약 USD $500/월입니다. [Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/) 에서 설명 하 고 [비용을 예측 하 고 관리](search-sku-manage-costs.md)하는 자세한 비용 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

새 쿼리 유형을 사용 하면 의미 체계 검색의 관련성 순위 및 응답 구조를 사용할 수 있습니다.

[의미 체계 쿼리를 만들어](semantic-how-to-query-request.md) 시작 합니다. 또는 관련 정보는 다음 문서를 참조 하세요.

+ [쿼리 용어에 맞춤법 검사 추가](speller-how-to-add.md)
+ [의미 대답 반환](semantic-answers.md)
+ [의미 등급 순위](semantic-ranking.md)
+ [의미 체계 검색 소개 (블로그 게시물)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [의미 체계 기능을 사용 하 여 의미 있는 정보 찾기 (AI 비디오)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)