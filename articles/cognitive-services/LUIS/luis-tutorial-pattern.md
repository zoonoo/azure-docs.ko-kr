---
title: 패턴을 사용하여 LUIS 예측을 개선하는 방법의 자습서 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 이 자습서에서는 의도 패턴을 사용하여 LUIS 의도 및 엔터티 예측을 개선합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238457"
---
# <a name="tutorial-improve-app-with-patterns"></a>자습서: 패턴을 사용하여 앱 개선

이 자습서에서는 패턴을 사용하여 의도 및 엔터티 예측을 확장합니다.  

> [!div class="checklist"]
* 패턴이 앱에 도움이 되는지 식별하는 방법
* 패턴을 만드는 방법 
* 패턴 예측 개선 사항을 확인하는 방법

이 문서에서는 LUIS 앱을 작성하기 위해 체험 [LUIS](luis-reference-regions.md) 계정이 필요합니다.

## <a name="before-you-begin"></a>시작하기 전에
[일괄 테스트](luis-tutorial-batch-testing.md) 자습서의 Human Resources 앱이 없으면 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json) GitHub 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `patterns`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>패턴을 사용하면 더 적은 예제로 LUIS에 일반 발화를 학습시킬 수 있습니다.
Human Resource 도메인의 특성 때문에 조직에서 직원 관계를 묻는 몇 가지 일반적인 방법이 있습니다. 예: 

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

이러한 발화가 너무 가까워서 많은 발화 예제를 제공하지 않고는 각각의 컨텍스트 고유성을 확인하기가 어렵습니다. 의도 패턴을 추가하면 LUIS는 많은 발화 예제를 제공하지 않고도 의도의 일반적인 발화 패턴을 학습합니다. 

이 의도의 예제 템플릿 발화는 다음과 같습니다.

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

패턴은 엔터티 및 무시 가능한 텍스트를 식별하는 구문을 포함하는 템플릿 발언 예제를 통해 제공됩니다. 패턴은 정규식 일치 및 기계 학습의 조합입니다.  의도 발언과 함께 템플릿 발언 예제는 의도에 맞는 발언을 LUIS가 더 잘 이해하도록 합니다.

패턴이 발언에 일치되도록 하기 위해 발언 내의 엔터티가 먼저 템플릿 발언의 엔터티와 일치되어야 합니다. 그러나 의도와 달리 템플릿은 엔터티 예측에 도움이 되지 않습니다. 

**패턴을 사용하면 몇 가지 예제 발언을 제공할 수 있지만 엔터티가 검색되지 않으면 패턴이 일치되지 않습니다.**

[목록 엔터티 자습서](luis-quickstart-intent-and-list-entity.md)에서 직원이 생성되었다는 사실을 기억하세요.

## <a name="create-new-intents-and-their-utterances"></a>새 의도 및 해당 발언 만들기
두 개의 새로운 의도 `OrgChart-Manager` 및 `OrgChart-Reports`를 추가합니다. LUIS가 클라이언트 앱에 예측을 반환하면, 의도 이름을 클라이언트 앱에서 함수 이름으로 사용할 수 있으며 Employee 엔터티는 해당 함수의 매개 변수로 사용할 수 있습니다.

```
OrgChart-Manager(employee){
    ///
}
```

1. 인사 관리 앱이 LUIS의 **빌드** 섹션에 있는지 확인합니다. 오른쪽 위의 메뉴 표시줄에서 **빌드**를 선택하여 이 섹션으로 변경할 수 있습니다. 

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

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "의도에 새 발언을 추가하는 LUIS 스크린샷")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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
이러한 의도에서 예제 발언의 수가 LUIS를 제대로 학습시키는 데 충분하지 않습니다. 실제 앱에서 각 의도에는 다양한 단어 선택 사항 및 발언 길이를 갖는 15개 이상의 발언이 필요합니다. 이러한 몇 가지 발언은 패턴을 강조 표시하기 위해 특별히 선택된 것입니다. 

