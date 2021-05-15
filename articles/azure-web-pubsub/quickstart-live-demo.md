---
title: Azure Web PubSub 서비스 라이브 데모
description: Azure Web PubSub 서비스 라이브 데모를 시작하기 위한 빠른 시작입니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 04/26/2021
ms.openlocfilehash: a516d215a971b2a6cb54d4e73305cc69ce0edf8c
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166958"
---
# <a name="quickstart-get-started-with-live-demo"></a>빠른 시작: 라이브 데모 시작

Azure Web PubSub 서비스를 사용하면 WebSocket 및 게시-구독 패턴을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. 이 빠른 시작에서는 라이브 데모를 통해 쉽게 시작하는 방법을 알아봅니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="get-started-with-the-live-demo"></a>라이브 데모 시작

### <a name="get-client-url-with-a-temp-access-token"></a>임시 액세스 토큰을 사용하여 클라이언트 URL 가져오기

첫 번째 단계로 Azure Web PubSub 인스턴스에서 클라이언트 URL을 가져와야 합니다. 

- Azure Portal로 이동하여 Azure Web PubSub 인스턴스를 확인합니다.
- `Key` 블레이드의 `Client URL Generator`로 이동합니다. 
- 적절하게 `Roles` 설정: **그룹에 보내기** 및 **그룹 가입/탈퇴**
- `Client Access URL`을 생성하고 복사합니다. 

### <a name="try-the-live-demo"></a>라이브 데모 사용해보기 

이 라이브 데모를 사용하면 그룹에 가입하거나 탈퇴하고 그룹 구성원에게 메시지를 쉽게 보낼 수 있습니다. 

- [클라이언트 Pub/Sub Demo](https://azure.github.io/azure-webpubsub/demos/clientpubsub.html)를 열고, `Client Access URL` 및 Connect를 붙여넣습니다. 
- 가입할 다른 그룹과 메시지를 보낼 다른 그룹을 시도하고 수신되는 메시지를 확인합니다.
- `Client Access URL`을 생성할 때 `Roles`를 선택 취소하여 그룹에 가입/탈퇴하거나 그룹에 메시지를 보낼 때 어떤 일이 발생하는지 확인할 수도 있습니다.