---
title: '빠른 시작: 앱 만들기 - LUIS'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 미리 빌드된 도메인 `HomeAutomation`을 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만드는 방법을 보여줍니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 마치면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 302321a36a6ce7526ad5e3144f87b88edbfaaec7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393687"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>빠른 시작: 미리 작성된 Home automation 앱 사용

이 빠른 시작에서는 미리 작성된 도메인 `HomeAutomation`를 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 응용 프로그램을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 마치면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>새 앱 만들기
애플리케이션은 **내 앱**에서 만들고 관리할 수 있습니다.

1. LUIS 포털의 내 앱 목록에서 **+ 만들기**를 선택합니다.

    ![LUIS 포털의 내 앱 목록에서 '+ 만들기'를 선택합니다.](./media/create-app-in-portal.png)

1. 대화 상자에서 애플리케이션 이름을 `Home Automation`으로 지정한 후 **완료**를 선택합니다. LUIS가 앱을 만듭니다. 설명은 선택 사항이며 작성 또는 예측에 사용되지 않습니다. LUIS 앱을 만들 때 예측 리소스도 선택 사항입니다. 앱을 프로덕션에 게시할 때 앱이 많은 요청을 처리할 수 있도록 예측 리소스를 할당해야 합니다.

    ![대화 상자에서 애플리케이션 이름을 'Home Automation'으로 지정합니다.](./media/create-new-app-details.png)

    >[!NOTE]
    >애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다.

## <a name="add-prebuilt-domain"></a>미리 빌드된 도메인 추가

**미리 빌드된 도메인**을 선택한 후 **HomeAutomation**을 검색합니다. HomeAutomation 카드에서 **도메인 추가**를 선택합니다.

!['미리 빌드된 도메인'을 선택한 후 'HomeAutomation'을 검색합니다. HomeAutomation 카드에서 '도메인 추가'를 선택합니다.](media/luis-quickstart-new-app/home-automation.png)

도메인 추가에 성공하면 미리 작성된 도메인 상자에 **도메인 제거** 단추가 표시됩니다.

## <a name="intents-and-entities"></a>의도 및 엔터티

**의도**를 선택하여 HomeAutomation 도메인 의도를 검토합니다. 미리 빌드된 도메인 의도에 샘플 발언이 포함되어 있습니다.

![HomeAutomation 의도 목록의 스크린샷](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation 의도 목록의 스크린샷")

> [!NOTE]
> **없음**은 모든 LUIS 앱에 제공되는 의도입니다. 이것은 앱에 제공되는 기능과 일치하지 않는 발언을 처리하는 데 사용됩니다.

**HomeAutomation.TurnOff** 의도를 선택합니다. 엔터티를 사용하여 레이블이 지정된 발언 목록이 의도에 포함된 것을 볼 수 있습니다.

[![HomeAutomation.TurnOff 의도의 스크린샷](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 의도의 스크린샷")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>앱 테스트
앱을 학습시킨 후에는 테스트할 수 있습니다. **테스트**를 선택합니다. 대화형 테스트 창에 테스트 발언(예: `Turn off the lights`)을 입력하고 Enter 키를 누릅니다.

```
Turn off the lights
```

점수가 가장 높은 의도가 각 테스트 발언에 대한 의도와 일치하는지 확인합니다.

이 예제에서 `Turn off the lights`는 **HomeAutomation.TurnOff**의 최고 득점 의도로 올바르게 확인되었습니다.

![발언이 강조 표시된 테스트 패널의 스크린샷](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

**검사**를 선택하여 예측에 대한 자세한 정보를 검토합니다.

![검사 정보가 포함된 테스트 패널의 스크린샷](media/luis-quickstart-new-app/test.png)

**테스트**를 다시 선택하여 테스트 창을 접습니다.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 예측 엔드포인트 쿼리

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

1. 브라우저 주소 표시줄에서 쿼리 문자열에 대해 다음 이름 및 값 표시줄이 URL에 있는지 확인합니다. 쿼리 문자열에 없으면 추가하세요.

    |이름/값 쌍|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

1. 브라우저 주소 표시줄에서 URL 끝으로 이동하고 `turn off the living room light`쿼리_값에 대해_를 입력한 후 Enter 키를 누릅니다.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.984315455
                },
                "HomeAutomation.QueryState": {
                    "score": 0.009912962
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.00626645749
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.00572059769
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.00379381469
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00366983772
                },
                "None": {
                    "score": 0.000623856
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.907323956,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
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

    [V3 예측 엔드포인트](luis-migration-api-v3.md)에 대해 자세히 알아봅니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

다음 코드에서 엔드포인트를 호출할 수 있습니다.

> [!div class="nextstepaction"]
> [코드를 사용하여 LUIS 엔드포인트 호출](luis-get-started-cs-get-intent.md)
