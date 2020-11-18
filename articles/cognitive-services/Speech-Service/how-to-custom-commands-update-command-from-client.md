---
title: 클라이언트 앱에서 명령 업데이트
titleSuffix: Azure Cognitive Services
description: 클라이언트 앱에서 명령 업데이트
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 1bffb09d0f49bbd0059e8a528d67bfe215f0650d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654350"
---
# <a name="update-a-command-from-the-client"></a>클라이언트에서 명령 업데이트

이 문서에서는 클라이언트 응용 프로그램에서 진행 중인 명령을 업데이트 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>명령의 상태를 업데이트 합니다.

클라이언트 응용 프로그램에서 음성 입력 없이 진행 중인 명령의 상태를 업데이트 해야 하는 경우에는 이벤트를 전송 하 여 명령을 업데이트할 수 있습니다.

이 시나리오를 설명 하기 위해 진행 중인 명령의 상태를 업데이트 하기 위해 (원래 해제 된) 다음과 같은 이벤트 작업을 보낼 수 있습니다. 

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

이 작업의 주요 특성을 검토할 수 있습니다.

| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | 활동은 "event" 유형이 며 이벤트의 이름은 "RemoteUpdate" 여야 합니다. |
| **value** | 특성 "value"는 현재 명령을 업데이트 하는 데 필요한 특성을 포함 합니다. |
| **updatedCommand** | "UpdatedCommand" 특성은 명령의 이름을 포함 하 고, "updatedParameters"는 매개 변수의 이름과 업데이트 된 값을 포함 하는 맵입니다. |
| **cancel** | 진행 중인 명령을 취소 해야 하는 경우 "cancel" 특성을 true로 설정 합니다. |
| **updatedGlobalParameters** | "UpdatedGlobalParameters" 특성은 "updatedParameters"와 마찬가지로 전역 매개 변수에 사용 되는 맵 이기도 합니다. |
| **processTurn** | 활동을 보낸 후에 처리 해야 하는 경우에는 "processTurn" 특성을 true로 설정 합니다. |

사용자 지정 명령 포털에서이 시나리오를 테스트할 수 있습니다.

1. 이전에 만든 사용자 지정 명령 애플리케이션을 엽니다. 
1. 학습, 테스트를 차례로 클릭 합니다.
1. "Turn"을 보냅니다.
1. 측면 패널을 열고 활동 편집기를 클릭 합니다.
1. 이전 섹션에 지정 된 RemoteCommand 이벤트를 입력 하 고 보냅니다.
    > [!div class="mx-imgBorder"]
    > ![원격 명령 보내기](media/custom-commands/send-remote-command-activity.png)

음성 또는 텍스트 대신 클라이언트의 활동을 사용 하 여 "OnOff" 매개 변수의 값을 "on"으로 설정 하는 방법을 확인 합니다.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>명령에 대 한 매개 변수의 카탈로그를 업데이트 합니다.

매개 변수에 대 한 유효한 옵션 목록을 구성할 때 매개 변수의 값은 응용 프로그램에 대해 전역적으로 정의 됩니다. 

이 예제에서 # 매개 변수는 대화에 관계 없이 지원 되는 값의 고정 목록을 포함 합니다.

대화 당 매개 변수의 카탈로그에 새 항목을 추가 해야 하는 경우 다음 작업을 보낼 수 있습니다.

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
이 작업을 사용 하 여 "TurnOnOff" 명령의 "주제 장치" 매개 변수 카탈로그에 "스테레오" 항목을 추가 했습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="업데이트 카탈로그":::

몇 가지 사항을 참고 하세요.
1. 이 작업은 한 번만 전송 해야 합니다 (연결을 시작한 직후에).
1. 이 작업을 보낸 후에는 이벤트 ParameterCatalogsUpdated 클라이언트에 다시 전송 될 때까지 기다려야 합니다.

## <a name="add-additional-context-from-the-client-application"></a>클라이언트 응용 프로그램에서 추가 컨텍스트 추가

나중에 사용자 지정 명령 응용 프로그램에서 사용할 수 있는 대화 당 클라이언트 응용 프로그램에서 추가 컨텍스트를 설정할 수 있습니다. 

예를 들어, 사용자 지정 명령 응용 프로그램에 연결 된 장치의 ID 및 이름을 보내려는 시나리오에 대해 생각해 볼 수 있습니다.

이 시나리오를 테스트 하려면 현재 응용 프로그램에 새 명령을 만들어 보겠습니다.
1. GetDeviceInfo 라는 새 명령을 만듭니다.
1. "장치 정보 가져오기"를 사용 하 여 예제 문장을 추가 합니다.
1. 완료 규칙 "Done"에서 clientContext의 특성이 포함 된 음성 응답 보내기 작업을 추가 합니다.
    > ![컨텍스트를 사용 하 여 음성 응답 보내기](media/custom-commands/send-speech-response-context.png)
1. 응용 프로그램을 저장, 학습 및 테스트 합니다.
1. 테스트 창에서 작업을 보내 클라이언트 컨텍스트를 업데이트 합니다.
    > ```json
    >{
    >   "type": "event",
    >   "name": "RemoteUpdate",
    >   "value": {
    >     "clientContext": {
    >       "deviceId": "12345",
    >       "deviceName": "My device"
    >     },
    >     "processTurn": false
    >   }
    >}
    > ```
1. "장치 정보 가져오기" 텍스트를 보냅니다.
    > ![클라이언트 컨텍스트 작업 보내기](media/custom-commands/send-client-context-activity.png)

몇 가지 사항을 참고 하세요.
1. 이 작업은 한 번만 전송 해야 합니다 (연결을 시작한 직후에).
1. ClientContext에 복합 개체를 사용할 수 있습니다.
1. 음성 응답에서 clientContext를 사용 하 여 작업을 보내고 웹 끝점을 호출할 때 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 엔드포인트에서 명령 업데이트](./how-to-custom-commands-update-command-from-web-endpoint.md)
