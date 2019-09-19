---
title: '빠른 시작: 앱 만들기 - LUIS'
titleSuffix: Azure Cognitive Services
description: 미리 빌드된 도메인 `HomeAutomation`을 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 마치면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 1704b62cae6375d376fc43fb7a2940cd9c717072
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382515"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>빠른 시작: 미리 빌드된 홈 자동화 앱 사용

이 빠른 시작에서는 미리 작성된 도메인 `HomeAutomation`를 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 응용 프로그램을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 마치면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [https://www.luis.ai](https://www.luis.ai)의 LUIS 포털에 만들어진 무료 LUIS 계정이 필요합니다. 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-a-new-app"></a>새 앱 만들기
애플리케이션은 **내 앱**에서 만들고 관리할 수 있습니다. 

2. **새 앱 만들기**를 선택합니다.

    [![앱 목록 스크린샷](media/luis-quickstart-new-app/app-list.png "앱 목록 스크린샷")](media/luis-quickstart-new-app/app-list.png)

3. 대화 상자에서 애플리케이션 이름을 "Home Automation"으로 지정합니다.

    [![새 앱 만들기 팝업 대화 상자 스크린샷](media/luis-quickstart-new-app/create-new-app-dialog.png "새 앱 만들기 팝업 대화 상자 스크린샷")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. 애플리케이션 문화권을 선택합니다. Home Automation 앱의 경우 영어를 선택합니다. 그런 후 **완료**를 선택합니다. LUIS에서 Home Automation 앱이 만들어집니다. 

    >[!NOTE]
    >애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다. 

## <a name="add-prebuilt-domain"></a>미리 빌드된 도메인 추가

왼쪽 탐색 창에서 **미리 작성된 도메인**을 선택합니다. 그런 다음, "Home"을 검색합니다. **도메인 추가**를 선택합니다.

[![미리 빌드된 도메인 메뉴에서 호출된 홈 자동화 도메인의 스크린샷](media/luis-quickstart-new-app/home-automation.png "미리 빌드된 도메인 메뉴에서 호출된 홈 자동화 도메인의 스크린샷")](media/luis-quickstart-new-app/home-automation.png)

도메인 추가에 성공하면 미리 작성된 도메인 상자에 **도메인 제거** 단추가 표시됩니다.

[![제거 단추가 있는 홈 자동화 도메인의 스크린샷](media/luis-quickstart-new-app/remove-domain.png "제거 단추가 있는 홈 자동화 도메인의 스크린샷")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>의도 및 엔터티

왼쪽 탐색 창에서 **의도**를 선택하여 HomeAutomation 도메인 의도를 검토합니다. 의도마다 예제 발언이 있습니다.

![HomeAutomation 의도 목록의 스크린샷](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation 의도 목록의 스크린샷")]

> [!NOTE]
> **없음**은 모든 LUIS 앱에 제공되는 의도입니다. 이것은 앱에 제공되는 기능과 일치하지 않는 발언을 처리하는 데 사용됩니다. 

**HomeAutomation.TurnOff** 의도를 선택합니다. 엔터티를 사용하여 레이블이 지정된 발언 목록이 의도에 포함된 것을 볼 수 있습니다.

[![HomeAutomation.TurnOff 의도 스크린샷](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 의도 스크린샷")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>앱 테스트
앱을 학습시킨 후에는 테스트할 수 있습니다. 맨 위 탐색에서 **테스트**를 선택합니다. 대화형 테스트 창에 테스트 발언(예: "Turn off the lights")을 입력하고 Enter 키를 누릅니다. 

```
Turn off the lights
```

점수가 가장 높은 의도가 각 테스트 발언에 대한 의도와 일치하는지 확인합니다.

이 예제에서 `Turn off the lights`는 **HomeAutomation.TurnOff**의 최고 득점 의도로 올바르게 확인되었습니다.

[![발화가 강조 표시된 테스트 패널의 스크린샷](media/luis-quickstart-new-app/test.png "발화가 강조 표시된 테스트 패널의 스크린샷")](media/luis-quickstart-new-app/test.png)


**검사**를 선택하여 예측에 대한 자세한 정보를 검토합니다.

![발언이 강조 표시된 테스트 패널의 스크린샷](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

**테스트**를 다시 선택하여 테스트 창을 접습니다. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-v2-api-prediction-endpoint"></a>V2 API 예측 엔드포인트 쿼리

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. 주소의 URL 끝으로 이동하고 `turn off the living room light`를 입력한 다음, Enter 키를 누릅니다. 브라우저에서 HTTP 엔드포인트의 JSON 응답 **V2 API** 버전이 표시됩니다.

    ```json
    {
      "query": "turn off the living room light",
      "topScoringIntent": {
        "intent": "HomeAutomation.TurnOff",
        "score": 0.9753089
      },
      "intents": [
        {
          "intent": "HomeAutomation.TurnOff",
          "score": 0.9753089
        },
        {
          "intent": "HomeAutomation.QueryState",
          "score": 0.01027893
        },
        {
          "intent": "HomeAutomation.TurnUp",
          "score": 0.006881481
        },
        {
          "intent": "HomeAutomation.SetDevice",
          "score": 0.006786365
        },
        {
          "intent": "HomeAutomation.TurnDown",
          "score": 0.005145787
        },
        {
          "intent": "HomeAutomation.TurnOn",
          "score": 0.004114749
        },
        {
          "intent": "None",
          "score": 0.000598924
        }
      ],
      "entities": [
        {
          "entity": "living room",
          "type": "HomeAutomation.Location",
          "startIndex": 13,
          "endIndex": 23,
          "score": 0.94558233
        },
        {
          "entity": "living room light",
          "type": "HomeAutomation.DeviceName",
          "startIndex": 13,
          "endIndex": 29,
          "resolution": {
            "values": [
              "living room light"
            ]
          }
        },
        {
          "entity": "light",
          "type": "HomeAutomation.DeviceType",
          "startIndex": 25,
          "endIndex": 29,
          "resolution": {
            "values": [
              "light"
            ]
          }
        }
      ]
    }
    ```
    
## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 예측 엔드포인트 쿼리

[V3 API 쿼리](luis-migration-api-v3.md)의 경우, 브라우저에서 꺾쇠괄호 안의 값을 고유한 값으로 변경하여 GET 메서드 HTTPS 요청을 변경합니다. 

**GET 메서드를 사용한 V2 URL**:

https://\<region>.api.cognitive.microsoft.com/luis/**v2.0**/apps/\<appID>?verbose=true&subscription-key=\<YOUR_KEY>&**q=\<user-utterance-text>**

**GET 메서드를 사용한 V3 URL**:

https://\<region>.api.cognitive.microsoft.com/luis/**v3.0-preview**/apps/\<appID>/**slots**/**production**/**predict**?verbose=true&subscription-key=\<YOUR_KEY>&**query=\<user-utterance-text>**

브라우저에서 HTTP 엔드포인트의 JSON 응답 **V3 API 버전**이 표시됩니다.

```json
{
    "query": "turn off the lights",
    "prediction": {
        "normalizedQuery": "turn off the lights",
        "topIntent": "HomeAutomation.TurnOff",
        "intents": {
            "HomeAutomation.TurnOff": {
                "score": 0.99649024
            }
        },
        "entities": {
            "HomeAutomation.DeviceType": [
                [
                    "light"
                ]
            ],
            "$instance": {
                "HomeAutomation.DeviceType": [
                    {
                        "type": "HomeAutomation.DeviceType",
                        "text": "lights",
                        "startIndex": 13,
                        "length": 6,
                        "modelTypeId": 5,
                        "modelType": "List Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

다음 코드에서 엔드포인트를 호출할 수 있습니다.

> [!div class="nextstepaction"]
> [코드를 사용하여 LUIS 엔드포인트 호출](luis-get-started-cs-get-intent.md)
