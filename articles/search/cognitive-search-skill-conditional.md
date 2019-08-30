---
title: 조건부 인식 검색 기술 (Azure Search) | Microsoft Docs
description: 조건부 기술은 필터링, 기본값 만들기 및 값 병합을 가능 하 게 합니다.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 29e50a3d978338eaa46566574e6a20685a14bda4
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186384"
---
#   <a name="conditional-skill"></a>조건부 기술

*조건부 스킬* 은 부울 연산이 출력에 할당할 데이터를 결정 하는 데 필요한 Azure Search 시나리오를 가능 하 게 합니다. 이러한 시나리오에는 조건을 기반으로 필터링, 기본값 할당 및 데이터 병합이 포함 됩니다.

다음 의사 코드는 조건부 기술에서 수행 하는 작업을 보여 줍니다.

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 이 기술은 Azure Cognitive Services API에 바인딩되지 않으며 사용에 대 한 요금이 청구 되지 않습니다. 그러나 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md) 하 여 하루에 적은 수의 강화를 제한 하는 "무료" 리소스 옵션을 재정의 해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>평가 필드

이 기술은 해당 입력이 계산 필드 이므로 특수 합니다.

다음 항목은 식의 유효한 값입니다.

-   주석 경로 (식의 경로는 "$ (" 및 ")"로 구분 되어야 합니다.)
 <br/>
    예를 들면 다음과 같습니다.
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  리터럴 (문자열, 숫자, true, false, null) <br/>
    예를 들면 다음과 같습니다.
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  비교 연산자를 사용 하는 식 (= =,! =, > =, >, < =, <) <br/>
    예를 들면 다음과 같습니다.
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   부울 연산자 (& &, | |,!, ^)를 사용 하는 식 <br/>
    예를 들면 다음과 같습니다.
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   숫자 연산자 (+,-, \*,/,%)를 사용 하는 식 <br/>
    예를 들면 다음과 같습니다. 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

조건부 기술은 평가를 지원 하기 때문에 보조 변환 시나리오에서 사용할 수 있습니다. 예를 들어 [기술 정의 4](#transformation-example)를 참조 하십시오.

## <a name="skill-inputs"></a>기술 입력
입력은 대/소문자를 구분합니다.

| 입력   | Description |
|-------------|-------------|
| 조건   | 이 입력은 평가할 조건을 나타내는 [평가 된 필드](#evaluated-fields) 입니다. 이 조건은 부울 값 (*true* 또는 *false*)으로 계산 되어야 합니다.   <br/>  예를 들면 다음과 같습니다. <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 이 입력은 조건이 *true*로 평가 되는 경우 반환할 값을 나타내는 [계산 된 필드](#evaluated-fields) 입니다. 상수 문자열은 작은따옴표 (' 및 ')로 반환 되어야 합니다. <br/>샘플 값: <br/> "= ' 계약 '"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 이 입력은 조건이 *false*로 평가 되는 경우 반환할 값을 나타내는 [계산 된 필드](#evaluated-fields) 입니다. <br/>샘플 값: <br/> "= ' 계약 '"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>기술 출력
단순히 "output" 이라고 하는 단일 출력이 있습니다. 조건이 false 이면 *false* 값을 반환 하 고 조건이 True 이면 *whenTrue* 을 반환 합니다.

## <a name="examples"></a>예

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>샘플 기술 정의 1: 문서를 필터링 하 여 프랑스어 문서만 반환

다음 출력은 문서 언어가 프랑스어 인 경우 문장 ("/document/frenchSentences")의 배열을 반환 합니다. 언어가 프랑스어가 아니면 값은 *null*로 설정 됩니다.

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
"/Document/frenchSentences"가 다른 기술 *컨텍스트로* 사용 되는 경우 "/document/frenchSentences"가 *null*로 설정 되지 않은 경우에만이 기술이 실행 됩니다.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>샘플 기술 정의 2: 존재 하지 않는 값의 기본값 설정

다음 출력은 문서 언어로 설정 된 주석 ("/document/languageWithDefault") 또는 언어가 설정 되지 않은 경우 "es"를 만듭니다.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>샘플 기술 정의 3: 두 필드의 값을 하나로 병합

이 예제에서는 일부 문장에 *frenchSentiment* 속성이 있습니다. *FrenchSentiment* 속성이 null 인 경우에는 *englishSentiment* 값을 사용 하려고 합니다. *감정* ("/document/sentiment/*/sentiment") 라는 멤버에 출력을 할당 합니다.

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

## <a name="transformation-example"></a>변환 예
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>샘플 기술 정의 4: 단일 필드에 대 한 데이터 변환

이 예제에서는 0에서 1 사이의 *감정* 를 받습니다. -1에서 1 사이를 변환 하려고 합니다. 조건부 기술을 사용 하 여 이러한 사소한 변환을 수행할 수 있습니다.

이 예에서는 조건이 항상 *true*이기 때문에 기술에 대 한 조건부 측면을 사용 하지 않습니다.

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
일부 매개 변수는 평가 되므로 문서화 된 패턴을 따르는 것이 특히 주의 해야 합니다. 식은 등호 (=)로 시작 해야 합니다. 경로는 "$ (" 및 ")"로 구분 되어야 합니다. 문자열을 작은따옴표로 묶어 두십시오. 이를 통해 계산기는 문자열과 실제 경로 및 연산자를 구분할 수 있습니다. 또한 연산자 주위에 공백을 넣어야 합니다. 예를 들어 경로에서 "*"는 곱하기와 다른 항목을 의미 합니다.


## <a name="next-steps"></a>다음 단계

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
