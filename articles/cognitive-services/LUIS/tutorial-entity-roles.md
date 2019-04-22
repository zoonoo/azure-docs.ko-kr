---
title: 역할이 포함된 컨텍스트 데이터 - Language Understanding
titleSuffix: Azure Cognitive Services
description: 컨텍스트를 기반으로 관련 데이터를 찾습니다. 예를 들어 한 건물과 사무실에서 다른 건물과 사무실로 이사할 경우 출발지 및 목적지 위치는 서로 관련이 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: diberry
ms.openlocfilehash: 98c2ad15d836162608affbfbc89908a15e274cee
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528815"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>자습서: 발언에서 컨텍스트 관련 데이터 추출

이 자습서에서는 컨텍스트를 기반으로 관련 데이터 조각을 찾습니다. 한 도시에서 다른 도시로 가는 이사의 출발지 및 목적지 위치를 예로 들 수 있습니다. 두 데이터 조각이 모두 필요할 수 있으며, 서로 관련되어 있습니다.  

이전에는 이 자습서가 계층 구조 엔터티를 사용하여 작성되었습니다. 엔터티 역할을 사용하면 계층 구조 엔터티 유형이 필요 없습니다. 역할은 미리 작성된 엔터티 유형 또는 사용자 지정 엔터티 유형에 사용할 수 있으며, 두 예제 발화 및 패턴 모두에서 사용할 수 있습니다. 

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 새 앱 만들기
> * 의도 추가 
> * 역할을 사용하여 원래 위치 및 대상 위치 정보 가져오기
> * 학습
> * 게시
> * 엔드포인트에서 의도 및 엔터티 역할 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>관련 데이터

이 앱은 원래 도시에서 대상 도시로 이동할 직원의 위치를 확인합니다. 이 앱은 미리 작성된 GeographyV2 엔터티를 사용하여 도시 이름을 식별하고, 역할을 사용하여 발화 내부의 위치 유형(원래 위치 및 대상 위치)을 확인합니다.

추출할 엔터티 데이터가 다음과 같은 경우 역할을 사용해야 합니다.

* 발화의 컨텍스트에서 서로 관련되어 있습니다.
* 특정 단어를 사용하여 각 역할을 나타냅니다. 이러한 단어의 예로 에서/(으)로, 떠나/~을(를) 향해, ~에서 꺼내/방향으로 등이 있습니다.
* 두 역할이 같은 발화에 자주 사용되므로 LUIS가 이처럼 자주 사용되는 문맥적 사용 방법을 학습할 수 있습니다.
* 클라이언트 앱에서 정보 단위로 그룹화하고 처리되어야 합니다.

## <a name="create-a-new-app"></a>새 앱 만들기

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>도시 간에 직원을 이동하는 의도 만들기

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **새 의도 만들기**를 선택합니다. 

1. 팝업 대화 상자에서 `MoveEmployeeToCity`을 입력하고 **완료**를 선택합니다. 

    ![새 의도 만들기 대화 상자의 스크린샷](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. 의도에 발화 예제를 추가합니다.

    |예제 발화|
    |--|
    |John W. Smith를 시애틀에서 올랜도로 이동|
    |Jill Jones를 시애틀에서 카이로로 이동|
    |John Jackson을 탬파에서 이동하여 애틀랜타에 배치 |
    |Debra Doughtery를 시카고에서 털사로 이동|
    |Jill Jones를 카이로에서 탬파로 이동|
    |Alice Anderson을 레드몬드에서 오클랜드로 이동|
    |Carl Chamerlin을 샌프란시스코에서 레드먼드로 이동|
    |Steve Standish를 샌디에이고에서 벨뷰로 이동 |
    |Tanner Thompson을 캔자스 시티에서 시카고로 이동|

    [![MoveEmployee 의도에 새 발화가 있는 LUIS의 스크린샷](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>미리 작성된 엔터티 geographyV2 추가

미리 작성된 엔터티 geographyV2는 도시 이름을 비롯한 위치 정보를 추출합니다. 발화에 도시 이름이 두 개 있고 두 도시가 다른 컨텍스트에서 서로 관련되어 있으므로, 역할을 사용하여 해당 컨텍스트를 추출합니다.

1. 왼쪽 탐색 영역에서 **엔터티**를 선택합니다.

1. **미리 작성된 엔터티 추가**를 선택한 다음, 검색 창에서 `geo`를 선택하여 미리 작성된 엔터티를 필터링합니다. 

    ![미리 작성된 geographyV2 엔터티를 앱에 추가](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. 확인란을 선택하고 **완료**를 선택합니다.
1. **엔터티** 목록에서 **geographyV2**를 선택하여 새 엔터티를 엽니다. 
1. `Origin` 및 `Destination` 역할을 추가합니다. 

    ![미리 작성된 엔터티에 역할 추가](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. 왼쪽 탐색 영역에서 **의도**를 선택한 다음, **MoveEmployeeToCity** 의도를 선택합니다. 도시 이름 레이블이 미리 작성된 엔터티 **geogrpahyV2**로 지정되었습니다.
1. 목록의 첫 번째 벌화에서 원래 위치를 선택합니다. 드롭다운 메뉴가 나타납니다. 목록에서 **geographyV2**를 선택한 다음, 메뉴를 따라 **원래 위치**를 선택합니다.

    [![도시를 원래 위치로 표시하는 스크린샷](media/tutorial-entity-roles/tag-origin-city-with-role.png "도시를 원래 위치로 표시하는 스크린샷")](media/tutorial-entity-roles/tag-origin-city-with-role.png#lightbox)

1. 이전 단계의 방법을 사용하여 모든 발화에 있는 위치의 모든 역할을 표시합니다. 

    [![발화에서 레이블이 지정된 위치 엔터티의 스크린샷](media/tutorial-entity-roles/all-locations-marked-with-roles.png "발화에서 레이블이 지정된 위치 엔터티의 스크린샷")](media/tutorial-entity-roles/all-locations-marked-with-roles.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>None 의도에 예제 발언 추가 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>의도에 대한 변경 내용을 테스트할 수 있도록 앱 학습시키기 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>학습을 거친 모델이 엔드포인트에서 쿼리할 수 있도록 앱 게시

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
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    올바른 의도가 예측되고 엔터티 배열은 해당 **entities** 속성에 원래 위치 및 대상 위치 역할이 모두 있습니다.
    
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

* [엔터티 개념](luis-concept-entity-types.md)
* [역할 개념](luis-concept-roles.md)
* [미리 작성된 엔터티 목록](luis-reference-prebuilt-entities.md)
* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)
* [역할](luis-concept-roles.md)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새 의도를 만들고, 컨텍스트에 따라 학습된 원본 및 대상 위치 데이터에 대한 발언 예시를 추가했습니다. 앱이 학습되고 게시되면 클라이언트 응용 프로그램이 해당 정보를 사용하여 관련 정보가 포함된 이동 티켓을 만들 수 있습니다.

> [!div class="nextstepaction"] 
> [복합 엔터티를 추가하는 방법 알아보기](luis-tutorial-composite-entity.md) 
