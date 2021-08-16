---
title: 의미 체계 검색
titleSuffix: Azure Cognitive Search
description: Cognitive Search에서 Bing의 딥 러닝 의미 체계 검색 모델을 사용하여 검색 결과를 보다 직관적으로 만드는 방법에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: c55ca73d63fb380dece0350de482dcc9087bd315
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744812"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Azure Cognitive Search의 의미 체계 검색

> [!IMPORTANT]
> 의미 체계 검색은 공개 미리 보기로 제공되며 미리 보기 REST API 및 포털을 통해서만 이용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 나와 있는 그대로 제공되며, 일반 공급 시 동일하게 구현된다는 보장은 없습니다. 이러한 기능에는 비용이 청구될 수 있습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

의미 체계 검색은 검색 결과에 대한 의미 체계 관련성 및 언어 이해를 추가하는 쿼리 관련 기능의 컬렉션입니다. 이 문서는 의미 체계 검색을 위한 개략적인 소개이며 각 기능에 대한 설명과 이러한 기능이 집합적으로 작동하는 방식을 설명합니다. 포함된 비디오는 기술에 대해 설명하고, 최종 섹션에서는 가용성 및 가격 책정에 대해 설명합니다.

의미 체계 검색은 프리미엄 기능입니다. 이 문서를 백그라운드에서 수행하는 것이 좋지만 시작하려면 다음 단계를 수행합니다.

