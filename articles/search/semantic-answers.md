---
title: 의미 대답 반환
titleSuffix: Azure Cognitive Search
description: 의미 체계 대답의 구성과 결과 집합에서 답변을 얻는 방법에 대해 설명 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722432"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Azure Cognitive Search에서 의미 대답 반환

> [!IMPORTANT]
> 의미 체계 검색은 preview REST API 통해서만 사용할 수 있는 공개 미리 보기 상태입니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 있는 그대로 제공 되며 일반 공급 시 동일한 구현을 보장 하지 않습니다. 이러한 기능은 청구 가능 합니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조 하세요.

[의미 체계 쿼리](semantic-how-to-query-request.md)를 작성 하는 경우 필요에 따라 쿼리를 직접 "답변" 하는 상위 문서에서 콘텐츠를 추출할 수 있습니다. 응답에 하나 이상의 답을 포함 하 여 앱의 사용자 환경을 개선 하기 위해 검색 페이지에 렌더링할 수 있습니다.

이 문서에서는 의미 체계 대답을 요청 하 고, 응답의 압축을 풀고, 높은 품질의 답변을 생성 하기 위해 가장 취약 되는 콘텐츠 특성에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[의미 체계 쿼리에](semantic-how-to-query-request.md) 적용 되는 모든 필수 구성 요소는 서비스 계층 및 지역을 포함 하 여 답변에도 적용 됩니다.

+ 쿼리 논리에는 의미 체계 쿼리 매개 변수와 "대답" 매개 변수를 포함 해야 합니다. 필수 매개 변수는이 문서에 설명 되어 있습니다.

+ 사용자가 입력 한 쿼리 문자열은 질문의 특징 (무엇, 위치, 시기, 방법)을 포함 하는 언어로 작성 되어야 합니다.

+ 문서 검색은 응답의 특징을 가진 텍스트를 포함 해야 하며 해당 텍스트는 "searchFields"에 나열 된 필드 중 하나에 있어야 합니다. 예를 들어 "해시 테이블 정의" 라는 쿼리가 제공 된 경우 "A 해시 테이블은 ..."를 포함 하는 부분을 포함 하는 searchFields가 없는 경우에는 응답이 반환 될 가능성이 없습니다.

## <a name="what-is-a-semantic-answer"></a>의미 대답은 무엇 인가요?

의미 체계 대답은 [의미 체계 쿼리 응답](semantic-how-to-query-request.md)의 하위 구조입니다. 검색 문서에서 하나 이상의 축 자 통로로 구성 되며 질문 처럼 보이는 쿼리에 대 한 답변으로 작성 됩니다. 답변이 반환 되려면 검색어의 언어 특성이 포함 된 검색 문서에 구 또는 문장이 있어야 하 고 쿼리 자체는 질문으로 나타나야 합니다.

Cognitive Search는 컴퓨터 읽기 이해력 모델을 사용 하 여 최상의 답을 선택 합니다. 이 모델은 사용 가능한 콘텐츠에서 잠재적인 답변 집합을 생성 하 고, 충분 한 신뢰 수준에 도달 하면 답변을 제안 합니다.

답변은 검색 페이지에서 렌더링 하도록 선택할 수 있는 쿼리 응답 페이로드의 독립적인 최상위 개체로 반환 됩니다. 구조적으로는 텍스트, 문서 키 및 신뢰도 점수를 구성 하는 응답 내의 배열 요소입니다.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>쿼리에서 의미 체계 대답을 요청 하는 방법

의미 대답을 반환 하려면 쿼리에 "queryType", "queryLanguage", "searchFields" 및 "answer" 매개 변수를 포함 해야 합니다. "Answer" 매개 변수를 지정 하면 대답을 얻을 수 있지만 대답 처리를 호출 해야 하는 경우 요청에이 매개 변수가 포함 되어야 합니다.

"SearchFields" 매개 변수는 콘텐츠 및 주문 측면에서 높은 품질의 대답을 반환 하는 데 중요 합니다 (아래 참조). 

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

+ 쿼리 문자열은 null이 아니어야 하며 질문으로 작성 해야 합니다. 이 미리 보기에서 "queryType" 및 "queryLanguage"는 예제와 같이 정확 하 게 설정 해야 합니다.

