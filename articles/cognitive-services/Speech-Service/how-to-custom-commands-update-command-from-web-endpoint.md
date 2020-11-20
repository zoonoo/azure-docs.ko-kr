---
title: 웹 엔드포인트에서 명령 업데이트
titleSuffix: Azure Cognitive Services
description: 웹 끝점에 대 한 호출을 사용 하 여 명령의 상태를 업데이트 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: a24f1337a68f38db273688e9a91c65ac2f4736b4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963609"
---
# <a name="update-a-command-from-a-web-endpoint"></a>웹 엔드포인트에서 명령 업데이트

클라이언트 응용 프로그램에서 음성 입력 없이 진행 중인 명령의 상태를 업데이트 해야 하는 경우 웹 끝점에 대 한 호출을 사용 하 여 명령을 업데이트할 수 있습니다.

이 문서에서는 웹 끝점에서 진행 중인 명령을 업데이트 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!div class = "checklist"]
> * 이전에 [만든 사용자 지정 명령 앱](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Azure Function 만들기 

이 예제에서는 다음 입력 또는이 입력의 하위 집합을 지 원하는 HTTP 트리거 [Azure 함수가](https://docs.microsoft.com/azure/azure-functions/) 필요 합니다.

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

이 입력의 주요 특성을 검토해 보겠습니다.

| attribute | 설명 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 대화의 고유 식별자입니다. 클라이언트 앱에서이 ID를 생성할 수 있습니다. |
| **currentCommand** | 대화에서 현재 활성화 된 명령입니다. |
| **name** | 명령 이름입니다. `parameters`특성은 매개 변수의 현재 값을 포함 하는 맵입니다. |
| **currentGlobalParameters** | 과 같은 맵 `parameters` 이지만 전역 매개 변수에 사용 됩니다. |

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

[클라이언트에서 명령을 업데이트할](./how-to-custom-commands-update-command-from-client.md)때 사용한 것과 동일한 형식을 인식할 수 있습니다. 

이제 Node.js 기반으로 Azure 함수를 만듭니다. 이 코드를 복사 하 여 붙여 넣습니다.

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

사용자 지정 명령에서이 Azure 함수를 호출 하는 경우 대화의 현재 값을 보냅니다. 업데이트 하려는 매개 변수를 반환 하거나 현재 명령을 취소 합니다.

## <a name="update-the-existing-custom-commands-app"></a>기존 사용자 지정 명령 앱 업데이트

Azure function을 기존 사용자 지정 명령 앱에 연결 해 보겠습니다.

1. 이라는 새 명령을 추가 `IncrementCounter` 합니다.
1. 값을 사용 하 여 하나의 예제 문장을 추가 `increment` 합니다.
1. `Counter` `Number` 의 기본값을 사용 하 여 형식의 새 매개 변수 (Azure 함수에 지정 된 이름과 동일)를 추가 `0` 합니다.
1. `IncrementEndpoint` **원격 업데이트가** **사용** 으로 설정 된 Azure 함수의 URL을 사용 하 여 라는 새 웹 끝점을 추가 합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="원격 업데이트를 사용 하 여 웹 끝점을 설정 하는 방법을 보여 주는 스크린샷":::
1. **IncrementRule** 라는 새 상호 작용 규칙을 만들고 **웹 끝점 호출** 작업을 추가 합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="상호 작용 규칙의 생성을 보여 주는 스크린샷":::
1. 작업 구성에서을 선택 `IncrementEndpoint` 합니다. **성공 시** 값을 사용 하 여 **음성 응답을 보내도록** 구성 하 `Counter` 고 오류 메시지와 함께 **실패 시** 구성 합니다.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="웹 끝점을 호출 하는 증분 카운터를 설정 하는 방법을 보여 주는 스크린샷":::
1. **사용자 입력을 대기** 하는 규칙의 실행 후 상태를 설정 합니다.

## <a name="test-it"></a>테스트

1. 앱을 저장 하 고 학습 합니다.
1. **테스트** 를 선택합니다.
1. `increment`몇 번 보냅니다 (명령의 예제 문장 `IncrementCounter` ).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="증분 카운터 예제를 보여 주는 스크린샷":::

Azure 함수가 `Counter` 각 턴에서 매개 변수의 값을 증가 시키는 방법을 확인 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 명령 애플리케이션에 CI/CD 프로세스 사용](./how-to-custom-commands-deploy-cicd.md)