## <a name="train-the-luis-app"></a>LUIS 앱 학습
새 의도 및 발언은 학습을 요구합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **학습** 단추를 선택합니다.

    ![학습 단추 이미지](./media/luis-tutorial-pattern/hr-train-button.png)

2. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 학습이 완료됩니다.

    ![성공 알림 표시줄 이미지](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기
챗봇 또는 다른 응용 프로그램에서 LUIS 예측을 얻으려면 앱을 게시해야 합니다. 

1. LUIS 웹 사이트의 오른쪽 위에서 **게시** 단추를 선택합니다. 

2. 프로덕션 슬롯과 **게시** 단추를 선택합니다.

    [ ![프로덕션 슬롯에 게시 단추가 강조 표시된 게시 페이지의 스크린샷](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. 웹 사이트의 위쪽에 성공이 확인된 녹색 상태 표시줄이 표시되면 게시가 완료됩니다.

## <a name="query-the-endpoint-with-a-different-utterance"></a>다른 발화를 사용하여 엔드포인트 쿼리
1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 끝점 URL이 표시된 다른 브라우저 창이 열립니다. 

    [ ![끝점 URL이 강조 표시된 게시 페이지의 스크린샷](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


2. 주소의 URL 끝으로 이동하고 `Who is the boss of Jill Jones?`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

    ```JSON
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

## <a name="add-the-template-utterances"></a>템플릿 발화 추가

1. 위쪽 메뉴에서 **빌드**를 선택합니다.

2. 왼쪽 탐색의 **앱 성능 개선** 아래에서 왼쪽 탐색의 **패턴**을 선택합니다.

3. **OrgChart-Manager** 의도를 선택하고 다음 템플릿 발언을 한 번에 하나씩 입력하고 각 템플릿 발언을 입력한 후에는 Enter 키를 누릅니다.

    |템플릿 발언|
    |:--|
    |Who is {Employee} the subordinate of[?]|
    |Who does {Employee} report to[?]|
    |Who is {Employee}['s] manager[?]|
    |Who does {Employee} directly report to[?]|
    |Who is {Employee}['s] supervisor[?]|
    |Who is the boss of {Employee}[?]|

    `{Employee}` 구문은 템플릿 발화 내의 엔터티 위치 및 엔터티 형식을 표시합니다. 
    
    역할이 있는 엔터티는 역할 이름을 포함하는 구문을 사용하며, [역할에 대한 별도 자습서](luis-tutorial-pattern-roles.md)에서 설명됩니다. 

    선택적 구문 `[]`은 선택적인 단어 또는 문장 부호에 표시를 합니다. LUIS는 대괄호로 묶인 선택적 텍스트는 무시하고 발언을 일치시킵니다.

    템플릿 발언을 입력할 때 왼쪽 중괄호 `{`를 입력하면 LUIS는 엔터티를 채우는 데 도움을 줍니다.

    [ ![의도의 템플릿 발언 입력 스크린샷](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. **OrgChart-Reports** 의도를 선택하고 다음 템플릿 발언을 한 번에 하나씩 입력하고 각 템플릿 발언을 입력한 후에는 Enter 키를 누릅니다.

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

2. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 끝점 URL이 표시된 다른 브라우저 창이 열립니다. 

3. 주소의 URL 끝으로 이동하고 `Who is the boss of Jill Jones?`를 발언으로 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

    ```JSON
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

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 LUIS 앱을 삭제합니다. 이렇게 하려면 앱 목록에서 앱 이름 오른쪽에 있는 줄임표(***...***)를 선택하고 **삭제**를 선택합니다. **앱을 삭제하시겠습니까?** 팝업 대화 상자에서 **확인**을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [패턴과 함께 역할을 사용하는 방법 알아보기](luis-tutorial-pattern-roles.md)