+ "SearchFields" 매개 변수는 추출 모델에 토큰을 제공 하는 문자열 필드를 결정 합니다. 캡션을 생성 하는 동일한 필드도 답을 생성 합니다. 캡션과 답변 모두에 대해 작동 하도록이 필드를 설정 하는 방법에 대 한 정확한 지침은 [searchFields 설정](semantic-how-to-query-request.md#searchfields)을 참조 하세요. 

+ "대답"의 경우 매개 변수 생성은 반환 되는 `"answers": "extractive"` 기본 답변 수가 1입니다. 위의 예제와 같이 개수를 최대 5 개까지 더하여 답변 수를 늘릴 수 있습니다.  둘 이상의 대답이 필요한 지 여부는 앱의 사용자 환경 및 결과를 렌더링 하는 방법에 따라 달라 집니다.

## <a name="deconstruct-an-answer-from-the-response"></a>응답에서 답변을 분해할 합니다.

답변은 @search.answers 처음 응답에서 표시 되는 배열에 제공 됩니다. 답변이 확정 되지 않은 경우 응답은로 표시 됩니다 `"@search.answers": []` . 답변이 포함 된 검색 결과 페이지를 디자인할 때 답변이 없는 경우를 처리 해야 합니다.

내에서 @search.answers "키"는 일치 항목의 문서 키 또는 ID입니다. 문서 키를 지정 하면 [조회 문서](/rest/api/searchservice/lookup-document) API를 사용 하 여 검색 페이지 또는 세부 정보 페이지에 포함할 검색 문서의 일부 또는 모든 부분을 검색할 수 있습니다.

"Text" 및 "하이라이트"는 모두 일반 텍스트와 강조 표시에서 동일한 콘텐츠를 제공 합니다. 기본적으로 강조 표시는 `<em>` 기존 highlightPreTag 및 highlightPostTag 매개 변수를 사용 하 여 재정의할 수 있는으로 스타일이 지정 됩니다. 다른 곳에서 언급 했 듯이, 답변의 내용은 검색 문서에서의 축 자 내용입니다. 추출 모델은 응답의 특징을 검색 하 여 적절 한 콘텐츠를 찾을 수 있지만 응답에서 새로운 언어를 구성 하지는 않습니다.

"점수"는 대답의 강도를 반영 하는 신뢰도 점수입니다. 응답에 여러 답변이 있는 경우이 점수를 사용 하 여 주문을 결정 합니다. 최상위 대답은 한 문서에서 시작 하 고 다른 문서에서 위쪽 캡션을 생성 하는 여러 검색 문서에서 파생 될 수 있지만 일반적으로 각 배열 내의 위쪽 위치에 동일한 문서가 표시 됩니다.

답변에는 항상 점수, 캡션 및 기본적으로 검색 가능한 모든 필드가 포함 된 "값" 배열이 나옵니다. Select 매개 변수를 지정한 경우 "value" 배열은 지정한 필드로 제한 됩니다. 응답의 항목에 대 한 자세한 내용은 [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)를 참조 하세요.

"방법: 클라우드 형식" 쿼리가 제공 되 면 응답에서 다음 응답이 반환 됩니다.

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

## <a name="tips-for-producing-high-quality-answers"></a>고품질 답변을 생성 하기 위한 팁

최상의 결과를 위해 다음 특징을 가진 문서 모음에 의미 체계 대답을 반환 합니다.

+ "searchFields"는 대답을 찾을 수 있는 충분 한 텍스트를 제공 하는 필드를 제공 해야 합니다. 문서의 축 자 텍스트만 답변으로 나타날 수 있습니다.

+ 쿼리 문자열은 null (search = `*` )이 아니어야 하며, 문자열에는 키워드 검색 (임의 용어의 순차적인 목록)과 달리 질문의 특징이 있어야 합니다. 쿼리 문자열이 응답 하지 않는 것으로 나타나는 경우 요청에서 쿼리 매개 변수로 "응답"을 지정 하더라도 응답 처리를 건너뜁니다.

+ 의미 추출 및 요약에는 적시에 분석할 수 있는 문서 당 토큰 수에 대 한 제한이 있습니다. 실제로 수백 개의 페이지에 실행 되는 규모가 많은 문서를 사용 하는 경우 콘텐츠를 더 작은 문서로 먼저 분할 해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [의미 체계 검색 개요](semantic-search-overview.md)
+ [의미 체계 순위 알고리즘](semantic-ranking.md)
+ [유사성 순위 알고리즘](index-ranking-similarity.md)
+ [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)