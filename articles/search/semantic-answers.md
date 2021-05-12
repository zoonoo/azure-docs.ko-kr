---
title: 의미 체계 대답 반환
titleSuffix: Azure Cognitive Search
description: 의미 체계 대답의 구성 및 결과 집합에서 답변을 얻는 방법에 대해 설명합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722432"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Azure Cognitive Search에서 의미 체계 대답 반환

> [!IMPORTANT]
> 의미 체계 검색은 퍼블릭 미리 보기로 제공되며 미리 보기 REST API를 통해서만 이용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 나와 있는 그대로 제공되며, 일반 공급 시 동일하게 구현된다는 보장은 없습니다. 해당 기능에는 비용이 청구될 수 있습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

[의미 체계 쿼리](semantic-how-to-query-request.md)를 작성할 때는 필요에 따라 쿼리에 직접 “답변”하는 상위 일치 문서에서 콘텐츠를 추출할 수 있습니다. 응답에는 하나 이상의 답변이 포함될 수 있으며, 이후 이를 검색 페이지에 렌더링하여 앱의 사용자 환경을 개선할 수 있습니다.

이 문서에서는 의미 체계 대답을 요청하고 응답의 압축을 푸는 방법과 높은 품질의 답변을 생성하는 데 가장 도움이 되는 콘텐츠 특성이 무엇인지에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

[의미 체계 쿼리](semantic-how-to-query-request.md)에 적용되는 모든 필수 구성 요소는 답변에도 적용되며, 여기에는 서비스 계층 및 지역도 포함됩니다.

+ 쿼리 논리는 의미 체계 쿼리 매개 변수와 “답변” 매개 변수를 포함해야 합니다. 이 문서에서는 필수 매개 변수를 설명합니다.

+ 사용자가 입력한 쿼리 문자열은 질문의 특성(무엇을, 어디서, 언제, 어떻게)을 가지는 언어로 작성되어야 합니다.

+ 검색 문서는 답변의 특성을 가지는 텍스트를 포함해야 하며, 해당 텍스트는 “searchFields”에 나열된 필드 중 하나에 존재해야 합니다. 예를 들어 “해시 테이블이란 무엇인가요”라는 쿼리에서 “해시 테이블은...”을 포함하는 구절을 가진 searchFields가 없는 경우 답변은 반환되지 않을 가능성이 큽니다.

## <a name="what-is-a-semantic-answer"></a>의미 체계 대답이란 무엇인가요?

의미 체계 대답은 [의미 체계 쿼리 응답](semantic-how-to-query-request.md)의 하위 구조입니다. 검색 문서의 축자 구절 하나 이상으로 구성되며 질문의 형태를 가진 쿼리에 대한 답변으로 작성됩니다. 답변이 반환되려면 구절 또는 문장이 답변의 언어 특성을 가지는 검색 문서에 존재해야 하며, 쿼리 자체는 질문의 형태를 하고 있어야 합니다.

Cognitive Search는 머신 리딩 이해력 모델을 사용하여 최선의 답을 선택합니다. 이 모델은 사용 가능한 콘텐츠를 통해 잠재적인 답변의 집합을 생성하고, 충분한 신뢰 수준에 도달하는 경우 답변을 제안합니다.

답변은 검색 페이지에서 검색 결과와 동시에 렌더링할 수 있는 쿼리 응답 페이로드의 독립적인 최고 수준 개체로 반환됩니다. 구조적으로 이는 텍스트, 문서 키, 신뢰도 점수로 구성된 응답 내의 배열 요소입니다.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>쿼리에서 의미 체계 대답을 요청하는 방법

의미 체계 대답을 반환하려면 쿼리에 “queryType”, “queryLanguage”, “searchFields”, “answers” 매개 변수를 포함해야 합니다. “answers” 매개 변수를 지정한다고 해서 답변이 반환된다는 보장은 없지만, 답변 처리를 호출하려면 요청에 해당 매개 변수가 꼭 포함되어야 합니다.

“searchFields” 매개 변수는 콘텐츠 및 순서 측면에서 높은 품질의 대답을 반환하는 데 있어 중요합니다(아래 참조). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ 쿼리 문자열은 Null이 아니어야 하며 질문 형태로 작성되어야 합니다. 이 미리 보기에서 “queryType” 및 “queryLanguage”는 예제에 나와 있는 대로 정확히 설정해야 합니다.

