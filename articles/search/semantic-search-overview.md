---
title: 의미 체계 검색
titleSuffix: Azure Cognitive Search
description: Bing의 심층 학습 의미 체계 검색 모델을 사용 하 여 검색 결과를 보다 직관적으로 만드는 Cognitive Search 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: eedb3dfeafbd378cfff7afb04fcc2b9aa6e791d6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679825"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 검색

> [!IMPORTANT]
> 의미 체계 검색 기능은 미리 보기 상태 이며 미리 보기 REST API 통해서만 사용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 있는 그대로 제공 됩니다.

의미 체계 검색은 보다 품질이 우수한 쿼리 환경을 지 원하는 쿼리 관련 기능의 모음입니다. 기능에는 검색 결과의 의미 체계를 비롯 하 여 의미 체계 강조 표시로 캡션 및 답변 생성이 포함 됩니다. [전체 텍스트 검색 엔진](search-lucene-query-architecture.md) 에서 반환 된 상위 50 결과는 가장 관련성이 높은 일치 항목을 찾는 데 reranked 됩니다.

기본 기술은 Bing 및 Microsoft Research의 투자를 활용 하 고 Cognitive Search 인프라에 통합 됩니다. 이를 사용 하려면 검색 요청을 약간만 수정 해야 하지만 추가 구성 이나 다시 인덱스는 필요 하지 않습니다.

공개 미리 보기 기능에는 다음이 포함 됩니다.

+ 검색 쿼리 용어의 컨텍스트나 의미 체계 의미에 따라 관련 문서를 검색 하는 의미 체계 순위 알고리즘
+ 관련 된 통로를 강조 표시 하는 의미 체계 캡션
+ 결과에서 작성 된 쿼리에 대 한 의미 체계 대답
+ 쿼리 용어가 검색 엔진에 도달 하기 전에 오타를 수정 하는 맞춤법 검사

## <a name="semantic-search-architecture"></a>의미 체계 검색 아키텍처

의미 체계 검색의 구성 요소는 기존 쿼리 실행 파이프라인 위에 계층화 됩니다. 맞춤법 수정 (다이어그램에는 표시 되지 않음)은 개별 쿼리 용어로 오타를 수정 하 여 회수를 향상 시킵니다. 모든 구문 분석 및 분석이 완료 되 면 검색 엔진이 쿼리와 일치 하는 문서를 검색 하 고 서비스를 만든 시간에 따라 [기본 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)(BM25 또는 클래식)을 사용 하 여 점수를 계산 합니다. 점수 매기기 프로필도이 단계에서 적용 됩니다. 

상위 50 일치 항목을 받은 경우 [의미 등급 알고리즘](semantic-how-to-query-response.md) 은 문서 모음을 다시 평가 합니다. 결과는 50 개 보다 많은 일치를 포함할 수 있지만 첫 번째 50만 reranked 됩니다. 등급을 지정 하기 위해 알고리즘은 기계 학습 및 전송 학습을 모두 사용 하 여 각 항목이 얼마나 잘 쿼리 의도에 따라 문서에 대 한 점수를 다시 지정 합니다.

캡션 및 대답을 만들려면 언어 표시 모델을 사용 합니다. 캡션 내에서 알고리즘은 Bing에서 개발한 언어 모델을 사용 하 여 쿼리 결과를 가장 잘 요약 한 강조 표시로 캡션을 추출 합니다. 검색 쿼리가 질문이 고 답변이 요청 된 경우 유사한 언어 모델은 검색 쿼리로 표현 되는 질문에 가장 잘 대답 하는 텍스트 통로를 식별 합니다.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="쿼리 파이프라인의 의미 체계 구성 요소" border="true":::

## <a name="availability-and-pricing"></a>가용성 및 가격 책정

의미 체계 순위는 미국 중 북부, 미국 서 부, 미국 서 부 2, 미국 동부 2, 서유럽, 유럽 서부 등의 지역 중 하나에 있는 표준 계층 (S1, S2, S3)에서 만든 검색 서비스에 대 한 등록 [등록](https://aka.ms/SemanticSearchPreviewSignup)을 통해 사용할 수 있습니다. 맞춤법 수정은 동일한 지역에서 사용할 수 있지만 계층 제한은 없습니다. 계층 및 지역 조건을 충족 하는 기존 서비스가 있으면 등록만 필요 합니다.

3 월 2 일부 터 3 월 1 일까 지 미리 보기 시작 사이에는 무료로 제공 됩니다. 4 월 1 일부 터이 기능을 실행 하는 계산 비용은 청구 가능 이벤트가 됩니다. 예상 비용은 25만 쿼리의 경우 약 USD $500/월입니다. [Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/) 에서 설명 하 고 [비용을 예측 하 고 관리](search-sku-manage-costs.md)하는 자세한 비용 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

새 쿼리 유형을 사용 하면 의미 체계 검색의 관련성 순위 및 응답 구조를 사용할 수 있습니다. [의미 체계 쿼리를 만들어](semantic-how-to-query-request.md) 시작 합니다. 또는 관련 정보는 다음 문서 중 하나를 참조 하세요.

+ [쿼리 용어에 맞춤법 검사 추가](speller-how-to-add.md)
+ [의미 체계 순위 및 응답](semantic-how-to-query-response.md)