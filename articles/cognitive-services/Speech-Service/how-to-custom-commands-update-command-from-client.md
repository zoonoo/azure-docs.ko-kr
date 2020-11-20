---
title: 클라이언트 앱에서 명령 업데이트
titleSuffix: Azure Cognitive Services
description: 클라이언트 응용 프로그램에서 명령을 업데이트 하는 방법을 알아봅니다.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 765bcbd0521f93bacb0799595e6fbef565d0f313
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963626"
---
# <a name="update-a-command-from-a-client-app"></a>클라이언트 앱에서 명령 업데이트

이 문서에서는 클라이언트 응용 프로그램에서 진행 중인 명령을 업데이트 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>명령의 상태를 업데이트 합니다.

클라이언트 응용 프로그램에서 음성 입력 없이 진행 중인 명령의 상태를 업데이트 해야 하는 경우 명령을 업데이트 하는 이벤트를 보낼 수 있습니다.

이 시나리오를 설명 하기 위해 다음 이벤트 작업을 보내 진행 중인 명령의 상태 ()를 업데이트 합니다 `TurnOnOff` . 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

이 작업의 주요 특성을 검토해 보겠습니다.

| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | 활동은 유형이 며 `"event"` 이벤트의 이름은 이어야 `"RemoteUpdate"` 합니다. |
| **value** | 특성은 `"value"` 현재 명령을 업데이트 하는 데 필요한 특성을 포함 합니다. |
| **updatedCommand** | `"updatedCommand"`명령 이름을 포함 하는 특성입니다. 해당 특성 내에서 `"updatedParameters"` 는 매개 변수의 이름과 업데이트 된 값을 포함 하는 맵입니다. |
| **cancel** | 진행 중인 명령을 취소 해야 하는 경우에는 특성을 `"cancel"` 로 설정 `true` 합니다. |
| **updatedGlobalParameters** | 특성은 `"updatedGlobalParameters"` 와 동일한 맵 `"updatedParameters"` 이지만 전역 매개 변수에 사용 됩니다. |
| **processTurn** | 활동을 보낸 후에도 다시 처리 해야 하는 경우에는 특성을 `"processTurn"` 로 설정 `true` 합니다. |

사용자 지정 명령 포털에서이 시나리오를 테스트할 수 있습니다.

1. 이전에 만든 사용자 지정 명령 응용 프로그램을 엽니다. 
1. **학습** , **테스트** 를 차례로 선택 합니다.
1. 전송 `turn` .
1. 사이드 패널을 열고 **활동 편집기** 를 선택 합니다.
1. `RemoteCommand`이전 섹션에서 지정한 이벤트를 입력 하 고 보냅니다.
    > [!div class="mx-imgBorder"]
    > ![원격 명령의 이벤트를 보여 주는 스크린샷](media/custom-commands/send-remote-command-activity.png)

매개 변수의 값 `"OnOff"` 이 `"on"` 음성 또는 텍스트 대신 클라이언트의 작업을 통해로 설정 된 방식을 확인 합니다.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>명령에 대 한 매개 변수의 카탈로그를 업데이트 합니다.

매개 변수에 대 한 유효한 옵션 목록을 구성할 때 매개 변수의 값은 응용 프로그램에 대해 전역적으로 정의 됩니다. 

이 예제에서 `SubjectDevice` 매개 변수는 대화에 관계 없이 지원 되는 값의 고정 목록을 포함 합니다.

대화 당 매개 변수의 카탈로그에 새 항목을 추가 하려는 경우 다음 작업을 보낼 수 있습니다.

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
이 작업을 사용 하면 `"stereo"` 명령에서 매개 변수의 카탈로그에 항목을 추가 했습니다 `"SubjectDevice"` `"TurnOnOff"` .

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="카탈로그 업데이트를 보여 주는 스크린샷":::

다음 몇 가지 사항을 참고 하세요.
- 이 작업은 한 번만 전송 해야 합니다 (이상적으로는 연결을 시작한 직후).
- 이 작업을 보낸 후에는 이벤트를 클라이언트로 다시 보낼 때까지 기다려야 합니다 `ParameterCatalogsUpdated` .

## <a name="add-more-context-from-the-client-application"></a>클라이언트 응용 프로그램에서 더 많은 컨텍스트 추가

나중에 사용자 지정 명령 응용 프로그램에서 사용할 수 있는 대화 당 클라이언트 응용 프로그램에서 추가 컨텍스트를 설정할 수 있습니다. 

예를 들어, 사용자 지정 명령 응용 프로그램에 연결 된 장치의 ID 및 이름을 보내려는 시나리오에 대해 생각해 볼 수 있습니다.

이 시나리오를 테스트 하려면 현재 응용 프로그램에서 새 명령을 만들어 보겠습니다.
1. 라는 새 명령을 만듭니다 `GetDeviceInfo` .
1. 의 예제 문장을 추가 `get device info` 합니다.
1. 완료 규칙 **완료** 에서의 특성이 포함 된 **음성 응답 보내기** 작업을 추가 합니다 `clientContext` .
   ![컨텍스트를 사용 하 여 음성을 보내기 위한 응답을 보여 주는 스크린샷](media/custom-commands/send-speech-response-context.png)
1. 응용 프로그램을 저장, 학습 및 테스트 합니다.
1. 테스트 창에서 작업을 보내 클라이언트 컨텍스트를 업데이트 합니다.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. 텍스트를 보냅니다 `get device info` .
   ![클라이언트 컨텍스트를 보내기 위한 활동을 보여 주는 스크린샷](media/custom-commands/send-client-context-activity.png)

다음 몇 가지 사항을 참고 하세요.
- 이 작업은 한 번만 전송 해야 합니다 (이상적으로는 연결을 시작한 직후).
- 에 복합 개체를 사용할 수 있습니다 `clientContext` .
- `clientContext`음성 응답에서를 사용 하 여 활동을 보내고 웹 끝점을 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 엔드포인트에서 명령 업데이트](./how-to-custom-commands-update-command-from-web-endpoint.md)
