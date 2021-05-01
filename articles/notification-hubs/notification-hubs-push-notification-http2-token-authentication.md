---
title: Azure Notification Hubs의 APNS에 대한 토큰 기반(HTTP/2) 인증 | Microsoft Docs
description: APNS에 대한 새 토큰 인증을 사용하는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460468"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS에 대한 토큰 기반(HTTP/2) 인증

## <a name="overview"></a>개요

이 문서에서는 토큰 기반 인증을 지원하는 새로운 APNS HTTP/2 프로토콜을 사용하는 방법을 설명합니다.

새 프로토콜을 사용할 경우의 주요 이점은 다음과 같습니다.

* 인증서에 비해 토큰 생성이 상대적으로 간단합니다.
* 만료 날짜가 없습니다. 인증 토큰과 해당 해지를 제어할 수 있습니다.
* 이제 페이로드가 최대 4KB입니다.
* 피드백이 동기화됩니다.
* Apple의 최신 프로토콜을 사용합니다. 하지만 인증서는 여전히 이진 프로토콜을 사용하고 있으며 이 프로토콜은 더 이상 사용되지 않습니다.

이 새로운 메커니즘은 다음과 같은 두 단계로 수행할 수 있습니다.

* Apple 개발자 계정 포털에서 필요한 정보를 가져옵니다.
* 새로운 정보로 알림 허브를 구성합니다.

이제 Notification Hubs는 APNS에서 새로운 인증 시스템을 사용하도록 설정됩니다.

APNS에 대한 인증서 자격 증명을 사용하여 마이그레이션한 경우 토큰 속성이 당사 시스템에서 사용자의 자격 증명을 덮어쓰지만 애플리케이션은 계속해서 원활하게 알림을 받습니다.

## <a name="obtaining-authentication-information-from-apple"></a>Apple에서 인증 정보 가져오기

토큰 기반 인증을 사용하려면 Apple 개발자 계정에서 다음 속성을 가져와야 합니다.

### <a name="key-identifier"></a>키 식별자

Apple 개발자 계정의 **Certificates, Identifiers & Profiles(인증서, 식별자 및 프로파일)** 의 **Keys(키)** 페이지에서 키 식별자를 가져올 수 있습니다.

![인증서](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![식별자](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>애플리케이션 식별자 및 애플리케이션 이름

개발자 계정의 **Certificates, Identifiers & Profiles(인증서, 식별자 및 프로파일)** 페이지에서도 애플리케이션 이름 및 식별자를 사용할 수 있습니다.

![인증서 및 ID](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>.NET SDK 또는 Azure Portal을 통해 구성

[최신 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)를 사용하여 또는 Azure Portal에서 토큰 기반 인증을 사용하도록 허브를 구성할 수 있습니다. 포털에서 토큰 기반 인증을 사용하도록 설정하려면 Azure Portal에 로그인하여 알림 허브의 **설정 > Apple(APNS)** 창으로 이동합니다. **인증 모드** 속성에서 **토큰** 을 선택하여 관련된 모든 토큰 속성으로 허브를 업데이트합니다.

![토큰 구성](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple 개발자 계정에서 검색한 속성을 입력합니다.
* 애플리케이션 모드(**프로덕션** 또는 **샌드박스**)를 선택합니다.
* **저장** 단추를 클릭하여 APNS 자격 증명을 업데이트합니다.

토큰 기반 자격 증명은 다음 필드로 구성됩니다.

* **키 ID**: Apple 개발자 포털에서 생성된 프라이빗 키의 식별자입니다. 예를 들면 `2USFGKSKLT`입니다.
* **팀 ID**: "접두사" 또는 "앱 접두사"라고도 합니다. Apple 개발자 포털의 조직에 대한 식별자입니다. 예를 들면 `S4V3D7CHJR`입니다.
* **번들 ID**: "앱 ID"라고도 합니다. 애플리케이션에 대한 번들 식별자입니다. 예를 들면 `com.example.myapp`입니다. 한 앱에 하나의 키만 사용할 수 있습니다. 이 값은 알림을 보낼 때 `apns-topic` HTTP 헤더로 매핑되며 특정 애플리케이션을 대상으로 지정하는 데 사용됩니다. `apns-topic` 값은 명시적으로 설정할 수 없습니다.
* **토큰**: "키" 또는 "프라이빗 키"라고도 합니다. 이는 Apple 개발자 포털에서 생성된 .p8 파일에서 가져옵니다. 키에 APNS를 사용하도록 설정되어 있어야 합니다(키 생성 시 Apple 개발자 포털에서 선택됨). NH 포털/API에 값을 제공하는 경우 값에서 PEM 헤더/바닥글을 제거해야 합니다.
* **엔드포인트**: Notification Hubs 포털 블레이드의 토글이며 API의 문자열 필드입니다. 유효한 값은 `https://api.development.push.apple.com:443/3/device` 또는 `https://api.sandbox.push.apple.com:443/3/device`입니다. Notification Hubs는 이 값을 사용하여 프로덕션 또는 샌드박스 환경에 알림을 보냅니다. 이는 앱의 `aps-environment` 자격과 일치해야 합니다. 그렇지 않으면 생성된 APNS 디바이스 토큰이 환경과 일치하지 않으며 알림 전송에 실패합니다.

올바른 사용법을 보여주는 코드 샘플은 다음과 같습니다.

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal을 사용하여 Azure 알림 허브 만들기](create-notification-hub-portal.md)
* [Azure Portal에서 알림 허브 구성](create-notification-hub-portal.md)
