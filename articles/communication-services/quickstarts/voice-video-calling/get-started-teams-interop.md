---
title: 빠른 시작 - Azure Communication Services의 Teams interop
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Calling SDK를 사용하여 Teams 미팅에 참가하는 방법을 알아봅니다.
author: chpalm
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-web-ios-android
ms.openlocfilehash: a9ef74c04c1f709348ae1d6dd97558ee6bedccf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654971"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>빠른 시작: 통화 앱으로 Teams 미팅 참가

> [!IMPORTANT]
> [Teams 테넌트 상호 운용성](../../concepts/teams-interop.md)을 사용/사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u)을 작성합니다.

JavaScript 클라이언트 라이브러리를 사용하여 호출 솔루션을 Microsoft Teams에 연결하여 Azure Communication Services를 시작하세요.

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop-ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [영웅 샘플 호출](../../samples/calling-hero-sample.md) 체크 아웃
- [클라이언트 라이브러리 기능 호출](./calling-client-samples.md)에 대한 자세한 정보
- [호출 작동 방식](../../concepts/voice-video-calling/about-call-types.md)에 대해 자세히 알아보기
