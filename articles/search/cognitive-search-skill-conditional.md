---
title: 조건부 인지 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 조건부 기술을 사용하면 기술 집합 정의에서 값을 필터링, 기본값 만들기 및 병합할 수 있습니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792040"
---
# <a name="conditional-cognitive-skill"></a>조건부 인지 기술

**조건부** 기술을 사용하면 부울 작업이 필요한 Azure Cognitive Search 시나리오를 사용하여 출력에 할당할 데이터를 결정할 수 있습니다. 이러한 시나리오에는 필터링, 기본값 할당 및 조건에 따라 데이터 병합이 포함됩니다.

다음 의사 코드는 조건부 기술이 수행하는 작업을 보여 줍니다.

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 이 기술은 Azure 인지 서비스 API에 바인딩되지 않으며 사용 요금이 부과되지 않습니다. 그러나 [코그너티브 서비스 리소스를 첨부하여](cognitive-search-attach-cognitive-services.md) 하루에 소수의 보강으로 제한하는 "무료" 리소스 옵션을 재정의해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>평가된 필드

이 기술은 입력이 필드를 평가하기 때문에 특별합니다.

다음 항목은 식의 유효한 값입니다.

-   별표 경로(식의 경로는 "$(" 및 ")"로 구분되어야 합니다.
 <br/>
    예제:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  리터럴(문자열, 숫자, 참, 거짓, null) <br/>
    예제:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  비교 연산자(=== !==, >=, >, <=, <)를 사용하는 표현식 <br/>
    예제:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   부울 연산자(&&, || <br/>
    예제:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   숫자 연산자(+, -, \*,, %)를 사용하는 표현식 <br/>
    예제: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

조건부 기술은 평가를 지원하므로 사소한 변환 시나리오에서 사용할 수 있습니다. 예를 들어 [기술 정의 4를](#transformation-example)참조하십시오.

## <a name="skill-inputs"></a>기술 입력
입력은 대/소문자를 구분합니다.

| 입력   | 설명 |
|-------------|-------------|
| condition(조건)   | 이 입력은 평가할 조건을 나타내는 [평가된 필드입니다.](#evaluated-fields) 이 조건은 부울*값(true* 또는 *false)으로*평가해야 합니다.   <br/>  예제: <br/> "= 사실" <br/> "= $(/문서/언어) =='fr'" <br/> "= $(/문서/페이지//언어)\*== $(/문서/예상언어)" <br/> |
| 때True    | 이 입력은 조건이 *true로*평가되는 경우 반환할 값을 나타내는 평가된 [필드입니다.](#evaluated-fields) 상수 문자열은 단일 따옴표(' 및 ')로 반환되어야 합니다. <br/>샘플 값: <br/> "= '계약'"<br/>"= $(/문서/계약유형)" <br/> "= $(/문서/엔터티/\*)" <br/> |
| 때 거짓   | 이 입력은 조건이 *false로*평가되는 경우 반환할 값을 나타내는 평가된 [필드입니다.](#evaluated-fields) <br/>샘플 값: <br/> "= '계약'"<br/>"= $(/문서/계약유형)" <br/> "= $(/문서/엔터티/\*)" <br/>

## <a name="skill-outputs"></a>기술 출력
단순히 "출력"이라고 하는 단일 출력이 있습니다. 조건이 false인 경우 *false* 또는 조건이 true인 경우 *true값을* 반환합니다.

## <a name="examples"></a>예

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>샘플 기술 정의 1: 프랑스어 문서만 반환하도록 문서 필터링

다음 출력은 문서의 언어가 프랑스어인 경우 문장 배열("/문서/frenchSentences")을 반환합니다. 언어가 프랑스어가 아닌 경우 값은 *null로*설정됩니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
"/document/frenchSentences"를 다른 기술의 *컨텍스트로* 사용하는 경우 해당 기술은 "/문서/frenchSentences"가 *null로*설정되지 않은 경우에만 실행됩니다.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>샘플 기술 정의 2: 존재하지 않는 값에 대한 기본값을 설정합니다.

다음 출력은 문서의 언어로 설정되거나 언어가 설정되지 않은 경우 "es"로 설정된 주석("/문서/언어WithDefault")을 만듭니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>샘플 기술 정의 3: 두 필드의 값을 하나의 필드로 병합

이 예에서 일부 문장에는 *프렌치감정* 속성이 있습니다. *프렌치감정* 속성이 null일 때마다 *영어감정* 값을 사용하려고 합니다. 감정("/문서/감정/감정")이라고 *sentiment* 하는 멤버에 출력을 할당합니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>변환 예제
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>샘플 기술 정의 4: 단일 필드의 데이터 변환

이 예제에서는 0에서 1 사이의 *감정을* 받습니다. 우리는 -1과 1 사이로 변환하고 싶습니다. 조건부 기술을 사용하여 이 사소한 변환을 수행할 수 있습니다.

이 예제에서는 조건이 항상 *true이기*때문에 기술의 조건부 측면을 사용하지 않습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>특별 고려 사항
일부 매개 변수가 평가되므로 문서화된 패턴을 따르기 위해 특히 주의해야 합니다. 식은 등가 부호로 시작해야 합니다. 경로는 "$(")"와 ""로 구분되어야 합니다. 문자열을 단일 따옴표에 넣어야 합니다. 이를 통해 계산자는 문자열과 실제 경로 및 연산자 구분을 할 수 있습니다. 또한 연산자 주위에 공백을 두어야 합니다(예: 경로에 "*"는 곱하기와 는 다른 것을 의미합니다).


## <a name="next-steps"></a>다음 단계

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
