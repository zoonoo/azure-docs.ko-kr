---
title: 조건부 cognitive search 기술 (Azure Search) | Microsoft Docs
description: 필터링, 기본값을 만들고, 값 병합을 허용 하는 조건부 기술 합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: luisca
ms.openlocfilehash: 6a203a38437ccb6a9c325e6594289744e0148c84
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028427"
---
#   <a name="conditional-skill"></a>조건부 기술

합니다 **조건부 기술** 다양 한 출력에 할당 되어야 하는 데이터를 결정 한 부울 연산이 필요한 시나리오를 사용 하도록 설정 합니다. 이러한 시나리오에 포함 됩니다: 조건에 따라 필터링, 기본 값을 할당 및 데이터를 병합 합니다.

다음 의사 코드 조건부 기술에서 수행 하는 항목을 설명 합니다.

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> 이 기술은 Cognitive Services API에 바인딩되지 않으며 사용 요금이 부과되지 않습니다. 그렇지만 **무료** 리소스 옵션을 재정의하여 일별 보강 횟수를 낮게 제한하려면 계속해서 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>계산된 필드

이 기술 입력을 계산된 필드 이므로 특수 합니다.

다음은 식의 유효한 값입니다.

-   주석 경로 (식에서 경로 구분 해야 합니다 "$(" and ")") <br/>
    예제:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  리터럴 (문자열, 숫자, true, false, null) <br/>
    예제:
    ```
       "= 'this is a string'"   // string, note the single quotes
       "= 34"                   // number
       "= true"                 // boolean
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

지원 되는 평가 인해 조건부 기술 부 변환 시나리오에 사용할 수 있습니다. 샘플을 참조 하세요 [기술 정의 4](#transformation-examples) 예입니다.

## <a name="skill-inputs"></a>기술 입력
입력은 대/소문자를 구분합니다.

| 입력      | 설명 |
|-------------|-------------|
| condition   | 이 입력 되는 [필드를 평가](#evaluated-fields) 나타내는 평가할 조건입니다. (True 또는 false)이이 조건은 부울 값으로 평가 해야 합니다.   <br/>  예제: <br/> "= true" <br/> "= $(/document/language) =='fr'" <br/> "= $(/document/pages/\*/language) == $(/document/expectedLanguage)" <br/> |
| whenTrue    | 이 입력 되는 [필드를 평가](#evaluated-fields)합니다. 조건이 평가 되는 경우 반환 값을 true로 합니다. 상수 문자열에서 반환될지 ' ' 따옴표입니다. <br/>샘플 값: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/> |
| whenFalse   | 이 입력 되는 [필드를 평가](#evaluated-fields)합니다. 조건이 false로 평가 되는 경우 반환할 값입니다.  <br/>샘플 값: <br/> "= 'contract'"<br/>"= $(/document/contractType)" <br/> "= $(/document/entities/\*)" <br/>

## <a name="skill-outputs"></a>기술 출력
'출력' 이라는 단일 출력이 있습니다. 조건이 true 인 경우 조건이 false 이면 whenFalse 또는 whenTrue 값을 반환 됩니다.

## <a name="examples"></a>예

### <a name="sample-skill-definition-1-filtering-documents-to-return-only-french-documents"></a>1 정의 기술 하는 샘플: "프랑스" 문서만 반환 하는 문서를 필터링 합니다.

다음 출력 문장 ("/ 문서/frenchSentences")의 배열을 문서의 언어를 경우 프랑스어를 반환 합니다. 해당 값을 설정할 언어를 프랑스어 없는 경우 null입니다.

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
"/ 문서/frenchSentences"으로 사용 되는 *상황에 맞는* 다른 기술에서의 해당 기술에만 실행 됩니다 "/ 문서/frenchSentences" 설정 되어 있지 않으면 null로


### <a name="sample-skill-definition-2-setting-a-default-value-when-it-does-not-exist"></a>2 정의 기술 하는 샘플: 존재 하지 않는 경우 기본값을 설정 합니다.

다음 출력에서는 언어 설정 되지 않은 경우 문서의 언어 또는 "es"로 설정 된 주석 ("/ 문서/languageWithDefault")를 만듭니다.

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

### <a name="sample-skill-definition-3-merging-values-from-two-different-fields-into-a-single-field"></a>3 정의 기술 하는 샘플: 단일 필드에 두 개의 다른 필드의 값을 병합

일부 문장에이 예는 *frenchSentiment* 속성입니다. 때마다 합니다 *frenchSentiment* 속성이 null 이면 사용 하고자 합니다 *englishSentiment* 값입니다. 출력 단순히 라는 멤버에 할당할 *감정* ("/ 문서화/감정 / * / 감정").

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

## <a name="transformation-examples"></a>변환 예제
### <a name="sample-skill-definition-4-performing-data-transformations-on-a-single-field"></a>4 정의 기술 하는 샘플: 단일 필드에서 데이터 변형을 수행

이 예제에서는 0과 1 사이의 감정 수신 하 고-1과 1 사이는 변환 하고자 합니다. 조건부 기술을 사용 하 여 수행할 수 있는 작은 수학 변환입니다.

이 특정 예제에서는 사용 하지 마십시오 스킬 조건부 측면 조건이 true 인 항상. 

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
다음 문서화 된 패턴에 특히 주의 해야 하므로 일부 매개 변수에 계산 되는, note 합니다. 식은 등호 기호 "="로 시작 해야 하며 경로 구분 해야 합니다 "$(" and ")"입니다. 문자열 및 실제 경로 및 연산자를 구분 하는 계산기에 도움이 되도록 하는 것과 따옴표로' 단일' 문자열에 배치 해야 합니다. 또한 연산자 주위의 공백을 삽입할 있는지도 확인 합니다 (예를 들어를 * 경로에서 곱하기 연산자 보다 다른 의미를 갖습니다).


## <a name="next-steps"></a>다음 단계

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
