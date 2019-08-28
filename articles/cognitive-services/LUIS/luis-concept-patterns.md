---
title: 패턴 지원 예측-LUIS
titleSuffix: Azure Cognitive Services
description: 패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: bad3bdc2b4508c082ca50647d5de5e7265c763a1
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639186"
---
# <a name="patterns-improve-prediction-accuracy"></a>패턴을 통해 예측 정확도 개선
패턴은 여러 발언이 매우 유사할 경우 정확도를 향상시키도록 디자인됩니다.  패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다. 

## <a name="patterns-solve-low-intent-confidence"></a>패턴을 통해 낮은 의도 신뢰도 해결
직원과 관련해서 조직도에 보고하는 Human Resources 앱을 가정해 봅니다. 직원의 이름 및 관계가 제공될 경우 LUIS는 관련 직원을 반환합니다. 관리자 이름은 Alice이고 Michael, Rebecca 및 Carl이 부하 직원으로 팀에 소속되어 있는 Tom이라는 직원이 있다고 가정합니다.

![조직도 이미지](./media/luis-concept-patterns/org-chart.png)

|발언|예측된 의도|의도 점수|
|--|--|--|
|Tom의 부하 직원은 누구인가요?|GetOrgChart|.30|
|어떤 직원이 Tom의 부하 직원인가요?|GetOrgChart|.30|

앱이 문장 길이가 다르고, 단어 순서가 다르고, 사용되는 단어(“부하 직원” “관리자”, “직속 부하”)도 다른 10~20개 발언을 포함하는 경우 LUIS는 낮은 신뢰도 점수를 반환할 수 있습니다. LUIS에서 단어 순서를 이해 하는 데 도움이 되는 패턴을 만듭니다. 

패턴은 다음과 같은 상황을 해결합니다. 

* 의도 점수가 낮음
* 올바른 의도가 가장 중요 한 점수가 아니라 가장 높은 점수에 가깝습니다. 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>패턴은 의도를 보장하지 않음
패턴은 혼합된 예측 기술을 사용합니다. 템플릿 발언에 대한 의도를 패턴으로 설정해도 의도 예측이 제대로 이루어질 것이라고 보장할 수 없지만 강력한 신호가 될 수 있습니다. 

<a name="patterns-do-not-improve-entity-detection"/></a>

## <a name="patterns-do-not-improve-machine-learned-entity-detection"></a>패턴은 컴퓨터에서 학습 한 엔터티 검색을 개선 하지 않습니다.

패턴은 주로 의도 및 역할의 예측을 지 원하는 데 도움이 됩니다. 패턴입니다. 자유 형식 엔터티를 추출 하는 데 엔터티를 사용 합니다. 패턴에서 엔터티를 사용 하는 동안 패턴은 컴퓨터에서 학습 한 엔터티를 검색 하는 데 도움이 되지 않습니다.  

여러 발언을 단일 패턴으로 축소하는 경우에는 향상된 엔터티 예측을 기대하지 못합니다. 간단한 엔터티를 발생시키려면 발언을 추가하거나 목록 엔터티를 사용합니다. 그렇지 않으면 패턴이 발생하지 않습니다.

## <a name="patterns-use-entity-roles"></a>패턴에 엔터티 역할 사용
패턴의 둘 이상의 엔터티가 문맥상 관련이 있는 경우 패턴은 엔터티 [역할](luis-concept-roles.md)을 사용하여 엔터티에 대한 컨텍스트 정보를 추출합니다.  

## <a name="prediction-scores-with-and-without-patterns"></a>패턴이 있거나 없는 예측 점수
충분한 예제 발언이 제공될 경우, LUIS는 패턴 없이 예측 신뢰도를 높일 수 있습니다. 패턴은 많은 발언을 제공할 필요 없이, 신뢰도 점수를 높입니다.  

## <a name="pattern-matching"></a>패턴 일치
먼저 패턴 내 엔터티를 검색된 후, 패턴의 나머지 단어 및 단어 순서가 유효한지 검사됩니다. 패턴이 일치하려면 패턴에 엔터티가 필요합니다. 패턴은 문자 수준이 아닌 토큰 수준에서 적용됩니다. 

