---
title: Azure Communication Services란?
description: Azure Communication Services를 사용하여 실시간 통신을 통해 풍부한 사용자 환경을 개발하는 방법을 알아봅니다.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a006ece278e4ec750040bde5bd4b7a6144c9e720
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946306"
---
# <a name="what-is-azure-communication-services"></a>Azure Communication Services란?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Azure Communication Services를 사용하면 실시간 멀티미디어 음성, 비디오 및 IP 전화(Telephony-over-IP) 통신 기능을 애플리케이션에 쉽게 추가할 수 있습니다. 또한 Communication Services 클라이언트 라이브러리를 사용하면 채팅 및 SMS 기능을 통신 솔루션에 추가할 수 있습니다.

다양한 시나리오에서 Communication Services를 음성, 비디오, 텍스트 및 데이터 통신에 사용할 수 있습니다.

- 브라우저- 브라우저, 브라우저-앱 및 앱-앱 통신
- 봇 또는 다른 서비스와 상호 작용하는 사용자
- 공중 전화망을 통해 상호 작용하는 사용자와 봇

혼합 시나리오가 지원됩니다. 예를 들어 Communication Services 애플리케이션에는 브라우저와 기존 전화 통신 디바이스에서 동시에 말하는 사용자가 있을 수 있습니다. 또한 Communication Services는 Azure Bot Service와 결합하여 봇 기반 IVR(대화형 음성 응답) 시스템을 구축할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

Azure Communication Services를 처음 사용하는 경우 다음 리소스를 시작하는 것이 좋습니다.
<br>

| 리소스                               |설명                           |
|---                                    |---                                   |
|**[Communication Services 리소스 만들기](./quickstarts/create-communication-resource.md)**|Azure Portal 또는 Communication Services 관리 클라이언트 라이브러리를 통해 첫 번째 Communication Services 리소스를 프로비저닝하여 Azure Communication Services 사용을 시작할 수 있습니다. Communication Services 리소스 연결 문자열이 있으면 첫 번째 사용자 액세스 토큰을 프로비저닝할 수 있습니다.|
|**[첫 번째 사용자 액세스 토큰 만들기](./quickstarts/access-tokens.md)**|사용자 액세스 토큰은 Azure Communication Services 리소스에 대해 서비스를 인증하는 데 사용됩니다. 이러한 토큰은 Communication Services 관리 클라이언트 라이브러리를 사용하여 프로비저닝되고 다시 발급됩니다.|
|**[전화 번호 가져오기](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Communication Services를 사용하여 전화 번호를 프로비저닝하고 해제할 수 있습니다. 이러한 전화 번호는 아웃바운드 호출을 시작하고 SMS 통신 솔루션을 구축하는 데 사용할 수 있습니다.|
|**[앱에서 SMS 보내기](./quickstarts/telephony-sms/send.md)**|Azure Communication Services SMS 클라이언트 라이브러리를 사용하면 .NET 및 JavaScript 애플리케이션에서 SMS 메시지를 보내고 받을 수 있습니다.|
|**[음성 및 화상 통화 시작](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services를 사용하면 통화 클라이언트 라이브러리를 사용하여 음성 및 화상 통화를 앱에 추가할 수 있습니다. 이 라이브러리는 WebRTC를 통해 구동되며 애플리케이션 내에서 피어 투 피어, 멀티미디어, 실시간 통신을 설정할 수 있도록 합니다.|
|**[채팅 시작](./quickstarts/chat/get-started.md)**|Azure Communication Services 채팅 클라이언트 라이브러리를 사용하여 실시간 채팅을 애플리케이션에 통합할 수 있습니다.|


## <a name="samples"></a>샘플

다음 샘플에서는 Azure Communication Services 클라이언트 라이브러리의 엔드투엔드 사용률을 보여 줍니다. 이러한 샘플을 사용하여 사용자 고유의 Communication Services 솔루션을 부트스트랩합니다.
<br>

| 샘플 이름                               | 설명                           |
|---                                    |---                                   |
|**[그룹 통화 주인공 샘플](./samples/calling-hero-sample.md)**|Communication Services 클라이언트 라이브러리를 사용하여 그룹 통화 환경을 빌드하는 방법을 확인합니다.|
|**[그룹 채팅 주인공 샘플](./samples/chat-hero-sample.md)**|Communication Services 클라이언트 라이브러리를 사용하여 그룹 채팅 환경을 빌드하는 방법을 확인합니다.|


## <a name="platforms-and-client-libraries"></a>플랫폼 및 클라이언트 라이브러리

다음 리소스는 Azure Communication Services 클라이언트 라이브러리에 대해 알아보는 데 도움이 됩니다.

| 리소스                               | 설명                           |
|---                                    |---                                   |
|**[클라이언트 라이브러리 및 REST API](./concepts/sdk-options.md)**|Azure Communication Services 기능은 개념적으로 각각 클라이언트 라이브러리로 표시되는 6가지 영역으로 구성됩니다. 실시간 통신 요구 사항에 따라 사용할 클라이언트 라이브러리를 결정할 수 있습니다.|
|**[통화 클라이언트 라이브러리 개요](./concepts/voice-video-calling/calling-sdk-features.md)**|Communication Services 통화 클라이언트 라이브러리에 대한 개요를 검토합니다.|
|**[채팅 클라이언트 라이브러리 개요](./concepts/chat/sdk-features.md)**|Communication Services 채팅 클라이언트 라이브러리에 대한 개요를 검토합니다.|
|**[SMS 클라이언트 라이브러리 개요](./concepts/telephony-sms/sdk-features.md)**|Communication Services SMS 클라이언트 라이브러리에 대한 개요를 검토합니다.|

## <a name="compare-azure-communication-services"></a>Azure Communication Services 비교

현재 Communication Services와 직접 상호 운용할 수 없는 활용을 고려할 수 있는 두 가지 다른 Microsoft 통신 제품이 있습니다.

 - [Microsoft Graph 클라우드 통신 API](https://docs.microsoft.com/graph/cloud-communications-concept-overview)를 사용하면 조직에서 M365 라이선스가 있는 Azure Active Directory 사용자와 연결된 통신 환경을 구축할 수 있습니다. 이는 Azure Active Directory에 연결된 애플리케이션에 적합하거나 Microsoft Teams에서 생산성 환경을 확장하려는 경우에 적합합니다. 또한 [Teams 환경](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true) 내에서 애플리케이션 및 사용자 지정을 빌드하는 API도 있습니다.

 - [Azure PlayFab 파티](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/)는 대기 시간이 짧은 채팅 및 데이터 통신을 게임에 간편하게 추가할 수 있습니다. Communication Services를 사용하여 게임 채팅 및 네트워킹 시스템을 제공할 수 있지만, PlayFab은 맞춤형 옵션이며 Xbox에서 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

 - [Communication Services 리소스 만들기](./quickstarts/create-communication-resource.md)
