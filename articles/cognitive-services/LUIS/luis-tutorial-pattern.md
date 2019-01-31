---
title: 패턴
titleSuffix: Azure Cognitive Services
description: 패턴을 사용하여 의도 및 엔터티 예측을 높이는 한편 발화 예제를 줄입니다. 패턴은 엔터티 및 무시 가능한 텍스트를 식별하는 구문을 포함하는 템플릿 발언 예제를 통해 제공됩니다.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cgronlun
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: e5aadfc8319ff67e471123d457fddf468af4842a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225774"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>자습서: 일반적인 패턴 템플릿 발언 서식 추가

이 자습서에서는 패턴을 사용하여 의도 및 엔터티 예측을 높이는 한편 발화 예제를 줄입니다. 패턴은 엔터티 및 무시 가능한 텍스트를 식별하는 구문을 포함하는 템플릿 발언 예제를 통해 제공됩니다. 패턴은 식 일치 및 기계 학습의 조합입니다.  의도 발언과 함께 템플릿 발언 예제는 의도에 맞는 발언을 LUIS가 더 잘 이해하도록 합니다. 

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 예제 앱 가져오기 
> * 의도 만들기
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기
> * 패턴 만들기
> * 향상된 패턴 예측 기능 확인
> * 무시 가능한 텍스트로 표시 및 패턴 내 중첩
> * 테스트 패널을 사용하여 패턴 성공 확인

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>앱 가져오기 예제

마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

다음 단계를 사용하세요.

