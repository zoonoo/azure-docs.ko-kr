---
title: 클라이언트 및 서버 아키텍처
titleSuffix: An Azure Communication Services concept document
description: 통신 서비스의 아키텍처에 대해 알아봅니다.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8b63ce25d6839e8672027240099859dc7aad96c5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886076"
---
# <a name="client-and-server-architecture"></a>클라이언트 및 서버 아키텍처

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

모든 Azure Communication Services 응용 프로그램에는 **서비스** 를 사용 하 여 사용자 간 연결을 용이 하 게 하는 **클라이언트 응용** 프로그램이 있습니다. 이 페이지에서는 다양 한 시나리오에서 일반적인 아키텍처 요소를 보여 줍니다.

## <a name="user-access-management"></a>사용자 액세스 관리

Azure Communication Services 클라이언트 라이브러리는 `user access tokens` 통신 서비스 리소스에 안전 하 게 액세스 해야 합니다. `User access tokens` 토큰의 중요 한 특성과 해당 토큰을 생성 하는 데 필요한 연결 문자열 때문에 신뢰할 수 있는 서비스에 의해 생성 되 고 관리 되어야 합니다. 액세스 토큰을 제대로 관리 하지 못하면 리소스를 잘못 사용 하 여 추가 요금이 부과 될 수 있습니다. 신뢰할 수 있는 서비스를 사용 하 여 사용자 관리를 수행 하는 것이 좋습니다. 신뢰할 수 있는 서비스는 토큰을 생성 하 고 적절 한 암호화를 사용 하 여 클라이언트에 다시 전달 합니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="사용자 액세스 토큰 아키텍처를 보여 주는 다이어그램":::

자세한 내용은 [id 관리 모범 사례](../../security/fundamentals/identity-management-best-practices.md) 를 검토 하세요.

## <a name="browser-communication"></a>브라우저 통신

Azure 통신 JavaScript 클라이언트 라이브러리는 다양 한 텍스트, 음성 및 비디오 상호 작용으로 웹 응용 프로그램을 사용 하도록 설정할 수 있습니다. 응용 프로그램은 클라이언트 라이브러리를 통해 Azure 통신 서비스와 직접적으로 상호 작용 하 여 데이터 평면에 액세스 하 고 실시간 텍스트, 음성 및 비디오 통신을 제공 합니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="통신 서비스의 브라우저 아키텍처를 보여 주는 다이어그램입니다.":::

## <a name="native-app-communication"></a>네이티브 앱 통신

대부분의 시나리오는 네이티브 응용 프로그램에서 가장 효과적으로 제공 됩니다. Azure 통신 서비스는 브라우저와 앱 간 통신을 모두 지원 합니다.  기본 응용 프로그램 환경을 빌드하는 경우 푸시 알림을 사용 하면 응용 프로그램이 실행 되 고 있지 않은 경우에도 사용자가 호출을 받을 수 있습니다. Azure 통신 서비스를 사용 하면 Google Firebase, Apple Push Notification Service 및 Windows 푸시 알림에 대 한 통합 푸시 알림을 쉽게 수행할 수 있습니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="네이티브 앱 통신을 위한 통신 서비스 아키텍처를 보여 주는 다이어그램":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>PSTN (공용 스위치 전화 통신 네트워크)을 통한 음성 및 SMS

전화 시스템을 통해 통신 하면 응용 프로그램의 도달 범위를 크게 늘릴 수 있습니다. PSTN 음성 및 SMS 시나리오를 지원 하기 위해 Azure 통신 서비스를 사용 하면 Azure Portal에서 직접 또는 REST Api와 클라이언트 라이브러리를 사용 하 여 [전화 번호를 획득할](../quickstarts/telephony-sms/get-phone-number.md) 수 있습니다. 전화 번호를 구한 후에는 인바운드 및 아웃 바운드 시나리오에서 모두 PSTN 호출 및 SMS를 사용 하 여 고객에 게 연결 하는 데 사용할 수 있습니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

> [!Note]
> 공개 미리 보기 동안 미국 전화 번호의 프로 비전은 미국 및 캐나다에 있는 청구 주소를 사용 하는 고객에 게 제공 됩니다. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="통신 서비스 PSTN 아키텍처를 보여 주는 다이어그램":::

PSTN 및 SMS 솔루션에 대 한 자세한 내용은 [pstn 및 sms 솔루션 계획](../concepts/telephony-sms/plan-solution.md) 을 참조 하세요.

## <a name="humans-communicating-with-bots-and-other-services"></a>봇 및 기타 서비스와 통신 하는 사람

Azure 통신 서비스는 Azure 통신 서비스 데이터 평면에 직접 액세스 하는 서비스와 함께 텍스트 및 음성 채널을 통해 사용자 대 시스템 통신을 지원 합니다. 예를 들어 수신 전화 통화에 대 한 봇 응답을 보유 하거나 웹 채팅에 참여할 수 있습니다. Azure 통신 서비스는 이러한 시나리오를 호출 하 고 채팅 하는 데 사용할 수 있는 클라이언트 라이브러리를 제공 합니다. 샘플 아키텍처 흐름은 아래에서 찾을 수 있습니다.

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="통신 서비스 봇 아키텍처를 보여 주는 다이어그램":::

## <a name="networking"></a>네트워킹

사용자 간에 임의의 데이터를 교환할 수 있습니다. 예를 들어 공유 혼합 현실 또는 게임 환경을 동기화 할 수 있습니다. 텍스트, 음성 및 비디오 통신에 사용 되는 실시간 데이터 평면은 다음과 같은 두 가지 방법으로 직접 사용할 수 있습니다.

- 호출에서 **클라이언트 라이브러리** -장치를 호출 하면 호출 채널을 통해 데이터를 보내고 받기 위한 api에 액세스할 수 있습니다. 이 방법은 기존 상호 작용에 데이터 통신을 추가 하는 가장 쉬운 방법입니다.
- **STUN/turn** -Azure 통신 서비스는 표준 규격 STUN를 사용 하 고 서비스를 사용 가능 하 게 설정 합니다. 이렇게 하면 표준화 된 기본 형식 위에 고도로 사용자 지정 된 전송 계층을 빌드할 수 있습니다. 사용자 고유의 표준 규격 클라이언트를 작성 하거나 [Winrtc](https://github.com/microsoft/winrtc)와 같은 오픈 소스 라이브러리를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)

자세한 내용은 다음 아티클을 참조하세요.

- [인증](../concepts/authentication.md) 에 대 한 자세한 정보
- [PSTN 및 SMS 솔루션](../concepts/telephony-sms/plan-solution.md) 에 대 한 자세한 정보

- [앱에 채팅 추가](../quickstarts/chat/get-started.md)
- [앱에 음성 통화 추가](../quickstarts/voice-video-calling/getting-started-with-calling.md)