---
title: Azure Communication Services에서 Teams interop 시작
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Chat 클라이언트 라이브러리를 사용하여 Teams 미팅에 조인하는 방법을 알아봅니다.
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ea66e4295e8228aa382aa29a46fcca8147dcbc98
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578051"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>빠른 시작: 채팅 앱으로 Teams 미팅에 참가

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

JavaScript 클라이언트 라이브러리를 사용하여 채팅 솔루션을 Microsoft Teams에 연결하여 Azure Communication Services를 시작하세요. 

## <a name="prerequisites"></a>사전 요구 사항 

1.  [Teams 배포](https://docs.microsoft.com/deployoffice/teams-install). 
2. 작동하는 [채팅 앱](./get-started.md). 

## <a name="enable-teams-interoperability"></a>Teams 상호 운용성 사용 

게스트 사용자로 Teams 미팅에 조인하는 Communication Services 사용자는 Teams 미팅 호출에 조인한 경우에만 미팅 채팅에 액세스할 수 있습니다. Teams 미팅 호출에 Communication Services 사용자를 추가하는 방법을 알아보려면 [Teams interop](../voice-video-calling/get-started-teams-interop.md) 설명서를 참조하세요.

Teams 상호 운용성 기능은 현재 프라이빗 미리 보기에 있습니다. 이 기능을 Communication Services 리소스에 사용하도록 설정하려면 다음 항목이 포함된 이메일을 acsfeedback@microsoft.com으로 보내주세요. 
1. Communication Services 리소스가 포함된 Azure 구독의 구독 ID 
2. Teams 테넌트 ID. 이를 가져오는 가장 쉬운 방법은 Team에 대한 링크를 가져와서 공유하는 것입니다. 

이 기능을 사용하려면 두 엔터티를 소유하는 조직의 구성원이어야 합니다. 

[!INCLUDE [Join Teams meetings](./includes/meeting-interop-javascript.md)]

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [채팅 영웅 샘플](../../samples/chat-hero-sample.md) 체크 아웃
- [채팅 작동 방식](../../concepts/chat/concepts.md)에 대한 자세한 정보
