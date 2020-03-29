---
title: Azure 알림 허브에서 APNS에 대한 토큰 기반(HTTP/2) 인증 | 마이크로 소프트 문서
description: APNS에 새 토큰 인증을 사용하는 방법을 알아봅니다.
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
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263815"
---
# <a name="token-based-http2-authentication-for-apns"></a>APNS에 대한 토큰 기반(HTTP/2) 인증

## <a name="overview"></a>개요

이 문서에서는 토큰 기반 인증과 함께 새 APNS HTTP/2 프로토콜을 사용하는 방법을 설명합니다.

새 프로토콜을 사용할 경우의 주요 이점은 다음과 같습니다.

* 토큰 생성은 인증서에 비해 비교적 간단합니다.
* 만료 날짜가 없습니다. 인증 토큰과 해당 해지를 제어할 수 있습니다.
* 이제 페이로드가 최대 4KB입니다.
* 피드백이 동기화됩니다.
* 당신은 애플의 최신 프로토콜에 - 인증서는 여전히 이진 프로토콜을 사용, 이는 사용 중단으로 표시

이 새 메커니즘을 사용하면 다음 두 단계로 수행할 수 있습니다.

* Apple 개발자 계정 포털에서 필요한 정보를 가져옵니다.
* 알림 허브를 새 정보로 구성합니다.

이제 알림 허브가 APNS와 함께 새 인증 시스템을 사용하도록 설정되었습니다.

APNS에 대한 인증서 자격 증명을 사용하여 마이그레이션한 경우 토큰 속성은 시스템에서 인증서를 덮어쓰지만 응용 프로그램은 계속해서 원활하게 알림을 받습니다.

## <a name="obtaining-authentication-information-from-apple"></a>Apple에서 인증 정보 가져오기

토큰 기반 인증을 사용하려면 Apple 개발자 계정의 다음 속성이 필요합니다.

### <a name="key-identifier"></a>키 식별자

키 식별자는 Apple 개발자 계정의 **인증서, 식별자 & 프로필**아래의 **키** 페이지에서 가져올 수 있습니다.

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>응용 프로그램 식별자 및 응용 프로그램 이름

응용 프로그램 이름과 식별자는 개발자 계정의 **인증서, 식별자 & 프로필** 페이지에서도 사용할 수 있습니다.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>.NET SDK 또는 Azure 포털을 통해 구성

[최신 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)또는 Azure 포털에서 토큰 기반 인증을 사용하도록 허브를 구성할 수 있습니다. 포털에서 토큰 기반 인증을 사용하려면 Azure 포털에 로그인하고 알림 허브의 **설정 > Apple(앱)** 패널로 이동합니다. **인증 모드** 속성에서 **토큰을** 선택하여 모든 관련 토큰 속성으로 허브를 업데이트합니다.

![토큰 구성](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Apple 개발자 계정에서 검색한 속성을 입력합니다.
* 응용 프로그램 모드(프로덕션 또는 **샌드박스)를****선택합니다.**
* **저장** 단추를 클릭하여 APNS 자격 증명을 업데이트합니다.

토큰 기반 자격 증명은 다음 필드로 구성됩니다.

* **키 ID**: Apple 개발자 포털에서 생성된 개인 키의 식별자; 예를 들어, `2USFGKSKLT`을 참조하십시오.
* **팀 ID**: "접두사" 또는 "앱 접두사"라고도 합니다. Apple 개발자 포털의 조직에 대한 식별자입니다. 예를 들어, `S4V3D7CHJR`을 참조하십시오.
* **번들 ID**: "앱 ID"라고도 합니다. 응용 프로그램의 번들 식별자입니다. 예를 들어, `com.microsoft.nhubsample2019`을 참조하십시오. 많은 앱에 하나의 키를 사용할 수 있습니다. 이 값은 `apns-topic` 알림을 보낼 때 HTTP 헤더에 매핑되며 특정 응용 프로그램을 대상으로 하는 데 사용됩니다.
* **토큰**: "키" 또는 "개인 키"라고도 합니다. 이는 Apple 개발자 포털에서 생성된 .p8 파일에서 가져옵니다. 키에 APNS가 활성화되어 있어야 합니다(키를 생성할 때 Apple 개발자 포털에서 선택됨). NH 포털/API에 제공할 때 PEM 헤더/바닥글에서 제거된 값이 있어야 합니다.
* **끝점**: 알림 허브 포털 블레이드의 토글과 API의 문자열 필드입니다. 유효한 값은 `https://api.push.apple.com` 또는 `https://api.sandbox.push.apple.com`입니다. 알림 허브는 프로덕션 환경 또는 샌드박스 환경에 이 값을 사용하여 알림을 보냅니다. 앱의 `aps-environment` 사용 권한과 일치해야 하며, 그렇지 않으면 생성된 APNS 장치 토큰이 환경과 일치하지 않으며 알림이 전송되지 않습니다.

다음은 올바른 사용을 설명하는 코드 샘플입니다.

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
* [Azure 포털에서 알림 허브 구성](create-notification-hub-portal.md)
