---
title: 계층적 엔터티
titleSuffix: Azure Cognitive Services
description: 컨텍스트를 기반으로 관련 데이터 조각을 찾습니다. 예를 들어 한 건물과 사무실에서 다른 건물과 사무실로 이사할 경우 출발지 및 목적지 위치는 서로 관련이 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753696"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>자습서: 발언에서 컨텍스트 관련 데이터 추출

이 자습서에서는 컨텍스트를 기반으로 관련 데이터 조각을 찾습니다. 한 도시에서 다른 도시로 가는 이사의 출발지 및 목적지 위치를 예로 들 수 있습니다. 두 데이터 조각이 모두 필요할 수 있으며, 서로 관련되어 있습니다.  

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 새 앱 만들기
> * 의도 추가 
> * 원본 및 대상 자식이 있는 위치 계층적 엔터티 추가
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>계층적 데이터

이 앱은 원래 도시에서 대상 도시로 이동할 직원의 위치를 확인합니다. 계층적 엔터티를 사용하여 발언 내의 위치를 확인합니다. 

계층적 엔터티는 이 형식의 데이터에 적합한데, 그 이유는 두 데이터, 즉, 자식 위치가 다음과 같은 특징을 보이기 때문입니다.

* 단순 엔터티입니다.
* 발언의 컨텍스트에서 서로 관련되어 있습니다.
* 특정 단어를 사용하여 각 엔터티를 나타냅니다. 이러한 단어의 예로 에서/(으)로, 떠나/~을(를) 향해, ~에서 꺼내/방향으로 등이 있습니다.
* 두 자식 요소가 동일한 발언에 있는 경우가 많습니다. 
* 클라이언트 앱에서 정보 단위로 그룹화하고 처리되어야 합니다.

## <a name="create-a-new-app"></a>새 앱 만들기

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>도시 간에 직원을 이동하는 의도 만들기

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **새 의도 만들기**를 선택합니다. 

1. 팝업 대화 상자에서 `MoveEmployeeToCity`을 입력하고 **완료**를 선택합니다. 

    ![새 의도 만들기 대화 상자의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |John W. Smith를 시애틀에서 댈러스로 이동|
    |Jill Jones를 시애틀에서 카이로로 이동|
    |John Jackson을 탬파에서 이동하여 애틀랜타에 배치 |
    |Debra Doughtery를 댈러스에서 털사로 이동|
    |Jill Jones를 카이로에서 탬파로 이동|
    |Alice Anderson을 레드몬드에서 오클랜드로 이동|
    |Carl Chamerlin을 샌프란시스코에서 레드먼드로 이동|
    |Steve Standish를 샌디에이고에서 벨뷰로 이동 |
    |Tanner Thompson을 캔자스 시티에서 시카고로 이동|

    [ ![MoveEmployee 의도에 새 발언이 있는 LUIS의 스크린샷](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>위치 엔터티 만들기
LUIS는 발언의 원래 위치와 대상 위치에 레이블을 지정하여 위치를 이해해야 합니다. 토큰(원시) 보기의 발언을 살펴보아야 하는 경우 **엔터티 보기**라는 레이블이 붙은 발언 위에 있는 표시줄에서 토글을 선택합니다. 스위치를 토글하면 컨트롤에 **토큰 보기**라는 레이블이 지정됩니다.

다음 발화를 살펴보겠습니다.

```json
move John W. Smith leaving Seattle headed to Dallas
```

발언에는 두 위치 `Seattle` 및 `Dallas`이 있습니다. 둘 다 계층적 엔터티의 자식 요소인 `Location`로 그룹화됩니다. 두 데이터 모두 발언에서 추출하여 클라이언트 애플리케이션의 요청을 완료해야 하고 서로 관련되어 있기 때문입니다. 
 
계층적 엔터티의 자식(원본 또는 대상)이 하나만 있는 경우에도 추출됩니다. 하나 또는 일부를 추출하기 위해 모든 자식을 검색할 필요가 없습니다. 

1. `move John W. Smith leaving Seattle headed to Dallas` 발화에서 `Seattle`이라는 단어를 선택합니다. 텍스트 상자가 맨 위에 있는 드롭다운 메뉴가 표시됩니다. 텍스트 상자에서 `Location`이라는 엔터티 이름을 입력한 다음, 드롭다운 메뉴에서 **새 엔터티 만들기**를 선택합니다. 

    [![의도 페이지에서 새 엔터티 만들기의 스크린샷](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "의도 페이지에서 새 엔터티 만들기의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. 팝업 창에서 자식 엔터티로 `Origin` 및 `Destination`이 있는 **계층적** 엔터티 형식을 선택합니다. **완료**를 선택합니다.

    ![새 위치 엔터티에 대한 엔터티 만들기 팝업 대화 상자의 스크린샷](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "새 위치 엔터티에 대한 엔터티 만들기 팝업 대화 상자의 스크린샷")

1. LUIS에서 용어가 원본, 대상 또는 둘 다인지 인식하지 못하므로 `Seattle`에 대한 레이블은 `Location`으로 표시됩니다. `Seattle`을 선택하고, **위치**를 선택한 다음, 오른쪽 메뉴에 따라 `Origin`을 선택합니다.

    [![위치 엔터티 자식을 변경하는 엔터티 레이블 지정 팝업 대화 상자의 스크린샷](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "위치 엔터티 자식을 변경하는 엔터티 레이블 지정 팝업 대화 상자의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. 나머지 발언의 다른 위치에 레이블을 지정합니다. 모든 위치가 표시되면 발언이 패턴처럼 보이기 시작합니다. 

    [![발화에서 레이블이 지정된 위치 엔터티의 스크린샷](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "발화에서 레이블이 지정된 위치 엔터티의 스크린샷")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    빨간색 밑줄은 LUIS가 엔터티에 대해 확신할 수 없다는 의미입니다. 학습을 통해 해결할 수 있습니다. 

## <a name="add-example-utterances-to-the-none-intent"></a>None 의도에 예제 발언 추가 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>의도에 대한 변경 내용을 테스트할 수 있도록 앱 학습시키기 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>학습된 모델을 엔드포인트에서 쿼리할 수 있도록 앱 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. 주소 표시줄의 URL 끝으로 이동하여 `Please move Carl Chamerlin from Tampa to Portland`를 입력합니다. 마지막 쿼리 문자열 매개 변수는 발언 **쿼리**를 나타내는 `q`입니다. 이 발화는 레이블이 있는 발화와 같지 않으므로 좋은 테스트이므로 추출된 계층적 엔터티와 함께 `MoveEmployee` 의도를 반환해야 합니다.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    올바른 의도가 예측되고 엔터티 배열은 해당 **entities** 속성에 원본 값과 대상 값이 모두 있습니다.
    
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* [계층적 엔터티](luis-concept-entity-types.md) 개념 정보
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)
* [역할 및 계층 구조 엔터티](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [패턴을 사용하여 예측 개선](luis-concept-patterns.md)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새 의도를 만들고, 컨텍스트에 따라 학습된 원본 및 대상 위치 데이터에 대한 발언 예시를 추가했습니다. 앱이 학습되고 게시되면 클라이언트 응용 프로그램이 해당 정보를 사용하여 관련 정보가 포함된 이동 티켓을 만들 수 있습니다.

> [!div class="nextstepaction"] 
> [복합 엔터티를 추가하는 방법 알아보기](luis-tutorial-composite-entity.md) 
