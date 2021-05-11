---
title: 클라이언트 및 서버 아키텍처
titleSuffix: An Azure Communication Services concept document
description: Communication Services 아키텍처에 관해 알아봅니다.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 5ee33c032293329a6af69a0b2be691092c2a8da4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729646"
---
# <a name="client-and-server-architecture"></a>클라이언트 및 서버 아키텍처

모든 Azure Communication Services 애플리케이션에는 **서비스** 를 사용하여 사용자 간에 쉽게 연결하는 **클라이언트 애플리케이션** 이 있습니다. 이 페이지에서는 다양한 시나리오의 일반적인 아키텍처 요소를 보여 줍니다.

## <a name="user-access-management"></a>사용자 액세스 관리

Azure Communication Services SDK에서 Communication Services 리소스에 안전하게 액세스하려면 `user access tokens`이 필요합니다. `User access tokens`은 토큰이 중요하고 토큰을 생성하는 데 필요한 연결 문자열 때문에 신뢰할 수 있는 서비스에서 생성하고 관리해야 합니다. 액세스 토큰을 제대로 관리하지 못하면 리소스를 잘못 사용하여 추가 요금이 부과될 수 있습니다. 신뢰할 수 있는 서비스를 사용하여 사용자 관리를 수행하는 것이 좋습니다. 신뢰할 수 있는 서비스는 토큰을 생성하고 적절한 암호화를 사용하여 클라이언트에 다시 전달합니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="사용자 액세스 토큰 아키텍처를 보여 주는 다이어그램":::

자세한 내용은 [ID 관리 모범 사례](../../security/fundamentals/identity-management-best-practices.md)를 검토하세요.

## <a name="browser-communication"></a>브라우저 통신

Azure Communications JavaScript SDK는 서식 있는 텍스트, 음성 및 동영상 상호 작용을 통해 웹 애플리케이션을 사용으로 설정할 수 있습니다. 애플리케이션은 SDK를 통해 Azure Communication Services와 직접 상호 작용하여 데이터 평면에 액세스하고 실시간 텍스트, 음성 및 비디오 통신을 제공합니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Communication Services의 브라우저 아키텍처를 보여 주는 다이어그램.":::

## <a name="native-app-communication"></a>원시 앱 통신

대부분 시나리오는 원시 애플리케이션에서 가장 효과적으로 제공됩니다. Azure Communication Services는 브라우저-앱 및 앱-앱 통신을 모두 지원합니다.  원시 애플리케이션 환경을 빌드할 때 푸시 알림을 사용하면 애플리케이션이 실행되지 않을 때도 사용자가 호출을 받을 수 있습니다. Azure Communication Services를 사용하면 Google Firebase, Apple Push Notification Service 및 Windows Push Notifications에 대한 통합 푸시 알림을 통해 쉽게 수행할 수 있습니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="원시 앱 통신을 위한 Communication Services 아키텍처를 보여 주는 다이어그램.":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>PSTN(퍼블릭 스위치 전화 통신 네트워크)을 통한 음성 및 SMS

전화 시스템을 통해 통신하면 애플리케이션의 도달 범위를 크게 늘릴 수 있습니다. PSTN 음성과 SMS 시나리오를 지원하기 위해 Azure Communication Services를 사용하면 Azure Portal에서 직접 또는 REST API와 SDK를 사용하여 [전화번호를 획득](../quickstarts/telephony-sms/get-phone-number.md)할 수 있습니다. 전화번호를 얻으면 인바운드와 아웃바운드 시나리오에서 PSTN 통화와 SMS를 모두 사용하여 고객에게 도달하는 데 사용할 수 있습니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

> [!Note]
> 퍼블릭 미리 보기 중에 청구 주소가 미국과 캐나다에 있는 고객은 미국 전화번호를 프로비저닝할 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Communication Services PSTN 아키텍처를 보여 주는 다이어그램.":::

PSTN 전화번호에 관한 자세한 내용은 [전화번호 형식](../concepts/telephony-sms/plan-solution.md)을 참조하세요.

## <a name="humans-communicating-with-bots-and-other-services"></a>봇 및 기타 서비스와 통신하는 사람

Azure Communication Services에서는 Azure Communication Services 데이터 평면에 직접 액세스하는 서비스를 사용하여 텍스트와 음성 채널을 통한 인간-시스템 통신을 지원합니다. 예를 들어 봇이 수신 전화에 응답하거나 웹 채팅에 참여하게 할 수 있습니다. Azure Communication Services는 해당 시나리오에서 통화하고 채팅하는 데 사용할 수 있는 SDK를 제공합니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Communication Services 봇 아키텍처를 보여 주는 다이어그램.":::

## <a name="networking"></a>네트워킹

예를 들어 공유 Mixed Reality나 게임 환경을 동기화하기 위해 사용자 간에 임의 데이터를 교환할 수 있습니다. 텍스트, 음성, 비디오 통신에 사용되는 실시간 데이터 평면은 다음 두 가지 방법으로 직접 사용할 수 있습니다.

- **Calling SDK** - 호출 중인 디바이스는 호출 채널을 통해 데이터를 보내고 받는 데 사용하는 API에 액세스합니다. 이 방법은 기존 상호 작용에 데이터 통신을 추가하는 가장 쉬운 방법입니다.
- **STUN/TURN** - Azure Communication Services는 표준 규격 STUN 및 TURN 서비스를 제공합니다. 그러면 표준화된 기본 형식 위에 고도로 사용자 지정된 전송 계층을 빌드할 수 있습니다. 고유 표준 규격 클라이언트를 작성하거나 [WinRTC](https://github.com/microsoft/winrtc)와 같은 오픈 소스 라이브러리를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)

자세한 내용은 다음 문서를 참조하세요.

- [인증](../concepts/authentication.md)에 관한 자세한 내용
- [전화번호 형식](../concepts/telephony-sms/plan-solution.md)에 관한 자세한 내용

- [앱에 채팅 추가](../quickstarts/chat/get-started.md)
- [앱에 음성 통화 추가](../quickstarts/voice-video-calling/getting-started-with-calling.md)