## <a name="pattern-syntax"></a>패턴 구문
패턴 구문은 발언의 템플릿입니다. 템플릿에는 무시하려는 단어 및 문장 부호 뿐만 아니라 일치시키려는 단어 및 엔터티도 포함되어야 합니다. 정규식은 **아닙니다**. 

패턴의 엔터티는 중괄호 `{}`로 묶입니다. 패턴은 엔터티 및 역할이 있는 엔터티를 포함할 수 있습니다. [패턴. any](luis-concept-entity-types.md#patternany-entity) 는 패턴에만 사용 되는 엔터티입니다. 

패턴 구문에서는 다음 구문을 지원 합니다.

|함수|구문|중첩 수준|예제|
|--|--|--|--|
|엔터티(entity)| {}-중괄호|2|{Entity-name} 형식은 어디에 있나요?|
|선택적|[]-대괄호<BR><BR>선택 항목 및 그룹화 조합의 중첩 수준에는 3의 제한이 있습니다. |2|물음표는 선택적인 [?]입니다.|
|그룹화|()-괄호|2|is (a \| b)|
|로 구분하거나 여러| \|-세로 막대 (파이프)<br><br>한 그룹의 세로 막대 (또는)에는 2 제한이 있습니다. |-|형식 ({form-name-short} &#x7c; {form-name-long} &#x7c; {form-number})| 
|utterance의 시작 및/또는 끝|^-캐럿|-|^ utterance 시작<br>utterance가 완료 되었습니다.<br>^ ' number} 엔터티 ^의 전체 utterance의 엄격한 리터럴 일치 항목 ^|

## <a name="nesting-syntax-in-patterns"></a>패턴의 중첩 구문

대괄호를 사용 하는 **선택적** 구문은 두 수준 중첩할 수 있습니다. 예를 들어 `[[this]is] a new form`을 참조하십시오. 이 예에서는 다음 길이 발언을 허용 합니다. 

|중첩 된 선택적 utterance 예제|설명|
|--|--|
|새 폼입니다.|패턴의 모든 단어를 찾습니다.|
|새 양식|패턴의 외부 선택적 단어 및 선택적 단어가 아닌 단어를 찾습니다.|
|새 양식|필수 단어만 찾습니다.|

괄호를 사용 하는 **그룹화** 구문은 두 수준 중첩할 수 있습니다. 예를 들어 `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`을 참조하십시오. 이 기능을 사용 하면 세 가지 엔터티를 일치 시킬 수 있습니다. 

Entity1이 원본 (시애틀) 및 대상 (카이로)과 같은 역할이 있는 위치인 경우 엔터티 2는 목록 엔터티 (RedWest-C)에서 알려진 빌딩 이름이 고, 다음 길이 발언는이 패턴에 매핑됩니다.

|Nested grouping utterance 예제|설명|
|--|--|
|RedWest-C|외부 그룹화 엔터티와 일치|
|Seattle|내부 그룹화 엔터티 중 하 나와 일치|
|Cairo|내부 그룹화 엔터티 중 하 나와 일치|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>선택적인 구문을 사용 하 여 그룹에 대 한 중첩 제한

**선택적** 구문의 **그룹화** 조합에는 중첩 수준 3 개로 제한 됩니다.

|Allowed|예제|
|--|--|
|예|([(test1 &#x7c; test2)] &#x7c; test3)|
|아니요|([([test1] &#x7c; test2)] &#x7c; test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>또는 구문을 사용 하 여 그룹에 대 한 중첩 제한

**Or** of 구문을 사용한 **grouping** 의 조합에는 세로 막대 2 개로 제한 됩니다.

|Allowed|예제|
|--|--|
|예|(test1 &#x7c; test2 &#x7c; (test3 &#x7c; test4))|
|아니요|(test1 &#x7c; test2 &#x7c; test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>패턴 템플릿에 엔터티를 추가하는 구문
패턴 템플릿에 엔터티를 추가하려면 엔터티 이름을 중괄호로 묶습니다(예: `Who does {Employee} manage?`). 

|엔터티 사용 패턴|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>패턴 템플릿에 엔터티 및 역할을 추가하는 구문
엔터티 역할은 엔터티 이름에 콜론을 입력하고 역할 이름을 입력하여 `{entity:role}`로 표시합니다. 패턴 템플릿에 역할이 있는 엔터티를 추가하려면 엔터티 이름과 역할 이름을 중괄호로 묶습니다(예: `Book a ticket from {Location:Origin} to {Location:Destination}`). 

|엔터티 역할을 사용한 패턴|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>패턴 템플릿에 pattern.any를 추가하는 구문
Pattern.any 엔터티를 사용하여 다양한 길이의 엔터티를 패턴에 추가할 수 있습니다. 패턴 템플릿을 따르기만 하면 pattern.any 길이에는 제한이 없습니다. 

**Pattern.any** 엔터티를 패턴 템플릿에 추가하려면 Pattern.any 엔터티를 중괄호로 묶습니다(예: `How much does {Booktitle} cost and what format is it available in?`).  

|Pattern.any 엔터티 사용 패턴|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|패턴의 책 제목|
|--|
|**steal this 책** 가격은 얼마이며 어떤 형식으로 사용할 수 있나요?|
|**ask** 가격은 얼마이며 어떤 형식으로 사용할 수 있나요?|
|**The Curious Incident of the Dog in the Night-Time** 가격은 얼마이며 어떤 형식으로 사용할 수 있나요?| 

LUIS는 패턴에 따라 책 제목이 끝나는 위치를 알 수 있기 때문에 책 제목의 단어는 LUIS 혼동 되지 않습니다.

## <a name="explicit-lists"></a>명시적 목록

작성 API를 통해 [명시적 목록을](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) 만들어 다음의 경우 예외를 허용 합니다.

* 패턴은 패턴을 포함 [합니다.](luis-concept-entity-types.md#patternany-entity)
* 그리고 해당 패턴 구문을 사용 하면 utterance을 기반으로 잘못 된 엔터티 추출이 발생할 수 있습니다. 

예를 들어, 패턴에 선택적 구문 `[]`와 엔터티 구문 `{}`가 데이터를 잘못 추출하는 방식으로 결합되어 있다고 가정해 봅니다.

패턴 `[find] email about {subject} [from {person}]'을 고려해 봅니다.

다음 발언에서 **subject** 및 **person** 엔터티는 올바르게 추출되고 올바르지 않게 추출됩니다.

|발화|엔터티|올바른 추출|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

위의 표에서 제목은 (책 제목) 이어야 `the man from La Mancha` 하지만 제목에 선택적 단어가 `from`포함 되어 있으므로 제목은 잘못 예측 됩니다. 

패턴에서 이러한 예외를 해결하려면 [명시적 목록에 대한 API 작성](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)을 사용하여 {subject} 엔터티에 대한 명시적 목록 일치로 `the man from la mancha`를 추가합니다.

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>템플릿 발언에서 선택적 텍스트를 표시하는 구문
정규식 대괄호 구문 `[]`을 사용하여 발언의 선택적 텍스트에 표시합니다. 선택적 텍스트는 대괄호에 최대 2개의 중괄호를 중첩할 수 있습니다.

|선택적 텍스트 사용 패턴|의미|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`및 `from {person}` 는 선택 사항입니다.|
|' 도와 주세요. [?]|구두점 표시는 선택 사항입니다.|

문장 부호 (`?`, `!`, `.`)는 무시 해야 하며 패턴의 대괄호 구문을 사용 하 여 무시 해야 합니다. 

## <a name="pattern-only-apps"></a>패턴 전용 앱
각 의도에 대 한 패턴이 있는 한 예 길이 발언 없는 의도를 사용 하 여 앱을 빌드할 수 있습니다. 패턴 전용 앱의 경우에는 예제 길이 발언이 필요 하므로 패턴은 컴퓨터에서 배운 엔터티를 포함 하지 않아야 합니다. 

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이 자습서의 패턴을 구현하는 방법 알아보기](luis-tutorial-pattern.md)
