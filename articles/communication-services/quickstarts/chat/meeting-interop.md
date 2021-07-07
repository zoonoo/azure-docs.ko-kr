---
title: Azure Communication Services에서 Teams interop 시작
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication 채팅 SDK를 사용하여 Teams 미팅에 조인하는 방법을 알아봅니다.
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-web-ios
ms.openlocfilehash: 196c35d78b18241a808d4f941dd51d0f08a5b722
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110080266"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>빠른 시작: 채팅 앱으로 Teams 미팅에 참가

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> [Teams 테넌트 상호 운용성](../../concepts/teams-interop.md)을 사용/사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)을 작성합니다.

채팅 솔루션을 Microsoft Teams에 연결하여 Azure Communication Services를 시작하세요. 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [채팅 영웅 샘플](../../samples/chat-hero-sample.md) 체크 아웃
- [채팅 작동 방식](../../concepts/chat/concepts.md)에 대한 자세한 정보