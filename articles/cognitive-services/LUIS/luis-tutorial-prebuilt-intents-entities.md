---
title: 미리 작성된 의도 및 엔터티
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 미리 빌드된 의도 및 엔터티를 앱에 추가하여 의도 예측 및 데이터 추출을 빠르게 수행합니다. 미리 빌드된 엔터티를 사용하여 발화에 레이블을 지정할 필요가 없습니다. 엔터티는 자동으로 검색됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: d8569bbc2d1befdd2b6fb9c8783dab206e19c7c9
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211970"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>자습서: 일반적인 의도 및 엔터티 식별

이 자습서에서는 미리 빌드된 의도 및 엔터티를 인사 관리 자습서 앱에 추가하여 의도 예측 및 데이터 추출을 빠르게 수행합니다. 엔터티가 자동으로 검색되기 때문에 미리 빌드된 엔터티를 사용하여 발언을 표시할 필요가 없습니다.

미리 빌드된 모델(도메인, 의도 및 엔터티)을 사용하면 모델을 신속하게 빌드할 수 있습니다.

**이 자습서에서 학습할 내용은 다음과 같습니다.**

> [!div class="checklist"]
> * 새 앱 만들기
> * 미리 빌드된 의도 추가 
> * 미리 빌드된 엔터티 추가 
> * 학습 
> * 게시 
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>새 앱 만들기

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>일반적인 사용자 의도에 도움이 되도록 미리 빌드된 의도 추가

LUIS는 일반적인 사용자 의도에 도움이 되도록 여러 가지 미리 빌드된 의도를 제공합니다.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. **Add prebuilt domain intent**(미리 빌드된 도메인 의도 추가)를 선택합니다. 

1. `Utilities`를 검색합니다. 

    [ ![검색 상자에 Utilities가 입력된 미리 빌드된 의도 대화 상자의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. 다음 의도를 선택하고 **완료**를 선택합니다. 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    이러한 의도는 대화에서 사용자가 어디에 있고 무엇을 하려 하는지 확인하는 데 유용합니다. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>일반적인 데이터 형식 추출에 도움이 되도록 미리 빌드된 엔터티 추가

LUIS는 일반적인 데이터 추출을 위해 여러 가지 미리 빌드된 엔터티를 제공합니다. 

1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

1. **미리 빌드된 엔터티 추가** 단추를 선택합니다.

1. 미리 빌드된 엔터티 목록에서 다음 엔터티를 선택하고 **완료**를 선택합니다.

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    이러한 엔터티는 클라이언트 애플리케이션 이름 및 위치 인식 기능을 추가하는 데 도움이 됩니다.

## <a name="add-example-utterances-to-the-none-intent"></a>None 의도에 예제 발언 추가 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>의도에 대한 변경 내용을 테스트할 수 있도록 앱 학습시키기 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>학습을 거친 모델이 엔드포인트에서 쿼리할 수 있도록 앱 게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 예측 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. 브라우저 주소 표시줄에서 URL의 끝으로 이동하여 `I want to cancel my trip to Seattle to see Bob Smith`을 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`로 발화 **쿼리**입니다. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    결과에서는 신뢰도 80%인 Utilities.Cancel 의도를 예측하고 도시 및 사람 이름 데이터를 추출했습니다. 


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>관련 정보

미리 빌드된 모델에 대한 자세한 정보:

* [미리 빌드된 도메인](luis-reference-prebuilt-domains.md): 전체 LUIS 앱 제작 시간을 단축하는 공통 도메인입니다.
* 미리 빌드된 의도: 공통 도메인의 개별 의도입니다. 전체 도메인을 추가하는 대신 의도를 개별적으로 추가할 수 있습니다.
* [미리 빌드된 엔터티](luis-prebuilt-entities.md): 대부분의 LUIS 앱에 유용한 일반 데이터 형식입니다.

LUIS 앱 사용에 대한 자세한 정보:

* [학습 방법](luis-how-to-train.md)
* [게시 방법](luis-how-to-publish-app.md)
* [LUIS 포털에서 테스트하는 방법](luis-interactive-test.md)

## <a name="next-steps"></a>다음 단계

미리 빌드된 의도 및 엔터티를 추가하여 클라이언트 애플리케이션은 일반적인 사용자 의도를 결정하고 일반적인 데이터 형식을 추출할 수 있습니다.  

> [!div class="nextstepaction"]
> [앱에 정규식 엔터티 추가](luis-quickstart-intents-regex-entity.md)

