---
title: 예측에 도움이 되는 패턴
titleSuffix: Language Understanding - Azure Cognitive Services
description: 패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: diberry
ms.openlocfilehash: 46c9eb99d808874e0f49dee5fa4865a4867873f1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271524"
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

앱이 문장 길이가 다르고, 단어 순서가 다르고, 사용되는 단어(“부하 직원” “관리자”, “직속 부하”)도 다른 10~20개 발언을 포함하는 경우 LUIS는 낮은 신뢰도 점수를 반환할 수 있습니다. LUIS가 단어 순서의 중요성을 이해하도록 하려면 패턴을 만듭니다. 

패턴은 다음과 같은 상황을 해결합니다. 

* 의도 점수가 낮은 경우
* 올바른 의도가 최상위 점수를 받지 못했으나 최상위 점수에 근접한 경우 

## <a name="patterns-are-not-a-guarantee-of-intent"></a>패턴은 의도를 보장하지 않음
패턴은 혼합된 예측 기술을 사용합니다. 템플릿 발언에 대한 의도를 패턴으로 설정해도 의도 예측이 제대로 이루어질 것이라고 보장할 수 없지만 강력한 신호가 될 수 있습니다. 

## <a name="patterns-do-not-improve-entity-detection"></a>패턴을 사용해도 엔터티 검색이 개선되지 않음
패턴에 엔터티가 필요한 경우 패턴은 엔터티 검색에 도움이 되지 않습니다. 패턴은 의도 및 역할이 있는 예측에만 도움을 줍니다.  

여러 발언을 단일 패턴으로 축소하는 경우에는 향상된 엔터티 예측을 기대하지 못합니다. 간단한 엔터티를 발생시키려면 발언을 추가하거나 목록 엔터티를 사용합니다. 그렇지 않으면 패턴이 발생하지 않습니다.

## <a name="patterns-use-entity-roles"></a>패턴에 엔터티 역할 사용
패턴의 둘 이상의 엔터티가 문맥상 관련이 있는 경우 패턴은 엔터티 [역할](luis-concept-roles.md)을 사용하여 엔터티에 대한 컨텍스트 정보를 추출합니다. 이것은 계층적 엔터티 자식과 동일하지만 **반드시** 패턴에서만 사용할 수 있습니다. 

## <a name="prediction-scores-with-and-without-patterns"></a>패턴이 있거나 없는 예측 점수
충분한 예제 발언이 제공될 경우, LUIS는 패턴 없이 예측 신뢰도를 높일 수 있습니다. 패턴은 많은 발언을 제공할 필요 없이, 신뢰도 점수를 높입니다.  

## <a name="pattern-matching"></a>패턴 일치
먼저 패턴 내 엔터티를 검색된 후, 패턴의 나머지 단어 및 단어 순서가 유효한지 검사됩니다. 패턴이 일치하려면 패턴에 엔터티가 필요합니다. 패턴은 문자 수준이 아닌 토큰 수준에서 적용됩니다. 

## <a name="pattern-syntax"></a>패턴 구문
패턴 구문은 발언의 템플릿입니다. 템플릿에는 무시하려는 단어 및 문장 부호 뿐만 아니라 일치시키려는 단어 및 엔터티도 포함되어야 합니다. 정규식은 **아닙니다**. 

패턴의 엔터티는 중괄호 `{}`로 묶입니다. 패턴은 엔터티 및 역할이 있는 엔터티를 포함할 수 있습니다. Pattern.any는 패턴에만 사용되는 엔터티입니다. 구문은 다음 섹션에 설명되어 있습니다.

### <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>패턴 템플릿에 엔터티를 추가하는 구문
패턴 템플릿에 엔터티를 추가하려면 엔터티 이름을 중괄호로 묶습니다(예: `Who does {Employee} manage?`). 

|엔터티 사용 패턴|
|--|
|`Who does {Employee} manage?`|

### <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>패턴 템플릿에 엔터티 및 역할을 추가하는 구문
엔터티 역할은 엔터티 이름에 콜론을 입력하고 역할 이름을 입력하여 `{entity:role}`로 표시합니다. 패턴 템플릿에 역할이 있는 엔터티를 추가하려면 엔터티 이름과 역할 이름을 중괄호로 묶습니다(예: `Book a ticket from {Location:Origin} to {Location:Destination}`). 

|엔터티 역할을 사용한 패턴|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

### <a name="syntax-to-add-a-patternany-to-pattern-template"></a>패턴 템플릿에 pattern.any를 추가하는 구문
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

이러한 책 제목 예제에서 책 제목의 문맥상 단어는 LUIS에 혼동을 주지 않습니다. LUIS는 책 제목 패턴을 따르며 Pattern.any 엔터티로 표시되어 있으므로 책 제목이 끝나는 위치를 잘 압니다.

### <a name="explicit-lists"></a>명시적 목록
패턴에 Pattern.any가 포함되어 있고 패턴 구문이 발언에 따라 잘못된 엔터티가 추출될 가능성을 허용하는 경우 작성 API를 통해 [명시적 목록](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)을 만들어 예외를 허용하도록 합니다. 

예를 들어, 패턴에 선택적 구문 `[]`와 엔터티 구문 `{}`가 데이터를 잘못 추출하는 방식으로 결합되어 있다고 가정해 봅니다.

패턴 `[find] email about {subject} [from {person}]'을 고려해 봅니다. 다음 발언에서 **subject** 및 **person** 엔터티는 올바르게 추출되고 올바르지 않게 추출됩니다.

|발화|엔터티|올바른 추출|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

위의 표의 발언 `email about the man from La Mancha`에서 제목은 `the man from La Mancha`(책 제목)이지만 제목에 선택적 단어인 `from`이 포함되어 있으므로 제목이 잘못 예측됩니다. 

패턴에서 이러한 예외를 해결하려면 [명시적 목록에 대한 API 작성](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)을 사용하여 {subject} 엔터티에 대한 명시적 목록 일치로 `the man from la mancha`를 추가합니다.

### <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>템플릿 발언에서 선택적 텍스트를 표시하는 구문
정규식 대괄호 구문 `[]`을 사용하여 발언의 선택적 텍스트에 표시합니다. 선택적 텍스트는 대괄호에 최대 2개의 중괄호를 중첩할 수 있습니다.

|선택적 텍스트 사용 패턴|
|--|
|`[find] email about {subject} [from {person}]`|

`.`, `!` 및 `?`와 같은 문장 부호는 대괄호를 사용하여 무시할 수 있습니다. 이러한 표시를 무시하려면 각 표시를 별도 패턴에 사용해야 합니다. 현재, 선택적 구문은 여러 항목 목록에서 항목을 무시하는 것을 지원하지 않습니다.

## <a name="patterns-only"></a>패턴만
LUIS에서는 의도에 예제 발언이 없는 앱이 허용됩니다. 이 사용 방식은 패턴을 사용할 때만 허용됩니다. 각 패턴에는 하나 이상의 엔터티가 필요합니다. 패턴 전용 앱의 경우, 패턴에 기계 학습된 엔터티가 포함되면 안 됩니다. 이러한 엔터티에는 예제 발언이 필요하기 때문입니다. 

## <a name="best-practices"></a>모범 사례
[모범 사례](luis-concept-best-practices.md)를 알아봅니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [이 자습서의 패턴을 구현하는 방법 알아보기](luis-tutorial-pattern.md)