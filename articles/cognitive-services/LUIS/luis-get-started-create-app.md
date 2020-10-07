---
title: '빠른 시작: 앱 만들기 - LUIS'
description: 이 빠른 시작에서는 미리 빌드된 도메인 `HomeAutomation`을 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만드는 방법을 보여줍니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 완료하면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/05/2020
ms.openlocfilehash: 28bf79b61c0278a3f45820a23cd2c69f0b609700
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316491"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>빠른 시작: 미리 빌드된 홈 자동화 앱 사용

이 빠른 시작에서는 미리 빌드된 도메인 `HomeAutomation`를 사용하여 조명 및 어플라이언스를 켜고 끄는 LUIS 앱을 만듭니다. 미리 작성된 도메인에는 의도, 엔터티 및 예제 발언이 제공됩니다. 완료하면 클라우드에서 LUIS 엔드포인트를 실행하게 됩니다.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>새 앱 만들기
애플리케이션은 **내 앱**에서 만들고 관리할 수 있습니다.

1. 내 앱 목록에서 **+ 대화용 새 앱**을 선택한 다음, 옵션 목록에서 **+ 대화용 새 앱**을 다시 선택합니다.

1. 대화 상자에서 애플리케이션 이름을 `Home Automation`으로 지정합니다.
1. 문화권으로 **영어**를 선택합니다.
1. 선택적 설명을 입력합니다.
1. 리소스를 아직 만들지 않은 경우 예측 리소스를 선택하지 마세요. 앱의 예측 엔드포인트(스테이징 또는 프로덕션)를 사용하려면 예측 리소스를 할당해야 합니다.
1. **완료**를 선택합니다.

    LUIS가 앱을 만듭니다.

    ![대화 상자에서 애플리케이션 이름을 'Home Automation'으로 지정합니다.](./media/create-new-app-details.png)

    >[!NOTE]
    >애플리케이션을 만든 후에는 문화권을 변경할 수 없습니다.

## <a name="add-prebuilt-domain"></a>미리 빌드된 도메인 추가

1. 왼쪽 탐색 영역에서 **미리 빌드된 도메인**을 선택합니다.
1. **HomeAutomation**을 검색합니다.
1. HomeAutomation 카드에서 **도메인 추가**를 선택합니다.

    > [!div class="mx-imgBorder"]
    > !['미리 빌드된 도메인'을 선택한 다음, 'HomeAutomation'을 검색합니다. HomeAutomation 카드에서 '도메인 추가'를 선택합니다.](media/luis-quickstart-new-app/home-automation.png)

    도메인 추가에 성공하면 미리 작성된 도메인 상자에 **도메인 제거** 단추가 표시됩니다.

## <a name="intents-and-entities"></a>의도 및 엔터티

1. **의도**를 선택하여 HomeAutomation 도메인 의도를 검토합니다. 미리 빌드된 도메인 의도에는 예제 발화가 있습니다.

    > [!div class="mx-imgBorder"]
    > ![HomeAutomation 의도 목록의 스크린샷](media/luis-quickstart-new-app/home-automation-intents.png "HomeAutomation 의도 목록의 스크린샷")

    > [!NOTE]
    > **없음**은 모든 LUIS 앱에 제공되는 의도입니다. 이는 앱에 제공되는 기능과 일치하지 않는 발언을 처리하는 데 사용됩니다.

1. **HomeAutomation.TurnOff** 의도를 선택합니다. 의도에는 엔터티를 사용하여 레이블이 지정된 예제 발화 목록이 포함되어 있습니다.

    > [!div class="mx-imgBorder"]
    > [![HomeAutomation.TurnOff 의도의 스크린샷](media/luis-quickstart-new-app/home-automation-turnoff.png "HomeAutomation.TurnOff 의도의 스크린샷")](media/luis-quickstart-new-app/home-automation-turnoff.png)

## <a name="train-the-luis-app"></a>LUIS 앱 학습

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>앱 테스트
앱을 학습시킨 후에는 테스트할 수 있습니다.

1. 오른쪽 상단 탐색에서 **테스트**를 선택합니다.

1. 대화형 테스트 창에 테스트 발언(예: `Turn off the lights`)을 입력하고 Enter 키를 누릅니다.

    ```
    Turn off the lights
    ```

    이 예제에서 `Turn off the lights`는 **HomeAutomation.TurnOff**의 최고 득점 의도로 올바르게 확인되었습니다.

    ![발언이 강조 표시된 테스트 패널의 스크린샷](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. **검사**를 선택하여 예측에 대한 자세한 정보를 봅니다.

    > [!div class="mx-imgBorder"]
    > ![검사 정보가 포함된 테스트 패널의 스크린샷](media/luis-quickstart-new-app/test.png)

1. 테스트 창을 닫습니다.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>앱을 게시하여 엔드포인트 URL 가져오기

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>V3 API 예측 엔드포인트 쿼리

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. 브라우저 주소 표시줄에서 쿼리 문자열에 대해 다음 이름 및 값 표시줄이 URL에 있는지 확인합니다. 쿼리 문자열에 없으면 추가하세요.

    |이름/값 쌍|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. 브라우저 주소 표시줄에서 URL 끝으로 이동하고 _쿼리_ 값에 대해 `turn off the living room light`를 입력한 다음, Enter를 누릅니다.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
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
                            "score": 0.902181149,
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
