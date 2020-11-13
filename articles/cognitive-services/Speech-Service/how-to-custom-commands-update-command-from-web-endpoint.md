---
title: 웹 끝점에서 명령 업데이트
titleSuffix: Azure Cognitive Services
description: 웹 끝점에서 명령 업데이트
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571251"
---
# <a name="update-a-command-from-a-web-endpoint"></a>웹 끝점에서 명령 업데이트

클라이언트 응용 프로그램에서 음성 입력 없이 진행 중인 명령의 상태를 업데이트 해야 하는 경우 웹 끝점에 대 한 호출을 사용 하 여 명령을 업데이트할 수 있습니다.

이 문서에서는 웹 끝점에서 진행 중인 명령을 업데이트 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소
> [!div class = "checklist"]
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure Function 만들기 

이 예제에서는 다음 입력 (또는이 입력의 하위 집합)을 지 원하는 HTTP-Triggered [Azure 함수가](https://docs.microsoft.com/azure/azure-functions/) 필요 합니다.

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

이 입력의 키 특성을 검토할 수 있습니다.

| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversationId"은 대화의 고유 식별자 이며, 클라이언트 앱에서이 id를 생성할 수 있습니다. |
| **currentCommand** | "currentCommand"는 현재 대화에서 활성화 된 명령입니다. |
| **name** | "name"은 명령의 이름이 고 "parameters"는 매개 변수의 현재 값을 포함 하는 맵입니다. |
| **currentGlobalParameters** | "currentGlobalParameters"는 "parameters"와 같은 맵 이지만 글로벌 매개 변수에 사용 됩니다. |

Azure 함수의 출력은 다음 형식을 지원 해야 합니다.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

는 [클라이언트에서 명령을 업데이트할](./how-to-custom-commands-update-command-from-client.md)때 사용 되는 것과 동일한 형식 이므로이 형식을 인식할 수 있습니다. 

이제 NodeJS를 기반으로 하는 Azure Function을 만들고이 코드를 복사 하 여 붙여넣습니다.

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

사용자 지정 명령에서이 Azure 함수를 호출 하는 경우 대화의 현재 값을 전송 하 고, 업데이트 하려는 매개 변수를 반환 하거나 현재 명령을 취소 하려고 합니다.

## <a name="update-the-existing-custom-commands-app"></a>기존 사용자 지정 명령 앱 업데이트

이제 기존 사용자 지정 명령 앱을 사용 하 여 Azure 함수를 연결 해 보겠습니다.

1. IncrementCounter 라는 새 명령을 추가 합니다.
1. "Increment" 값을 사용 하 여 하나의 예제 문장을 추가 합니다.
1. Number 형식으로 Counter (위의 Azure Function에 지정 된 것과 동일한 이름) 라는 새 매개 변수를 추가 합니다. 기본값은 0입니다.
1. Azure Function의 URL 및 원격 업데이트를 사용 하도록 설정 하 여 IncrementEndpoint 라는 새 웹 끝점을 추가 합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="원격 업데이트를 사용 하 여 웹 끝점 설정":::
1. "IncrementRule" 라는 새 상호 작용 규칙을 만들고 웹 끝점 호출 작업을 추가 합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="증가 규칙":::
1. 작업 구성에서 IncrementEndpoint을 선택 하 고, 성공 시 구성을 선택 하 고, 오류 메시지와 함께 실패 시 카운터 값을 사용 하 여 음성 응답을 보냅니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="증분 카운터 호출 끝점 설정":::
1. 사용자 입력을 대기 하는 규칙의 실행 후 상태를 설정 합니다.

## <a name="test-it"></a>테스트

1. 앱 저장 및 학습
1. 테스트 클릭
1. "증가값" (IncrementCounter 명령에 대 한 예제 문장)을 몇 번 보냅니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="증가값 카운터 예":::

카운터 매개 변수의 값이 각 턴에서 Azure 함수로 증가 하는 방식을 확인 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 애플리케이션에 CI/CD 프로세스 사용](./how-to-custom-commands-deploy-cicd.md)