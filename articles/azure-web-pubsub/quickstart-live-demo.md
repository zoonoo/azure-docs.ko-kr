---
title: Azure Web PubSub 서비스 라이브 데모
description: Azure Web PubSub 서비스 라이브 데모를 시작하기 위한 빠른 시작입니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: e35ef66c038748e31ba218a56adb8111374d2339
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113219609"
---
# <a name="quickstart-get-started-with-chatroom-live-demo"></a>빠른 시작: 채팅방 라이브 데모 시작

Azure Web PubSub 서비스를 사용하면 WebSocket 및 게시-구독 패턴을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. [채팅방 라이브 데모](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)에서는 Azure Web PubSub에서 제공하는 실시간 메시징 기능을 보여줍니다. 이 라이브 데모를 통해 채팅 그룹에 쉽게 조인하고 특정 그룹에 실시간 메시지를 보낼 수 있습니다. 

:::image type="content" source="media/quickstart-live-demo/chat-live-demo.gif" alt-text="채팅방 라이브 데모 사용":::

이 빠른 시작에서는 라이브 데모를 통해 쉽게 시작하는 방법을 알아봅니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-chatroom-live-demo"></a>채팅방 라이브 데모 시작

### <a name="get-client-url-with-a-temp-access-token"></a>임시 액세스 토큰을 사용하여 클라이언트 URL 가져오기

첫 번째 단계로 Azure Web PubSub 인스턴스에서 클라이언트 URL을 가져와야 합니다. 

- Azure Portal로 이동하여 Azure Web PubSub 인스턴스를 확인합니다.
- `Key` 블레이드의 `Client URL Generator`로 이동합니다. 
- 적절하게 `Roles` 설정: **그룹에 보내기** 및 **그룹 가입/탈퇴**
- `Client Access URL`을 생성하고 복사합니다. 

:::image type="content" source="media/quickstart-live-demo/generate-client-url.png" alt-text="클라이언트 URL 생성의 스크린샷.":::

### <a name="try-the-live-demo"></a>라이브 데모 사용해보기 

이 라이브 데모를 사용하면 그룹에 가입하거나 탈퇴하고 그룹 구성원에게 메시지를 쉽게 보낼 수 있습니다. 

- [채팅방 라이브 데모](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)를 열고 `Client Access URL` 및 Connect를 붙여넣습니다. 

:::image type="content" source="media/quickstart-live-demo/paste-client-access-url.png" alt-text="라이브 데모를 사용하여 클라이언트 URL을 붙여넣는 스크린샷.":::

> [!NOTE]
>  **클라이언트 액세스 URL** 은 시작 환경을 단순화하기 위해 포털에서 제공되는 편리한 도구로, 이 클라이언트 액세스 URL을 사용하여 빠른 연결 테스트를 수행할 수도 있습니다. 사용자 고유의 애플리케이션을 작성할 수 있도록 SDK를 4개 언어로 제공하여 URL을 생성하는 데 도움을 줍니다. 

- 가입할 다른 그룹과 메시지를 보낼 다른 그룹을 시도하고 수신되는 메시지를 확인합니다. 예를 들어:
    - 두 클라이언트를 동일한 그룹에 조인합니다. 메시지가 그룹 구성원에게 브로드캐스트될 수 있음을 알 수 있습니다. 
    - 두 클라이언트를 서로 다른 그룹에 조인합니다. 그룹 멤버가 아닌 경우에는 클라이언트가 메시지를 수신할 수 없습니다. 
- `Client Access URL`을 생성할 때 `Roles`를 선택 취소하여 그룹에 조인하거나 그룹에 메시지를 보낼 때 어떤 일이 발생하는지 확인할 수도 있습니다. 예를 들어:
    - `Send to Groups` 권한을 선택 취소합니다. 클라이언트가 그룹에 메시지를 보낼 수 없는 것을 볼 수 있습니다. 
    - `Join/Leave Groups` 권한을 선택 취소합니다. 클라이언트는 그룹에 조인할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 채팅방 라이브 데모를 통해 실시간 메시징 기능을 알아보았습니다. 이제 자체 애플리케이션을 빌드할 수 있습니다. 

> [!div class="nextstepaction"]
> [빠른 시작: Azure Web PubSub에서 메시지 게시 및 구독](https://azure.github.io/azure-webpubsub/getting-started/publish-messages/js-publish-message)

> [!div class="nextstepaction"]
> [빠른 시작: Azure Web PubSub를 사용하여 간단한 채팅방 만들기](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [빠른 시작: Azure Functions 및 Azure Web PubSub 서비스를 사용하여 간단한 서버리스 채팅 애플리케이션 만들기](./quickstart-serverless.md)

> [!div class="nextstepaction"]
> [Azure Web PubSub 샘플 자세히 살펴보기](https://github.com/Azure/azure-webpubsub/tree/main/samples)