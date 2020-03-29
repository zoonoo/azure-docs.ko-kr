---
title: 패턴 구문 참조 - LUIS
titleSuffix: Azure Cognitive Services
description: 개체를 만들어 LUIS(언어 이해) 앱에서 사용자 발언에서 주요 데이터를 추출합니다. 추출된 데이터는 클라이언트 응용 프로그램에서 사용됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 696f4bdc22bed01a4b5be8bff63ade482a8dbe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221282"
---
# <a name="pattern-syntax"></a>패턴 구문

패턴 구문은 발언의 템플릿입니다. 템플릿에는 무시하려는 단어 및 문장 부호 뿐만 아니라 일치시키려는 단어 및 엔터티도 포함되어야 합니다. 정규식은 **아닙니다**.

> [!CAUTION]
> 패턴에는 하위 구성 요소가 아닌 기계 학습 엔터티 부모만 포함됩니다.

패턴의 엔터티는 중괄호 `{}`로 묶입니다. 패턴은 엔터티 및 역할이 있는 엔터티를 포함할 수 있습니다. [pattern.any는](luis-concept-entity-types.md#patternany-entity) 패턴에서만 사용되는 엔터티입니다.

패턴 구문은 다음 구문을 지원합니다.

|함수|구문|중첩 수준|예제|
|--|--|--|--|
|엔터티| {}- 곱슬 대괄호|2|{엔터티 이름}의 양식은 어디에 있습니까?|
|선택 사항|[] - 대괄호<BR><BR>선택적 및 그룹화의 조합의 중첩 수준에 는 3개로 제한됩니다. |2|물음표는 선택 사항입니다 [?]|
|그룹화|() - 괄호|2|\| (b)|
|또는| \|- 수직 막대 (파이프)<br><br>한 그룹에서 세로 막대(Or)에 2개로 제한됩니다. |-|양식({양식 이름 짧은} &#x7c; {양식 이름-긴} &#x7c; {양식 번호})|
|발언시작 및/또는 끝|^ - 카테|-|^발언 시작<br>발언이 완료되었습니다^<br>^{number} 엔터티로 전체 발화의 엄격한 리터럴 일치^|

## <a name="nesting-syntax-in-patterns"></a>패턴에 구문 중첩

대괄호가 있는 **선택적** 구문은 두 개의 레벨을 중첩할 수 있습니다. 예: `[[this]is] a new form` 이 예제에서는 다음과 같은 발언을 허용합니다.

|중첩된 선택적 발화 예제|설명|
|--|--|
|이것은 새로운 양식입니다.|패턴의 모든 단어와 일치합니다.|
|는 새 양식입니다.|패턴에서 외부 선택적 단어 및 선택 사항이 아닌 단어와 일치합니다.|
|새 양식|필요한 단어만 일치합니다.|

괄호와 **함께 그룹화** 구문은 두 개의 수준을 중첩할 수 있습니다. 예: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )` 이 기능을 사용하면 세 엔터티 중 어느 요소도 일치할 수 있습니다.

Entity1이 출발지(시애틀) 및 대상(카이로)과 같은 역할이 있는 위치이고 엔터티 2가 목록 엔터티(RedWest-C)에서 알려진 건물 이름인 경우 다음 발언은 이 패턴에 매핑됩니다.

|중첩된 그룹화 발언 예제|설명|
|--|--|
|레드웨스트-C|외부 그룹화 엔터티와 일치|
|Seattle|내부 그룹화 엔터티 중 하나와 일치합니다.|
|Cairo|내부 그룹화 엔터티 중 하나와 일치합니다.|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>선택적 구문이 있는 그룹에 대한 중첩 제한

**선택적** 구문으로 **그룹화하는** 경우 중첩 수준이 3개로 제한됩니다.

|허용됨|예제|
|--|--|
|yes|( ( ((1 &#x7c; 테스트2) ] &#x7c; 테스트3)|
|예|( [ [ [test1] &#x7c; 테스트2 ] &#x7c; 테스트3 )|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>또는-ing 구문이 있는 그룹에 대한 중첩 제한

**또는 ing** 구문으로 **그룹화하는** 조합은 2개의 세로 막대로 제한됩니다.

|허용됨|예제|
|--|--|
|yes|(test1 &#x7c; &#x7c; (테스트3 &#x7c; 테스트4)|
|예|(test1 &#x7c; test2 &#x7c; &#x7c; (테스트4 &#x7c; 테스트5) |

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

PATTERN.any 엔터티를 기반으로 책 제목이 끝나는 위치를 LUIS가 알고 있기 때문에 책 제목의 단어는 LUIS에 혼동되지 않습니다.

## <a name="explicit-lists"></a>명시적 목록

다음을 수행할 때 예외를 허용하도록 작성 API를 통해 [명시적 목록을](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) 만듭니다.

* 패턴에 패턴이 포함되어 [있습니다.](luis-concept-entity-types.md#patternany-entity)
* 그리고 이 패턴 구문을 사용하면 발언을 기반으로 잘못된 엔터티 추출이 발생할 수 있습니다.

예를 들어, 패턴에 선택적 구문 `[]`와 엔터티 구문 `{}`가 데이터를 잘못 추출하는 방식으로 결합되어 있다고 가정해 봅니다.

패턴 `[find] email about {subject} [from {person}]'을 고려해 봅니다.

다음 발언에서 **subject** 및 **person** 엔터티는 올바르게 추출되고 올바르지 않게 추출됩니다.

|발화|엔터티|올바른 추출|
|--|--|:--:|
|email about dogs from Chris|subject=dogs<br>person=Chris|✔|
|email about the man from La Mancha|subject=the man<br>person=La Mancha|X|

앞의 표에서 제목은 (책 제목)이어야 `the man from La Mancha` 하지만 제목에 선택적 `from`단어가 포함되어 있으므로 제목이 잘못 예측됩니다.

패턴에서 이러한 예외를 해결하려면 [명시적 목록에 대한 API 작성](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8)을 사용하여 {subject} 엔터티에 대한 명시적 목록 일치로 `the man from la mancha`를 추가합니다.

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>템플릿 발언에서 선택적 텍스트를 표시하는 구문
정규식 대괄호 구문 `[]`을 사용하여 발언의 선택적 텍스트에 표시합니다. 선택적 텍스트는 대괄호에 최대 2개의 중괄호를 중첩할 수 있습니다.

|선택적 텍스트 사용 패턴|의미|
|--|--|
|`[find] email about {subject} [from {person}]`|`find`및 `from {person}` 선택 사항입니다.|
|'도와줄 수 있을까?]|문장 부호는 선택 사항입니다.|

구두점 (,`?` `!` `.`) 은 무시해야하며 패턴의 대괄호 구문을 사용하여 무시해야합니다.

## <a name="next-steps"></a>다음 단계

패턴에 대해 자세히 알아보기:

* [패턴을 추가하는 방법](luis-how-to-model-intent-pattern.md)
* [pattern.any 엔터티를 추가하는 방법](luis-how-to-add-entities.md#add-a-patternany-entity)
* [패턴 개념](luis-concept-patterns.md)

.json 응답에서 감정이 어떻게 반환되는지 [이해합니다.](luis-reference-prebuilt-sentiment.md)