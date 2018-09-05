---
title: 패턴 역할을 사용하여 LUIS 예측을 개선하는 방법의 자습서 - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: 이 자습서에서는 문맥상 관련이 있는 엔터티에 대해 패턴 역할을 사용하여 LUIS 예측을 개선합니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 633bf16790437ba83e9a2a99f33a23fef9255364
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43090529"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>자습서: 패턴 역할을 사용하여 앱 개선

이 자습서에서는 역할과 패턴이 결합된 단순 엔터티를 사용하여 의도 및 엔터티 예측을 높입니다.  패턴을 사용할 경우 의도에 더 적은 수의 예제 발언이 필요합니다.

> [!div class="checklist"]
* 패턴 역할 이해
* 역할과 함께 단순 엔터티 사용 
* 역할과 함께 단순 엔터티를 사용하여 발언에 대한 패턴 만들기
* 패턴 예측 개선 사항을 확인하는 방법

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>시작하기 전에
[패턴](luis-tutorial-pattern.md) 자습서의 Human Resources 앱이 없는 경우 JSON을 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트의 새 앱으로 [가져옵니다](luis-how-to-start-new-app.md#import-new-app). 가져올 앱은 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json) GitHub 리포지토리에 있습니다.

원래의 인사 관리 앱을 유지하려면 [설정](luis-how-to-manage-versions.md#clone-a-version) 페이지에서 버전을 복제하고 해당 이름을 `roles`로 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 

## <a name="the-purpose-of-roles"></a>역할의 용도
역할의 용도는 발언에서 문맥상 관련이 있는 엔터티를 추출하는 것입니다. 발언 `Move new employee Robert Williams from Sacramento and San Francisco`에서 출발지 도시 및 목적지 도시 값은 서로 관련이 있으며 일반적인 언어를 사용해서 각 위치를 나타냅니다. 

패턴을 사용할 경우 패턴의 모든 엔터티가 _먼저_ 검색되어야 패턴이 발언과 일치됩니다. 

패턴을 만들 경우 첫 번째 단계는 패턴의 의도를 선택하는 것입니다. 의도를 선택하면 패턴이 일치할 경우 올바른 의도가 항상 높은 점수로 반환됩니다(일반적으로 99~100%). 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>역할을 사용하여 계층 구조 엔터티를 단순 엔터티와 비교

[계층 구조 자습서](luis-quickstart-intent-and-hier-entity.md)에서 **MoveEmployee** 의도는 기존 직원을 한 건물에서 다른 건물의 사무실로 이전할 시기를 감지했습니다. 예제 발언에는 출발지 및 목적지 위치가 있었지만 역할은 사용하지 않았습니다. 대신, 출발지 및 목적지는 계층 구조 엔터티의 자식이었습니다. 

이 자습서에서 Human Resources 앱은 새 직원을 한 도시에서 다른 도시로 이전하는 것에 대한 발언을 검색합니다. 이러한 두 가지 유형의 발언은 유사하지만 다른 LUIS 기능을 사용하여 해결됩니다.

|자습서|예제 발화|출발지 및 목적지 위치|
|--|--|--|
|[계층 구조(역할 없음)](luis-quickstart-intent-and-hier-entity.md)|mv Jill Jones from **a-2349** to **b-1298**|a-2349, b-1298|
|이 자습서(역할 있음)|Move Billy Patterson from **Yuma** to **Denver**.|Yuma, Denver|

패턴에서는 계층적 부모만 사용되므로 패턴에서 계층 구조 엔터티를 사용할 수 없습니다. 출발지 및 목적지의 명명된 위치를 반환하기 위해서는 패턴을 사용해야 합니다.

### <a name="simple-entity-for-new-employee-name"></a>새 직원 이름에 대한 단순 엔터티
새 직원의 이름인 Billy Patterson은 아직 **Employee** 목록 엔터티에 속하지 않습니다. 이름을 외부 시스템으로 전송하여 회사 자격 증명을 만들기 위해 먼저 새 직원 이름이 추출됩니다. 회사 자격 증명이 만들어진 후에는 직원 자격 증명이 **Employee** 목록 엔터티에 추가됩니다.

**Employee** 목록은 [목록 자습서](luis-quickstart-intent-and-list-entity.md)에서 만들어졌습니다.

**NewEmployee** 엔터티는 역할이 없는 단순 엔터티입니다. 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>재배치 도시 역할이 있는 단순 엔터티
새 직원과 가족은 도시에서 가상의 회사가 있는 도시로 이사해야 합니다. 새 직원는 어떤 도시에서도 올 수 있으므로 위치를 검색해야 합니다. 목록의 도시만 추출될 수 있으므로 목록 엔터티 같은 설정된 목록은 작동하지 않습니다.

출발지 및 목적지 도시와 연결된 역할 이름은 모든 엔터티에서 고유해야 합니다. 역할을 고유하게 유지하는 쉬운 방법은 명명 전략을 통해 포함하는 엔터티와 연결하는 것입니다. **NewEmployeeRelocation** 엔터티는 두 가지 역할, **NewEmployeeReloOrigin** 및 **NewEmployeeReloDestination**을 갖는 단순 엔터티입니다.

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>단순 엔터티는 검색되려면 충분한 예제가 필요합니다.
예제 발언 `Move new employee Robert Williams from Sacramento and San Francisco`에는 기계 학습된 엔터티만 있으므로 엔터티가 검색되기 위해 의도에 충분한 예제 발언을 제공하는 것이 중요합니다.  

**패턴을 사용하면 몇 가지 예제 발언을 제공할 수 있지만 엔터티가 검색되지 않으면 패턴이 일치되지 않습니다.**

이것은 도시와 같은 이름이므로 단순 엔터티를 검색하는 데 문제가 있는 경우 비슷한 값의 구 목록을 추가하는 것이 좋습니다. 이렇게 하면 LUIS에 해당 형식의 단어 또는 구에 대한 추가 신호를 제공하여 도시 이름 검색에 도움을 줍니다. 구 목록은 패턴이 일치하는 데 필요한 엔터티 검색에 도움을 주는 방식으로만 패턴을 지원합니다. 

## <a name="create-new-entities"></a>새 엔터티 만들기
1. 위쪽 메뉴에서 **빌드**를 선택합니다.

2. 왼쪽 탐색 영역에서 **엔터티**를 선택합니다. 

3. **새 엔터티 만들기**를 선택합니다.

4. 팝업 창에서 `NewEmployee`을 **단순** 엔터티로 입력합니다.

5. **새 엔터티 만들기**를 선택합니다.

6. 팝업 창에서 `NewEmployeeRelocation`을 **단순** 엔터티로 입력합니다.

7. 엔터티 목록에서 **NewEmployeeRelocation**을 선택합니다. 

8. 첫 번째 역할로 `NewEmployeeReloOrigin`을 입력하고 Enter 키를 누릅니다.

9. 두 번째 역할로 `NewEmployeeReloDestination`을 입력하고 Enter 키를 누릅니다.

## <a name="create-new-intent"></a>새 의도 만들기
시작하기 전에 미리 빌드된 keyPhrase 엔터티를 제거한 후, 이 섹션의 단계를 완료한 후에 다시 추가하면 이러한 단계에서 엔터티에 레이블을 쉽게 지정할 수 있습니다. 

1. 왼쪽 탐색에서 **의도**를 선택합니다.

2. **새 의도 만들기**를 선택합니다. 

3. 팝업 대화 상자에서 의도 이름으로 `NewEmployeeRelocationProcess`를 입력합니다.

4. 다음 예제 발언을 입력하고 새 엔터티에 레이블을 지정합니다. 엔터티 및 역할 값은 굵게 표시됩니다. 텍스트에 레이블을 지정하는 것이 더 쉬울 경우 **토큰 보기**로 전환해야 합니다. 

    의도에 레이블을 지정할 때는 엔터티의 역할을 지정하지 마세요. 나중에 패턴을 만들 때 지정하면 됩니다. 

    |발화|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Move **Bob Jones** from **Seattle** to **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Move **Dave C. Cooper** from **Redmond** to **New York City**|Dave C. Cooper|Redmond, New York City|
    |Move **Jim Paul Smith** from **Toronto** to **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Move **J. Benson** from **Boston** to **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Move **Travis "Trav" Hinton** from **Castelo Branco** to **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Move **Trevor Nottington III** from **Aranda de Duero** to **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Move **Dr. Greg Williams** from **Orlando** to **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Move **Robert "Bobby" Gregson** from **Kansas City** to **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Move **Patti Owens** from **Bellevue** to **Rockford**|Patti Owens|Bellevue, Rockford|
    |Move **Janet Bartlet** from **Tuscan** to **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    직원 이름은 다양한 접두사, 단어 수, 구문 및 접미사를 갖습니다. 이것은 LUIS에서 새 직원 이름의 변형을 이해하는 데 중요합니다. 도시 이름 또한 다양한 단어 수 및 구문을 갖습니다. 이러한 다양성은 이러한 엔터티가 사용자 발언에 나타나는 방식을 LUIS에 학습시키는 데 중요합니다. 
    
    두 엔터티가 동일한 단어 수를 갖고 다른 변형은 없다면 이 엔터티가 해당 단어 수만 갖고 다른 변형이 없는 것으로만 LUIS를 학습시키게 됩니다. LUIS는 표시되지 않기 때문에 보다 광범위한 변형 집합을 올바르게 예측할 수 없습니다. 

    keyPhrase 엔터티를 제거한 경우 지금 앱에 다시 추가합니다.

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-without-pattern"></a>패턴 없이 끝점 쿼리

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. 주소의 URL 끝으로 이동하고 `Move Wayne Berry from Miami to Mount Vernon`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

의도 예측 점수는 약 50%입니다. 클라이언트 응용 프로그램이 더 높은 점수를 원할 경우 수정해야 합니다. 두 경우 모두 엔터티는 예측되지 않았습니다.

패턴은 예측 점수에 도움이 되지만, 패턴이 발언을 일치시키려면 먼저 엔터티가 올바르게 예측되어야 합니다. 

## <a name="add-a-pattern-that-uses-roles"></a>역할을 사용하는 패턴 추가
1. 위쪽 탐색 영역에서 **빌드**를 선택합니다.

2. 왼쪽 탐색 영역에서 **패턴**을 선택합니다.

3. **의도 선택** 드롭다운 목록에서 **NewEmployeeRelocationProcess**를 선택합니다. 

4. `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]` 패턴을 입력합니다.

    끝점을 학습, 게시 및 쿼리하는 경우 엔터티를 찾을 수 없어서 실망할 수 있습니다. 이 경우 패턴이 일치되지 않으므로 예측이 개선되지 않았을 것입니다. 이러한 상황은 엔터티에 레이블이 지정된 충분한 수의 예제 발언이 없을 때 나타납니다. 더 많은 예제를 추가하는 대신, 구 목록을 추가하여 이 문제를 해결하세요.

## <a name="create-a-phrase-list-for-cities"></a>도시에 대한 구 목록 만들기
사람의 이름처럼 도시는 단어 및 문장 부호의 어떤 혼합으로도 이루어질 수 있으므로 까다로운 항목입니다. 하지만 국가/지역 및 전 세계의 도시가 알려져 있으므로 LUIS에는 학습을 시작할 도시의 구 목록이 필요합니다. 

1. 왼쪽 메뉴의 **앱 성능 개선** 섹션에서 **구 목록**을 선택합니다. 

2. 목록 이름을 `Cities`로 지정하고 목록에 대해 다음 `values`를 추가합니다.

    |구 목록 값|
    |--|
    |시애틀|
    |샌디에이고|
    |New York City|
    |로스앤젤레스|
    |Portland|
    |Philadephia|
    |마이애미|
    |댈러스|

    전 세계의 모든 도시 또는 국가/지역의 모든 도시를 추가하지는 마세요. LUIS는 목록에 있는 도시를 일반화할 수 있어야 합니다. 

    **서로 교환 가능한 값**을 선택해야 합니다. 이 설정에서는 목록의 단어가 동의어로 처리됩니다. 패턴에서 단어가 취급되는 방식과 같습니다.

    이 자습서 시리즈가 구 목록을 [마지막으로 만든 시기](luis-quickstart-primary-and-secondary-data.md)에도 단순 엔터티의 엔터티 검색이 급증했습니다.  

3. 앱을 학습하고 게시합니다.

## <a name="query-endpoint-for-pattern"></a>패턴에서 끝점 쿼리
1. **게시** 페이지의 아래쪽에서 **엔드포인트** 링크를 선택합니다. 그러면 주소 표시줄에 엔드포인트 URL이 있는 다른 브라우저 창이 열립니다. 

2. 주소의 URL 끝으로 이동하고 `Move wayne berry from miami to mount vernon`을 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

이제 의도 점수는 훨씬 더 높고 역할 이름은 엔터티 응답의 일부입니다.

## <a name="clean-up-resources"></a>리소스 정리

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 앱에 대한 모범 사례 알아보기](luis-concept-best-practices.md)