+ “searchFields” 매개 변수는 추출 모델에 토큰을 제공하는 문자열 필드를 결정합니다. 캡션을 생성하는 필드가 답변 또한 생성합니다. 이 필드가 캡션 및 답변 모두에 대해 작동하도록 설정하는 방법에 대한 정확한 참고 자료는 [searchFields 설정](semantic-how-to-query-request.md#searchfields)을 참조하세요. 

+ “답변”의 경우 매개 변수 생성은 `"answers": "extractive"`이며 반환되는 답변의 수는 기본적으로 1개입니다. 위의 예제에 나와 있는 것과 같이 답변을 추가하여 답변 수를 최대 5개까지 늘릴 수 있습니다.  답변이 2개 이상 필요한지는 앱의 사용자 환경 및 결과 렌더링 방법에 따라 달라집니다.

## <a name="deconstruct-an-answer-from-the-response"></a>응답에서 답변 분해

답변은 응답에서 첫 번째로 표시되는 @search.answers 배열에 제공됩니다. 답변이 확정되지 않은 경우 응답은 `"@search.answers": []`로 표시됩니다. 답변을 포함하는 검색 결과 페이지를 디자인할 때는 답변을 찾지 못한 경우를 처리해야 합니다.

@search.answers 내에서 “key”는 일치 항목의 문서 키 또는 ID입니다. 지정된 문서 키를 통해 [조회 문서](/rest/api/searchservice/lookup-document) API를 사용하여 검색 문서의 일부 또는 모든 부분을 검색하고 이를 검색 페이지 또는 세부 정보 페이지에 포함할 수 있습니다.

“text” 및 “highlights”는 동일한 콘텐츠를 일반 텍스트와 강조 표시 텍스트 모두로 제공합니다. 기본적으로 강조 표시는 `<em>`로 스타일이 지정되며, 이는 highlightPreTag 및 highlightPostTag 매개 변수를 사용하여 재정의할 수 있습니다. 이미 언급했듯이 답변의 핵심은 검색 문서에서의 축자 콘텐츠입니다. 추출 모델은 답변의 특징을 검색하여 적절한 콘텐츠를 찾지만, 응답에서 새로운 언어를 구성하지는 않습니다.

“score”는 답변의 강도를 반영하는 신뢰도 점수입니다. 응답에 답변이 여러 개인 경우 이 점수를 사용하여 순서를 결정합니다. 상위 답변 및 상위 캡션은 서로 다른 검색 문서에서 파생되었을 수 있습니다. 즉, 상위 답변이 한 문서에서 생성되고 상위 캡션은 다른 문서에서 생성되었을 수 있지만, 일반적으로는 각 배열 내 상위 위치에 동일한 문서가 표시됩니다.

답변 다음에는 항상 점수, 캡션, 기본적으로 검색 가능한 모든 필드가 포함된 “value” 배열이 표시됩니다. select 매개 변수를 지정한 경우 “value” 배열은 지정한 필드로 제한됩니다. 응답의 항목에 대한 자세한 내용은 [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)를 참조하세요.

“구름은 어떻게 만들어지나요” 쿼리가 제공되면 응답에서는 다음 답변이 반환됩니다.

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>고품질 답변을 생성하기 위한 팁

최상의 결과를 위해 다음 특성을 가진 문서 모음에 있는 의미 체계 대답을 반환합니다.

+ “searchFields”에서 답변을 찾을 가능성이 큰 충분한 양의 텍스트를 제공하는 필드를 제공해야 합니다. 문서의 축자 텍스트만 답변으로 표시될 수 있습니다.

+ 쿼리 문자열은 Null(search=`*`)이 아니어야 하며, 문자열은 키워드 검색(임의 단어 또는 구문의 순차적인 목록)과는 달리 질문의 특성을 지니고 있어야 합니다. 쿼리 문자열이 답변처럼 보이지 않는 경우 요청이 쿼리 매개 변수로 “answers”를 지정하는 경우라도 답변 처리를 건너뛰게 됩니다.

+ 의미 체계 추출 및 요약에는 시간 내에 분석할 수 있는 문서당 토큰 수에 대한 제한이 있습니다. 실제로 수백 개의 페이지로 구성된 큰 문서가 있는 경우 먼저 콘텐츠를 분할하여 더 작은 문서로 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

+ [의미 체계 검색 개요](semantic-search-overview.md)
+ [의미 체계 순위 지정 알고리즘](semantic-ranking.md)
+ [유사성 순위 알고리즘](index-ranking-similarity.md)
+ [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)