1.  [앱 JSON 파일](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `patterns`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다.

## <a name="create-new-intents-and-their-utterances"></a>새 의도 및 해당 발언 만들기

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **의도** 페이지에서 **새 의도 만들기**를 선택합니다. 

3. 팝업 대화 상자에서 `OrgChart-Manager`을 입력하고 **완료**를 선택합니다.

    ![새 메시지 만들기 팝업 창](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |Who is John W. Smith the subordinate of?|
    |Who does John W. Smith report to?|
    |Who is John W. Smith's manager?|
    |Who does Jill Jones directly report to?|
    |Who is Jill Jones supervisor?|

    [![의도에 새 발언을 추가하는 LUIS 스크린샷](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "의도에 새 발언을 추가하는 LUIS 스크린샷")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    의도의 발언에서 Employee 엔터티 대신 keyPhrase 엔터티가 레이블로 지정되더라도 걱정하지 마세요. 두 엔터티 모두 테스트 창 및 끝점에서 올바르게 예측됩니다. 

5. 왼쪽 탐색 영역에서 **의도**를 선택합니다.

6. **새 의도 만들기**를 선택합니다. 

7. 팝업 대화 상자에서 `OrgChart-Reports`을 입력하고 **완료**를 선택합니다.

8. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |Who are John W. Smith's subordinates?|
    |Who reports to John W. Smith?|
    |Who does John W. Smith manage?|
    |Who are Jill Jones direct reports?|
    |Who does Jill Jones supervise?|

## <a name="caution-about-example-utterance-quantity"></a>예제 발언 수에 대한 주의 사항

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 주소의 URL 끝으로 이동하고 `Who is the boss of Jill Jones?`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

이 쿼리는 성공했나요? 이 학습 주기 동안에는 성공적이었습니다. 두 상위 의도의 점수는 서로 가깝습니다. LUIS 학습이 매번 정확히 같은 것은 아니고 약간의 편차가 있으므로 이러한 두 점수는 다음 학습 주기에서 반전될 수 있습니다. 그 결과 잘못된 의도가 반환될 수 있습니다. 

패턴을 사용하면 올바른 의도 점수가 훨씬 높은 백분율 값을 가지고 다음으로 가장 높은 점수보다 훨씬 높게 만들 수 있습니다. 

두 번째 브라우저 창을 열어 둡니다. 자습서에서 나중에 다시 사용합니다. 

## <a name="template-utterances"></a>템플릿 발언
Human Resource 도메인의 특성 때문에 조직에서 직원 관계를 묻는 몇 가지 일반적인 방법이 있습니다. 예: 

|발언|
|--|
|Who does Jill Jones report to?|
|Who reports to Jill Jones?|

이러한 발화가 너무 가까워서 많은 발화 예제를 제공하지 않고는 각각의 컨텍스트 고유성을 확인하기가 어렵습니다. 의도 패턴을 추가하면 LUIS는 많은 발화 예제를 제공하지 않고도 의도의 일반적인 발화 패턴을 학습합니다. 

이 의도에 대한 템플릿 발화 예제는 다음과 같습니다.

|템플릿 발화 예제|구문 의미|
|--|--|
|Who does {Employee} report to[?]|interchangeable {Employee}, ignore [?]}|
|Who reports to {Employee}[?]|interchangeable {Employee}, ignore [?]}|

`{Employee}` 구문은 템플릿 발화 내의 엔터티 위치 및 엔터티 형식을 표시합니다. 선택적인 `[?]` 구문은 선택적인 단어 또는 문장 부호를 표시합니다. LUIS는 대괄호로 묶인 선택적 텍스트는 무시하고 발언을 일치시킵니다.

구문은 정규식처럼 보이지만 정규식이 아닙니다. 중괄호(`{}`) 및 대괄호(`[]`) 구문만 지원됩니다. 최대 두 수준까지 중첩될 수 있습니다.

패턴이 발언에 일치되도록 하기 위해 발언 내의 엔터티가 먼저 템플릿 발언의 엔터티와 일치되어야 합니다. 그러나 의도와 달리 템플릿은 엔터티 예측에 도움이 되지 않습니다. 

**패턴을 사용하면 몇 가지 예제 발언을 제공할 수 있지만 엔터티가 검색되지 않으면 패턴이 일치되지 않습니다.**

이 자습서에서는 두 가지 새 의도인 `OrgChart-Manager`과 `OrgChart-Reports`를 추가합니다. 

|의도|발화|
|--|--|
|OrgChart-Manager|Who does Jill Jones report to?|
|OrgChart-Reports|Who reports to Jill Jones?|

LUIS가 클라이언트 앱에 예측을 반환하면, 의도 이름을 클라이언트 앱에서 함수 이름으로 사용할 수 있으며 Employee 엔터티는 해당 함수의 매개 변수로 사용할 수 있습니다.

```nodejs
OrgChartManager(employee){
    ///
}
```

[목록 엔터티 자습서](luis-quickstart-intent-and-list-entity.md)에서 직원이 생성되었다는 사실을 기억하세요.

1. 위쪽 메뉴에서 **빌드**를 선택합니다.

2. 왼쪽 탐색의 **앱 성능 개선** 아래에서 왼쪽 탐색의 **패턴**을 선택합니다.

3. **OrgChart-Manager** 의도를 선택하고, 다음 템플릿 발화를 입력합니다.

    |템플릿 발언|
    |:--|
    |Who is {Employee} the subordinate of[?]|
    |Who does {Employee} report to[?]|
    |Who is {Employee}['s] manager[?]|
    |Who does {Employee} directly report to[?]|
    |Who is {Employee}['s] supervisor[?]|
    |Who is the boss of {Employee}[?]|

    역할이 있는 엔터티는 역할 이름을 포함하는 구문을 사용하며, [역할에 대한 별도 자습서](luis-tutorial-pattern-roles.md)에서 설명됩니다. 

    템플릿 발언을 입력할 때 왼쪽 중괄호 `{`를 입력하면 LUIS는 엔터티를 채우는 데 도움을 줍니다.

    [![의도에 대한 템플릿 발언을 입력하는 스크린샷](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. **OrgChart-Reports** 의도를 선택하고, 다음 템플릿 발화를 입력합니다.

    |템플릿 발언|
    |:--|
    |Who are {Employee}['s] subordinates[?]|
    |Who reports to {Employee}[?]|
    |Who does {Employee} manage[?]|
    |Who are {Employee} direct reports[?]|
    |Who does {Employee} supervise[?]|
    |Who does {Employee} boss[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>패턴이 사용될 때의 끝점 쿼리

1. 앱을 학습하고 다시 게시합니다.

2. 엔드포인트 URL 탭으로 브라우저 탭을 다시 전환합니다.

3. 주소의 URL 끝으로 이동하고 `Who is the boss of Jill Jones?`를 발언으로 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

이제 의도 예측이 훨씬 더 높아집니다.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>선택적 텍스트 및 미리 작성된 엔터티 사용

이 자습서의 이전 패턴 템플릿 발언에는 문자 s의 소유격 `'s` 사용 및 물음표 `?` 사용과 같은 몇 가지 선택적 텍스트가 있었습니다. 엔드포인트 발언이 관리자 및 인사부 담당자가 기록 데이터와 함께 향후 계획된 사내 직원 이동을 찾고 있는 것을 보여 준다고 가정합니다.

예제 발언은 다음과 같습니다.

|의도|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

이 예제 각각에 올바른 예측을 위해 LUIS에 필요한 동사 시제 `was`, `is`, `will be`는 물론 날짜 `March 3`, `now` 및 `in a month`가 사용되었습니다. 마지막 두 가지 예제에는 `in`과 `on`을 제외하고 거의 동일한 텍스트가 사용되었습니다.

예제 템플릿 발언:
|의도|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

대괄호 `[]`의 선택적 구문을 사용하면 템플릿 발언에 선택적 텍스트를 쉽게 추가할 수 있고 두 번째 수준 `[[]]`까지 중첩할 수 있으며 엔터티 또는 텍스트를 포함할 수 있습니다.

**질문: 마지막 두 개 예제 발화가 하나의 템플릿 발화로 결합될 수 없는 이유는 무엇인가요?** 패턴 템플릿은 OR 구문을 지원하지 않습니다. `in` 버전과 `on` 버전을 모두 catch하려면 각각이 별도의 템플릿 예제여야 합니다.

**질문: 각 템플릿 발화의 첫 글자인 `w`가 모두 소문자인 이유가 무엇인가요? 필요에 따라 대문자나 소문자가 되어야 하지 않나요?** 클라이언트 애플리케이션이 쿼리 엔드포인트에 제출한 발언은 소문자로 변환됩니다. 템플릿 발언은 대문자나 소문자일 수 있으며 엔드포인트 발언도 마찬가지입니다. 비교는 항상 소문자로 변환한 후 수행됩니다.

**질문: March 3이 예측된 경우 템플릿 발화의 미리 작성된 번호 부분이 숫자 `3`과 날짜 `March 3` 모두로 예측되지 않는 이유가 무엇인가요?** 템플릿 발언은 날짜를 컨텍스트에 따라 문자 그대로 `March 3`으로 사용하거나 `in a month`로 추상화하여 사용합니다. 날짜에는 숫자가 포함될 수 있지만 숫자가 반드시 날짜로 보이지는 아닙니다. 예측 JSON 결과로 반환하려는 유형을 가장 잘 나타내는 엔터티를 항상 사용하십시오.  

**질문: `Who will {Employee}['s] manager be on March 3?`와 같이 표현이 부족한 발화의 경우는 어떤가요?** `will`과 `be`가 구분되는 경우와 같이 문법적으로 다른 동사 시제는 새로운 템플릿 발언이 되어야 합니다. 기존 템플릿 발언은 일치하지 않습니다. 발언의 의도는 변경되지 않았지만 발언의 단어 배치가 변경되었습니다. 이러한 변화는 LUIS의 예측에 영향을 미칩니다.

**기억해야 할 사항: 엔터티를 먼저 찾은 다음, 패턴을 일치시킵니다.**

## <a name="edit-the-existing-pattern-template-utterance"></a>기존 패턴 템플릿 발언 편집

1. LUIS 웹 사이트의 최상위 메뉴에서 **빌드**를 선택한 다음, 왼쪽 메뉴에서 **패턴**을 선택합니다. 

2. 기존 템플릿 발언 `Who is {Employee}['s] manager[?]`를 찾아서 오른쪽의 줄임표(***...***)를 선택합니다. 

3. 팝업 메뉴에서 **편집** 을 선택합니다. 

4. 템플릿 발언을 `who is {Employee}['s] manager [[on]{datetimeV2}?]]`로 변경합니다.

## <a name="add-new-pattern-template-utterances"></a>새 패턴 템플릿 발언 추가

1. **빌드**의 **패턴** 섹션에서 몇 가지 패턴 템플릿 발언을 새로 추가합니다. 의도 드롭 다운 메뉴에서 **OrgChart-Manager**를 선택하고 다음 템플릿 발언을 각각 입력합니다.

    |의도|선택적 텍스트와 미리 작성된 엔터티가 있는 예제 발언|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. 앱을 학습합니다.

3. 패널의 맨 위에서 **테스트**를 선택하여 테스트 패널을 엽니다. 

4. 몇 가지 테스트 발언을 입력하여 패턴이 일치하는지 의도 점수가 상당히 높은지 확인합니다. 

    첫 번째 발언을 입력한 다음, 모든 예측 결과를 볼 수 있도록 결과 아래에서 **검사**를 선택합니다.

    |발화|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

모든 발언의 내부에서 엔터티를 찾았으므로 동일한 패턴과 일치하며 예측 점수가 높습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 많은 발화 예제 없이 높은 정확도로 예측하기 어려운 발화에 대한 두 가지 의도를 추가합니다. 허용되는 이러한 LUIS 패턴을 추가하여 훨씬 높은 점수로 의도를 더 잘 예측할 수 있습니다. 엔터티와 무시 가능한 텍스트를 표시하면 LUIS에서 더 다양한 발화에 패턴을 적용할 수 있었습니다.

> [!div class="nextstepaction"]
> [패턴과 함께 역할을 사용하는 방법 알아보기](luis-tutorial-pattern-roles.md)
