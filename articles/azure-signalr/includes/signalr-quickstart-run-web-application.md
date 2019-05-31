---
title: 포함 파일
description: 포함 파일
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66128257"
---
## <a name="run-the-web-application"></a>웹 애플리케이션 실행

1. 편의를 위해 GitHub에서 호스트된 샘플 단일 페이지 웹 애플리케이션이 있습니다. 브라우저에서 [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/)을(를) 엽니다.

    > [!NOTE]
    > HTML 파일 소스는 [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html)에 있습니다.

1. 함수 앱 기본 URL을 입력하라는 메시지가 표시되면 `http://localhost:7071`을 입력합니다.

1. 메시지가 표시되면 사용자 이름을 입력합니다.

1. 웹 애플리케이션이 함수 앱에서 *GetSignalRInfo* 함수를 호출하여 Azure SignalR Service에 연결할 연결 정보를 검색합니다. 연결이 완료되면 이 채팅 메시지 입력 상자가 표시됩니다.

1. 메시지를 입력하고 Enter 키를 누릅니다. 이 애플리케이션이 Azure 함수 앱에서 이 메시지를 *SendMessage* 함수로 보낸 다음, 이 앱이 SignalR 출력 바인딩을 사용하여 연결된 클라이언트 모두에 이 메시지를 브로드캐스트합니다. 모든 기능이 올바르게 작동하는 경우 이 메시지가 애플리케이션에 표시됩니다.

    ![애플리케이션 실행](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. 다른 브라우저 창에서 웹 애플리케이션의 다른 인스턴스를 엽니다. 애플리케이션의 모든 인스턴스에 전송된 메시지가 표시됩니다.

> [!IMPORTANT]
> HTML 페이지는 HTTPS를 통해 제공되지만 로컬 Azure Functions 런타임은 기본적으로 HTTP를 사용하기 때문에, 브라우저(예: Firefox)는 함수에 대한 웹 페이지의 요청을 차단하는 혼합 콘텐츠 정책을 적용할 수 있습니다. 이 문제를 해결하려면 이러한 제한 사항이 없는 브라우저를 사용하거나 */docs/demo/chat-v2* 디렉터리에서 [http-server](https://www.npmjs.com/package/http-server)와 같은 로컬 HTTP 서버를 시작합니다. *local.settings.json*의 `CORS` 설정에 원본이 추가되어 있는지 확인합니다.