---
title: Azure Notification Hubs iOS 13 업데이트 | Microsoft Docs
description: Azure Notification Hubs의 iOS 13 주요 변경 내용에 대해 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88998053"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>iOS 13용 Azure Notification Hubs 업데이트

최근 Apple에서 공용 푸시 서비스를 일부 변경했습니다. 변경 내용은 대부분 iOS 13 및 Xcode 릴리스에 맞춰져 있습니다. 이 문서에서는 해당 변경이 Azure Notification Hubs에 미치는 영향에 대해 설명합니다.

## <a name="apns-push-payload-changes"></a>APNS 푸시 페이로드 변경

### <a name="apns-push-type"></a>APNS 푸시 유형

이제 Apple에서는 개발자가 APNS API의 새로운 `apns-push-type` 헤더를 통해 알림을 경고 또는 백그라운드 알림으로 식별해야 합니다. [Apple 설명서](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)에 따르면 "이 헤더 값은 알림 페이로드의 내용을 정확하게 반영해야 합니다. 일치하지 않는 경우 또는 필요한 시스템에서 헤더가 누락된 경우 APN에서 오류를 반환하거나 알림 전달을 지연하거나 또는 모두 삭제할 수 있습니다."

이제 개발자는 Azure Notification Hubs를 통해 알림을 보내는 애플리케이션에서 이 헤더를 설정해야 합니다. 기술적인 제한으로 인해 고객은 이 속성을 포함하는 요청이 있는 APNS 자격 증명에 토큰 기반 인증을 사용해야 합니다. APNS 자격 증명에 인증서 기반 인증을 사용하는 경우 토큰 기반 인증을 사용하도록 전환해야 합니다.

다음의 코드 샘플은 Azure Notification Hubs를 통해 전송된 알림 요청에서 이 헤더 속성을 설정하는 방법을 보여줍니다.

#### <a name="template-notifications---net-sdk"></a>템플릿 알림 - .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>원시 알림 - .NET SDK

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

이 전환 작업을 진행하는 동안 Azure Notification Hubs에 `apns-push-type` 집합이 없는 알림이 감지되면 서비스는 알림 요청에서 푸시 유형을 유추하고 값을 자동으로 설정합니다. 토큰 기반 인증을 사용하여 필요한 헤더를 설정하도록 Azure Notification Hubs를 구성해야 합니다. 자세한 내용은 [APNS용 토큰 기반(HTTP/2) 인증](./notification-hubs-push-notification-http2-token-authentication.md)을 참조하세요.

## <a name="apns-priority"></a>APNS 우선 순위

또 다른 사소한 변경 사항은 알림을 보내는 백 엔드 애플리케이션을 변경해야 하는 경우, 이제 `apns-priority` 헤더를 5로 설정해야 한다는 것입니다. 많은 애플리케이션에서 `apns-priority` 헤더를 10으로 설정(즉시 제공 표시)하거나 설정하지 않고 기본값(또한 10)을 가져옵니다.

더 이상 백그라운드 알림에 이 값을 10으로 설정하는 것은 허용되지 않으며 각 요청에 대한 값을 설정해야 합니다. 이 값이 누락된 경우 Apple은 백그라운드 알림을 제공하지 않습니다. 예를 들면 다음과 같습니다.

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>SDK 변경 내용

수년 동안 iOS 개발자는 푸시 토큰 대리자에게 전송된 `deviceToken` 데이터의 `description` 속성을 사용하여 백 엔드 애플리케이션이 디바이스에 알림을 보내는데 사용하는 푸시 토큰을 추출했습니다. Xcode 11을 사용하면 해당 `description` 속성이 다른 형식으로 변경됩니다. 개발자가 이 속성에 사용했던 기존 코드는 이제 중단됩니다. 이 변경 사항을 수용하기 위해 Azure Notification Hubs SDK를 업데이트 했으므로 애플리케이션에서 사용하는 SDK를 [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios) 2.0.4 이상 버전으로 업데이트하세요.