1. [지역 및 서비스 계층 요구 사항을 확인합니다](#availability-and-pricing).
1. [미리 보기 프로그램에 가입합니다](https://aka.ms/SemanticSearchPreviewSignup). 요청을 처리하는 데 영업일 기준 최대 2일까지 걸릴 수 있습니다.
1. 승인되면 [의미 체계 캡션과 강조 표시](semantic-how-to-query-request.md)를 반환하는 쿼리를 만들거나 수정합니다.
1. 몇 가지 추가 쿼리 속성을 추가하여 [의미 체계 대답](semantic-answers.md)도 반환합니다.
1. 필요에 따라 정밀도 및 재현율을 최대화하는 [맞춤법 검사](speller-how-to-add.md) 속성을 포함합니다.

## <a name="what-is-semantic-search"></a>의미 체계 검색이란?

의미 체계 검색은 쿼리 관련 AI의 선택적 계층입니다. 검색 서비스에서 사용하도록 설정된 경우 기존 쿼리 실행 파이프라인을 두 가지 방식으로 확장합니다. 먼저 선택적 의미 체계 순위 모델을 추가합니다. 그런 다음, 응답에서 캡션과 답변을 반환합니다.

*의미 체계 순위 지정* 은 용어 간의 컨텍스트 및 관련성을 검색하여 쿼리에 더 적합한 일치 항목을 승격합니다. 언어 이해는 콘텐츠 내에서 요약 또는 *캡션* 및 *답변* 을 찾고 응답에 포함합니다. 이를 검색 결과 페이지에서 렌더링하여 보다 생산적인 검색 환경을 제공할 수 있습니다.

요약 및 순위 지정에는 미리 학습된 최신 모델이 사용됩니다. 사용자가 검색에서 필요로 하는 빠른 성능을 유지하기 위해 의미 체계 요약 및 순위 지정은 [기본 유사성 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)에 따라 점수가 매겨진 상위 50개의 결과에만 적용됩니다. 이러한 결과를 문서 모음으로 사용하면 의미 체계 순위 지정이 일치의 의미 체계 강도에 따라 결과의 점수를 다시 지정합니다.

기본 기술은 Bing 및 Microsoft Research에서 추가 기능으로 Cognitive Search 인프라에 통합되어 있습니다. 의미 체계 검색을 지원하는 연구 및 AI 투자에 대한 자세한 내용은 [Bing의 AI가 Azure Cognitive Search를 지원하는 방법(Microsoft Research 블로그)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)을 참조하세요.

다음 동영상에서는 기능의 개요를 제공합니다.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="feature-description-and-workflow"></a>기능 설명 및 워크플로

의미 체계 검색은 다음과 같은 기능을 추가하여 정밀도 및 재현율을 향상시킵니다.

| 기능 | Description |
|---------|-------------|
| [맞춤법 검사](speller-how-to-add.md) | 쿼리 용어가 검색 엔진에 도달하기 전에 오타를 수정합니다. |
| [의미 체계 순위 지정](semantic-ranking.md) | 컨텍스트나 의미 체계 의미를 사용하여 새 관련성 점수를 계산합니다. |
| [의미 체계 캡션 및 강조 표시](semantic-how-to-query-request.md) | 콘텐츠를 가장 잘 요약한 문서의 문장과 구에 간편한 검색을 위한 주요 구절을 강조합니다. 결과 페이지에서 개별 콘텐츠 필드가 너무 조밀한 경우 결과를 요약하는 캡션이 유용합니다. 강조 표시된 텍스트는 사용자가 일치 항목으로 간주되는 이유를 빠르게 확인할 수 있도록 가장 관련성이 높은 용어와 구를 승격시킵니다. |
| [의미 체계 답변](semantic-answers.md) | 의미 체계 쿼리에서 반환되는 선택적 및 추가 하위 구조입니다. 질문처럼 보이는 쿼리에 직접 답변을 제공합니다. |

### <a name="order-of-operations"></a>작업 순서

의미 체계 검색의 구성 요소는 기존 쿼리 실행 파이프라인을 양방향으로 확장합니다. 맞춤법 수정 기능을 사용하는 경우 [맞춤법 검사기](speller-how-to-add.md)는 용어가 검색 엔진에 도달하기 전에 쿼리 시작에서 오타를 수정합니다.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="쿼리 실행의 의미 체계 구성 요소" border="true":::

쿼리 실행은 반전된 인덱스에 대한 용어 구문 분석, 분석 및 검색을 통해 일반적인 방식으로 진행됩니다. 엔진은 토큰 일치를 사용하여 문서를 검색하고 [기본 유사성 점수 매기기 알고리즘](index-similarity-and-scoring.md#similarity-ranking-algorithms)을 사용하여 결과를 채점합니다. 점수는 인덱스에서 쿼리 용어와 일치하는 용어 간의 언어적 유사성을 기준으로 계산됩니다. 정의한 경우 점수 매기기 프로필도 이 단계에서 적용됩니다. 그런 다음, 결과가 의미 체계 검색 하위 시스템에 전달됩니다.

준비 단계에서 초기 결과 집합에서 반환된 문서 모음은 각 문서를 요약하는 구절을 찾기 위해 문장과 단락 수준에서 분석됩니다. 키워드 검색과 달리 이 단계에서는 머신 읽기와 이해력을 사용하여 콘텐츠를 평가합니다. 이 콘텐츠 처리 단계를 통해 의미 체계 쿼리는 [캡션](semantic-how-to-query-request.md) 및 [답변](semantic-answers.md)을 반환합니다. 이를 공식화하기 위해 의미 체계 검색에서는 언어 표현을 사용하여 결과를 가장 잘 요약하는 주요 구절을 추출하고 강조 표시합니다. 검색 쿼리가 질문이고 답변이 요청된 경우 응답에는 검색 쿼리로 표현되는 질문에 가장 잘 대답하는 텍스트 구절이 포함됩니다. 

캡션과 답변 모두에 대해 기존 텍스트는 공식화에서 사용됩니다. 의미 체계 모델은 사용 가능한 콘텐츠에서 새로운 문장이나 구를 작성하지 않으며 새로운 결론에 도달할 수 있는 논리를 적용하지 않습니다. 즉, 시스템은 아직 존재하지 않는 콘텐츠는 반환하지 않습니다.

그런 다음, 쿼리 용어의 [개념적 유사성](semantic-ranking.md)을 기준으로 결과는 다시 채점됩니다.

쿼리에서 의미 체계 기능을 사용하려면 [검색 요청](semantic-how-to-query-request.md)을 약간만 수정해야 하지만 추가 구성이나 인덱스 다시 작성은 필요하지 않습니다.

## <a name="semantic-capabilities-and-limitations"></a>의미 체계 기능 및 제한 사항

의미 체계 검색은 최신 기술이므로 수행할 수 있는 작업과 수행할 수 없는 작업에 대한 기대치를 설정하는 것이 중요합니다.

이를 통해 두 가지 방법으로 검색 결과의 품질을 향상시킬 수 있습니다. 첫째, 원래 쿼리의 의도에 의미 체계적으로 가까운 문서를 승격하는 것은 상당한 장점입니다. 둘째, 페이지에 캡션 및 잠재적으로 답변을 제공할 때 결과를 즉시 사용할 수 있습니다. 엔진은 항상 기존 콘텐츠를 사용합니다. 의미 체계 검색에 사용되는 언어 모델은 응답처럼 보이지만 새 문자열을 쿼리에 대한 응답으로 구성하거나 일치하는 문서에 대한 캡션으로 구성하려고 시도하지 않는 온전한 문자열을 추출하도록 설계되었습니다.

의미 체계 검색은 논리 엔진이 아니므로 문서 또는 문서 모음 내의 다양한 콘텐츠에서 정보를 유추하지 않습니다. 예를 들어 지리적 입력이 없는 "사막의 리조트 호텔"에 대한 쿼리가 있는 경우 엔진은 두 주에 모두 사막이 있더라도 애리조나나 네바다에 위치한 호텔의 일치 항목을 생성하지 않습니다. 마찬가지로 쿼리에 "지난 5년 동안" 절이 포함된 경우 엔진은 반환할 현재 날짜를 기준으로 시간 간격을 계산하지 않습니다.

Cognitive Search에서 위의 시나리오에 유용할 수 있는 메커니즘에는 외견상 다른 용어 간의 연결을 작성할 수 있는 [동의어 맵](search-synonyms.md) 또는 OData 식으로 지정된 [날짜 필터](search-query-odata-filter.md)가 포함됩니다.

## <a name="availability-and-pricing"></a>가용성 및 가격 책정

의미 체계 검색은 [등록 가입](https://aka.ms/SemanticSearchPreviewSignup)을 통해 사용할 수 있습니다. 3월 2일의 미리 보기 시작부터 7월 초까지 의미 체계 기능이 무료로 제공됩니다.

| 기능 | 계층 | 지역 | 등록 | 예상 가격 책정 |
|---------|------|--------|---------------------|-------------------|
| 의미 체계 검색(캡션, 강조 표시, 답변) | 표준 계층(S1, S2, S3) | 미국 중북부, 미국 서부, 미국 서부 2, 미국 동부 2, 북유럽, 서유럽 | 필수 | 7월 초부터 예상 가격 책정은 처음 25만개의 쿼리의 경우 USD 500달러/월, 각 추가 1000개의 쿼리에는 2달러입니다.  |
| 맞춤법 검사 | 임의 | 미국 중북부, 미국 서부, 미국 서부 2, 미국 동부 2, 북유럽, 서유럽 | 필수 | 없음(무료) |

의미 체계 기능과 맞춤법 검사에 대한 [등록 가입](https://aka.ms/SemanticSearchPreviewSignup)이 하나 있습니다. 

의미 체계 검색 없이 맞춤법 검사를 무료로 사용할 수 있습니다. 의미 체계 검색에 대한 요금은 쿼리 요청에 `queryType=semantic`이 포함되고 검색 문자열이 비어 있지 않은 경우에 부과됩니다(예: `search=pet friendly hotels in new york`). 빈 검색(쿼리 위치 `search=*`)에는 요금이 부과되지 않습니다.

최종 가격 책정 정보는 [Cognitive Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/) 및 [비용 예상 및 관리](search-sku-manage-costs.md)에서 설명합니다.

## <a name="next-steps"></a>다음 단계

이전 섹션에서 설명한 계층 및 지역 요구 사항을 충족하는 검색 서비스에 대한 미리 보기를 [가입](https://aka.ms/SemanticSearchPreviewSignup)합니다.

요청을 처리하는 데 영업일 기준 최대 2일까지 걸릴 수 있습니다. 서비스가 준비되면 [의미 체계 쿼리를 만들어](semantic-how-to-query-request.md) 실행 중인 의미 체계 순위를 확인합니다.
