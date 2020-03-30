---
title: Azure 알림 허브 iOS 13 업데이트 | 마이크로 소프트 문서
description: Azure 알림 허브의 iOS 13 주요 변경 사항에 대해 알아보기
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228137"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>iOS 13에 대한 Azure 알림 허브 업데이트

애플은 최근 공개 푸시 서비스에 몇 가지 변경 사항을 변경; 변경 사항은 주로 iOS 13 및 Xcode의 릴리스와 일치합니다. 이 문서에서는 이러한 변경 내용이 Azure 알림 허브에 미치는 영향을 설명합니다.

## <a name="apns-push-payload-changes"></a>APNS 푸시 페이로드 변경

### <a name="apns-push-type"></a>APNS 푸시 타입

이제 Apple은 개발자가 APNS API의 새 `apns-push-type` 헤더를 통해 알림을 경고 또는 백그라운드 알림으로 식별하도록 요구합니다. [Apple의 문서에](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)따르면 : "이 헤더의 가치는 알림 페이로드의 내용을 정확하게 반영해야합니다. 불일치가 있거나 필수 시스템에서 헤더가 없는 경우 APN은 오류를 반환하거나 알림 배달을 지연하거나 완전히 삭제할 수 있습니다."

이제 개발자는 Azure 알림 허브를 통해 알림을 보내는 응용 프로그램에서 이 헤더를 설정해야 합니다. 기술적 제한으로 인해 고객은 이 특성을 포함하는 요청이 있는 APNS 자격 증명에 토큰 기반 인증을 사용해야 합니다. APNS 자격 증명에 인증서 기반 인증을 사용하는 경우 토큰 기반 인증을 사용하는 것으로 전환해야 합니다.

다음 코드 샘플에서는 Azure 알림 허브를 통해 전송된 알림 요청에서 이 헤더 특성을 설정하는 방법을 보여 준다.

#### <a name="template-notifications---net-sdk"></a>템플릿 알림 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>네이티브 알림 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>직접 REST 호출

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

이 전환 중에 Azure Notification Hubs가 `apns-push-type` 설정되지 않은 알림을 검색할 때 서비스는 알림 요청에서 푸시 유형을 유추하고 값을 자동으로 설정합니다. 필요한 헤더를 설정하려면 토큰 기반 인증을 사용하도록 Azure 알림 허브를 구성해야 합니다. 자세한 내용은 [APNS에 대한 토큰 기반(HTTP/2) 인증을](notification-hubs-push-notification-http2-token-authentification.md)참조하십시오.

## <a name="apns-priority"></a>APNS 우선 순위

또 다른 사소한 변경이지만 알림을 보내는 백 엔드 응용 프로그램을 변경해야 하는 변경 `apns-priority` 사항은 백그라운드 알림의 경우 헤더를 이제 5로 설정해야 하는 요구 사항입니다. 많은 응용 프로그램에서 `apns-priority` 헤더를 10(즉각적인 배달을 표시)으로 설정하거나 설정하지 않고 기본값(10)을 가져옵니다.

이 값을 10으로 설정하면 더 이상 백그라운드 알림이 허용되지 않으며 각 요청에 대한 값을 설정해야 합니다. 이 값이 없는 경우 Apple은 백그라운드 알림을 제공하지 않습니다. 예를 들어:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK 변경 사항

수년 동안 iOS 개발자는 `description` 푸시 `deviceToken` 토큰 대리자로 전송된 데이터의 속성을 사용하여 백 엔드 응용 프로그램이 장치에 알림을 보내는 데 사용하는 푸시 토큰을 추출했습니다. Xcode 11에서는 `description` 해당 특성이 다른 형식으로 변경되었습니다. 이제 개발자가 이 특성에 사용한 기존 코드가 손상되었습니다. 이러한 변경 사항을 수용하기 위해 Azure 알림 허브 SDK를 업데이트했기 때문에 응용 프로그램에서 사용하는 SDK를 [Azure 알림 허브 iOS SDK](https://github.com/Azure/azure-notificationhubs-ios)버전 2.0.4 이상으로 업데이트하십시오.
