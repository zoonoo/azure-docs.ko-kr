---
title: 조건부 cognitive search 기술 (Azure Search) | Microsoft Docs
description: 조건부 기술에는 필터링, 기본값을 만들고, 값 병합 수 있습니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 149b701d4a1700787656448e2bdd0d92d2a93844
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791512"
---
#   <a name="conditional-skill"></a>조건부 기술

합니다 *조건부 기술* 출력에 할당할 데이터를 결정 한 부울 연산이 필요로 하는 Azure Search 시나리오를 사용 하도록 설정 합니다. 이러한 시나리오에는 필터링, 기본 값을 할당 및 데이터 조건에 따라 병합 포함 됩니다.

다음 의사 코드 조건부 기술 무엇을 수행 하는 방법을 보여 줍니다.

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 이 기술 Azure Cognitive Services API에 바인딩되어 있지 않은 및 사용에 대 한 요금이 청구 되지 않습니다. 하지만 사용자는 여전히 해야 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md) 하루 원칙이 수가 적은 수를 제한 하는 "무료" 리소스 옵션을 무시 하려면.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>계산된 필드

이 기술 입력을 계산된 필드 이므로 특수 합니다.

다음 항목은 식의 유효한 값:

-   주석 경로 (식에서 경로 구분 해야 합니다 "$(" and ")")
 <br/>
    예제:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  리터럴 (문자열, 숫자, true, false, null) <br/>
    예제:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  비교 연산자를 사용 하는 식 (= =,! =, > =, >, < =, <) <br/>
    예제:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   부울 연산자를 사용 하는 식 (& &, | |,!, ^) <br/>
    예제:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   숫자 연산자를 사용 하는 식 (+,-, \*, /, %) <br/>
    예제: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

조건부 기술 평가 지원 하므로 부 변환 시나리오에서 사용할 수 있습니다. 예를 들어, 참조 [기술 정의 4](#transformation-example)합니다.

## <a name="skill-inputs"></a>기술 입력
입력은 대/소문자를 구분합니다.

| 입력   | 설명 |
|-------------|-------------|
| condition   | 이 입력 되는 [필드를 평가](#evaluated-fields) 나타내는 평가할 조건입니다. 이 조건은 부울 값으로 평가 해야 합니다 (*true* 하거나 *false*).   <br/>  예제: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 이 입력 되는 [필드를 평가](#evaluated-fields) 에 조건이 평가 되는 경우에 반환할 값을 나타내는 *true*합니다. 작은따옴표로 상수 문자열을 반환 합니다 ('및'). <br/>샘플 값: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 이 입력 되는 [필드를 평가](#evaluated-fields) 에 조건이 평가 되는 경우에 반환할 값을 나타내는 *false*합니다. <br/>샘플 값: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>기술 출력
"출력입니다."를 호출 하기만 하면 되는 단일 출력은 값을 반환 한다는 *whenFalse* 조건이 false 인 경우 또는 *whenTrue* 조건이 true 인 경우.

## <a name="examples"></a>예

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>1 정의 기술 하는 샘플: 프랑스어 문서만 반환 하는 문서를 필터링 합니다.

다음 출력 문서의 언어를 프랑스어 경우 문장 ("/ 문서/frenchSentences")의 배열을 반환 합니다. 값으로 설정 됩니다 언어 아니면 프랑스어 *null*합니다.

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
"/ 문서/frenchSentences"으로 사용 됩니다 합니다 *상황에 맞는* 의 다른 기술 스킬을 경우에 실행 "/ 문서/frenchSentences"으로 설정 되지 *null*합니다.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>2 정의 기술 하는 샘플: 존재 하지 않는 값에 대 한 기본값 설정

다음 출력 언어 설정 하지 않은 경우 문서의 언어 또는 "es" 설정 되는 주석 ("/ 문서/languageWithDefault")를 만듭니다.

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

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>3 정의 기술 하는 샘플: 두 필드의 값을 하나로 병합

일부 문장에이 예는 *frenchSentiment* 속성입니다. 때마다 합니다 *frenchSentiment* 속성이 null 이면 사용 하고자 합니다 *englishSentiment* 값입니다. 출력 호출 되는 멤버에 할당 *감정* ("/ 문서화/감정 / * / 감정").

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
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>4 정의 기술 하는 샘플: 단일 필드에서 데이터 변환

이 예제에서는 수신를 *감정* 0과 1 사이입니다. -1과 1 사이의 수를 변환 하려고 합니다. 조건부 기술 부이 변환을 수행 하려면 사용할 수 있습니다.

이 예제에서는 사용 하지 조건이 항상 있으므로 스킬 조건부 측면 *true*합니다.

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
일부 매개 변수 계산 되므로 문서화 된 패턴에 따라 특히 주의 해야 합니다. 식은 등호를 사용 하 여 시작 해야 합니다. 경로 구분 해야 합니다 "$(" and ")"입니다. 작은따옴표에 문자열을 저장 해야 합니다. 계산기 문자열 및 실제 경로 및 연산자를 구분할 수 있습니다. 또한 연산자 주위의 공백을 삽입할 있는지도 확인 합니다 (예를 들어를 "*" 경로에 다른 의미로 multiply 보다).


## <a name="next-steps"></a>다음 단계

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
