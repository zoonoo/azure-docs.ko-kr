---
title: Azure Communication Services에서 Teams interop 시작
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Chat 클라이언트 라이브러리를 사용하여 Teams 미팅에 조인하는 방법을 알아봅니다.
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 75580931b2df33b02b9759b6faaa5bec548daaae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496180"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>빠른 시작: 채팅 앱으로 Teams 미팅에 참가

> [!IMPORTANT]
> [Teams 테넌트 상호 운용성](../../concepts/teams-interop.md)을 사용/사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)을 작성합니다.

JavaScript 클라이언트 라이브러리를 사용하여 채팅 솔루션을 Microsoft Teams에 연결하여 Azure Communication Services를 시작하세요. 

## <a name="prerequisites"></a>사전 요구 사항 

1.  [Teams 배포](/deployoffice/teams-install). 
2. 작동하는 [채팅 앱](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Teams 상호 운용성 사용 

게스트 사용자로 Teams 미팅에 조인하는 Communication Services 사용자는 Teams 미팅 호출에 조인한 경우에만 미팅 채팅에 액세스할 수 있습니다. Teams 미팅 호출에 Communication Services 사용자를 추가하는 방법을 알아보려면 [Teams interop](../voice-video-calling/get-started-teams-interop.md) 설명서를 참조하세요.

이 기능을 사용하려면 두 엔터티를 소유하는 조직의 구성원이어야 합니다.

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [채팅 영웅 샘플](../../samples/chat-hero-sample.md) 체크 아웃
- [채팅 작동 방식](../../concepts/chat/concepts.md)에 대한 자세한 정보