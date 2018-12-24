---
title: 미리 작성된 의도 및 엔터티
titleSuffix: Azure Cognitive Services
description: 미리 빌드된 의도 및 엔터티를 인사 관리 자습서 앱에 추가하여 의도 예측 및 데이터 추출을 빠르게 수행합니다. 미리 빌드된 엔터티를 사용하여 발화에 레이블을 지정할 필요가 없습니다. 엔터티는 자동으로 검색됩니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b6fb603b84cdcf3cb0f75d0020fa2047a0a838d1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074080"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>자습서 2: 일반적인 의도 및 엔터티 식별
이 자습서에서는 인사 관리 앱을 수정합니다. 미리 빌드된 의도 및 엔터티를 인사 관리 자습서 앱에 추가하여 의도 예측 및 데이터 추출을 빠르게 수행합니다. 엔터티를 자동으로 검색하기 때문에 미리 빌드된 엔터티를 사용하여 발화의 레이블을 지정할 필요가 없습니다.

미리 빌드된 모델의 일반적인 주체 도메인 및 데이터 형식을 통해 모델을 신속하게 빌드할 뿐만 아니라 모델 모양의 예제를 제공하는 데 도움이 됩니다. 

**이 자습서에서는 다음 방법에 대해 알아봅니다.**

> [!div class="checklist"]
> * 기존 자습서 앱 사용
> * 미리 빌드된 의도 추가 
> * 미리 빌드된 엔터티 추가 
> * 학습 
> * 게시 
> * 엔드포인트에서 의도 및 엔터티 가져오기

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>기존 앱 사용
마지막 자습서에서 만든 **HumanResources**라는 앱을 사용하여 계속 진행합니다. 

이전 자습서의 HumanResources 앱이 없으면 다음 단계를 사용합니다.

1.  [앱 JSON 파일](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json)을 다운로드하고 저장합니다.

2. JSON을 새 앱으로 가져옵니다.

3. **관리** 섹션의 **버전** 탭에서 버전을 복제하고 `prebuilts`라는 이름을 지정합니다. 복제는 원래 버전에 영향을 주지 않고도 다양한 LUIS 기능을 사용할 수 있는 좋은 방법입니다. 버전 이름이 URL 경로의 일부로 사용되므로 이름에는 URL에 유효하지 않은 문자가 포함될 수 없습니다. 

## <a name="add-prebuilt-intents"></a>미리 빌드된 의도 추가
LUIS는 일반적인 사용자 의도에 도움이 되도록 여러 가지 미리 빌드된 의도를 제공합니다.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. **미리 빌드된 의도 추가**를 선택합니다. 

3. `Utilities`를 검색합니다. 

    [ ![검색 상자에 Utilities가 입력된 미리 빌드된 의도 대화 상자의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 다음 의도를 선택하고 **완료**를 선택합니다. 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>미리 빌드된 엔터티 추가
LUIS는 일반적인 데이터 추출을 위해 여러 가지 미리 빌드된 엔터티를 제공합니다. 

1. 왼쪽 탐색 메뉴에서 **엔터티**를 선택합니다.

2. **미리 빌드된 엔터티 관리** 단추를 선택합니다.

3. 미리 빌드된 엔터티 목록에서 **number** 및 **datetimeV2**를 선택하고 **완료**를 선택합니다.

    ![number가 선택된 미리 빌드된 엔터티 대화 상자의 스크린샷](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>게시

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>엔드포인트에서 의도 및 엔터티 가져오기

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. 브라우저 주소 표시줄에서 URL의 끝으로 이동하여 `I want to cancel on March 3`을 입력합니다. 마지막 쿼리 문자열 매개 변수는 `q`로 발화 **쿼리**입니다. 

    ```json
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    결과에서는 Utilities.Cancel 의도를 예측하고 March 3 날짜와 숫자 3을 추출했습니다. 

    3월 3일이 과거 또는 미래에 있을 때 발언이 언급되지 않았기 때문에 3월 3일에는 두 가지 값이 있습니다. 필요할 경우 가정하거나 설명을 요청하는 것은 클라이언트 응용 프로그램의 책임입니다. 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

미리 빌드된 의도 및 엔터티를 추가하여 클라이언트 응용 프로그램은 일반적인 사용자 의도를 결정하고 일반적인 데이터 형식을 추출할 수 있습니다. 

> [!div class="nextstepaction"]
> [앱에 정규식 엔터티 추가](luis-quickstart-intents-regex-entity.md)

