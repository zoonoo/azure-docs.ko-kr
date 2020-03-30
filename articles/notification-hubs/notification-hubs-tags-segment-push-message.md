---
title: Azure 알림 허브에서 라우팅 및 태그 식
description: Azure 알림 허브에 대한 식을 라우팅하고 태그를 붙이는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 2432ac41645e373ea3a87ff7e69ef02a4e30c81d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062307"
---
# <a name="routing-and-tag-expressions"></a>라우팅 및 태그 식

## <a name="overview"></a>개요

태그 식을 사용하면 Notification Hubs를 통해 푸시 알림을 보내는 경우에 특정한 디바이스 집합, 보다 구체적으로는 등록을 대상으로 지정할 수 있습니다.

## <a name="targeting-specific-registrations"></a>특정 등록을 대상으로 지정

특정한 알림 등록을 대상으로 지정하는 유일한 방법은 등록에 태그를 연결한 후에 해당 태그를 대상으로 지정하는 것입니다. [등록 관리에서](notification-hubs-push-notification-registration-management.md)설명한 대로 푸시 알림을 받으려면 앱이 알림 허브에 장치 핸들을 등록해야 합니다. 앱이 알림 허브에 등록을 생성하면 응용 프로그램 백 엔드에서 푸시 알림을 보낼 수 있습니다. 애플리케이션 백 엔드는 다음과 같은 방식으로 등록이 특정 알림을 대상으로 지정하도록 선택할 수 있습니다.

1. **브로드캐스트**: 알림 허브 내의 모든 등록이 알림을 수신합니다.
2. **태그**: 지정된 태그를 포함하는 모든 등록이 알림을 수신합니다.
3. **태그 식**: 등록의 태그 집합이 지정된 식과 일치하는 모든 등록이 알림을 수신합니다.

## <a name="tags"></a>태그들

태그는 영숫자와 다음 비영숫자 문자를 포함하는 최대 120자 까지의 모든 문자열이 될`@`수`#`있습니다.`.``:``-``_` 다음 예제는 특정 음악 그룹에 대한 토스트 알림을 받을 수 있는 애플리케이션을 보여줍니다. 이 시나리오에서 알림을 라우팅 하는 간단한 방법은 다음 그림에서와 같이 다른 밴드를 나타내는 태그로 등록에 레이블을 지정하는 것입니다.

![태그 개요](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags.png)

그림에서 **비틀즈로** 태그된 메시지는 **비틀즈**태그에 등록된 태블릿에만 도달합니다.

태그에 대한 등록을 생성하는 내용은 [등록 관리](notification-hubs-push-notification-registration-management.md)를 참조하세요.

[Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK에 포함된 `Microsoft.Azure.NotificationHubs.NotificationHubClient` 클래스의 알림 메서드를 사용하여 태그에 알림을 보낼 수 있습니다. Node.js 또는 푸시 알림 REST API를 사용할 수도 있습니다.  다음은 SDK 사용 예입니다.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

태그는 미리 프로비전되어서는 안 되며 여러 앱별 개념을 참조할 수 있습니다. 예를 들어, 이 예제 애플리케이션 사용자는 밴드에 대한 댓글을 추가할 수 있고 자신이 좋아하는 밴드에 대한 댓글뿐만 아니라 친구가 추가하는(어떤 밴드에 관한 댓글이든 상관없이) 댓글에 대해서도 토스트를 수신하고자 합니다. 다음 그림은 이 시나리오의 예를 강조 합니다.

![친구 태그](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags2.png)

이 예제에서 Alice는 비틀즈업데이트에 관심이 있으며 Bob은 Wailers 업데이트에 관심이 있습니다. 밥은 찰리의 의견에도 관심이 있고, 찰리는 와일러스에 관심이 있습니다. 비틀즈에 대한 찰리의 코멘트에 대한 알림이 전송되면 알림 허브는 앨리스와 밥 모두에게 전송됩니다.

태그(예: `band_Beatles` 또는)에서 `follows_Charlie`여러 문제를 인코딩할 수 있지만 태그는 간단한 문자열이며 값이 있는 속성은 아닙니다. 등록은 특정 태그의 유무에 따라 일치합니다.

태그를 사용하여 흥미가 있는 그룹에 알림을 보내는 방법에 대한 단계별 전체 자습서는 [속보](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)를 참조하세요.

> [!NOTE]
> Azure Notification Hubs는 등록당 최대 60개 태그를 지원합니다.

## <a name="using-tags-to-target-users"></a>태그를 사용하여 사용자를 대상으로 지정하기

태그를 사용하는 또 다른 방법은 특정 사용자와 연결된 모든 장치를 식별하는 것입니다. 다음 그림과 같이 사용자 ID가 포함된 태그로 등록에 태그를 붙일 수 있습니다.

![사용자 태그](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags3.png)

그림에서 태그가 지정된 `user_Alice` 메시지는 로 태그된 `user_Alice`모든 장치에 도달합니다.

## <a name="tag-expressions"></a>태그 식

알림이 단일 태그가 아니라 태그를 사용하는 부울 식으로 식별된 등록 집합을 대상으로 해야 하는 경우가 있습니다.

보스턴에 있는 모든 사람에게 Red Sox와 Cardinals의 경기에 대해 미리 알림을 보내는 스포츠 애플리케이션을 생각해 보겠습니다. 클라이언트 앱이 팀과 지역 관심사에 대한 태그를 등록하면, 알림은 Red Sox 또는 Cardinals에 관심이 있는 보스턴의 모든 사람을 대상으로 하게 됩니다. 이 조건은 다음과 같은 부울 식으로 표현될 수 있습니다.

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![태그 식](./media/notification-hubs-tags-segment-push-message/notification-hubs-tags4.png)

태그 `AND` 식은 (),`&&`() `OR` `||`및 `NOT` (;`!` 괄호를 포함할 수도 있습니다. 연산자만 `OR` 사용하는 태그 식은 20개의 태그를 참조할 수 있습니다. `AND` 연산자가 있지만 `OR` 연산자가 없는 식은 10개의 태그를 참조할 수 없습니다. 그렇지 않으면 태그 표현식은 6개의 태그로 제한됩니다.

다음은 SDK를 사용하여 태그 식을 사용하여 알림을 보내는 예제입니다.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
