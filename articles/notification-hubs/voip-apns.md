---
title: Azure Notification Hubs를 사용 하 여 APNS VOIP 알림 보내기
description: Azure Notification Hubs를 통해 APNS VOIP 알림을 보내는 방법을 알아봅니다 (공식적으로 지원 되지 않음).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80146889"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Notification Hubs를 통해 APNS VOIP 사용 (공식적으로 지원 되지 않음)

Azure Notification Hubs을 통해 APNS VOIP 알림을 사용할 수 있습니다. 그러나이 시나리오는 공식적으로 지원 되지 않습니다.

## <a name="considerations"></a>고려 사항

Notification Hubs를 통해 APNS VOIP 알림을 보내도록 선택 하는 경우에는 다음과 같은 제한 사항을 고려해 야 합니다.

- VOIP 알림을 보내려면 `apns-topic` 헤더를 응용 프로그램 번들 ID + 접미사로 설정 해야 합니다 `.voip` . 예를 들어 번들 ID를 사용 하는 샘플 앱의 경우 `com.microsoft.nhubsample` `apns-topic` 헤더를로 설정 해야 합니다. `com.microsoft.nhubsample.voip.`

   앱의 번들 ID는 허브의 APNS 자격 증명의 일부로 구성 되어야 하 고 값을 변경할 수 없기 때문에이 방법은 Azure Notification Hubs에서 잘 작동 하지 않습니다. 또한 Notification Hubs는 런타임에 헤더의 값을 재정의할 수 없습니다 `apns-topic` .

   VOIP 알림을 보내려면 앱 번들 ID를 사용 하 여 별도의 알림 허브를 구성 해야 합니다 `.voip` .

- VOIP 알림을 보내려면 `apns-push-type` 헤더를 값으로 설정 해야 합니다 `voip` .

   고객이 iOS 13으로 전환 하는 데 도움이 되도록 Notification Hubs 헤더의 올바른 값을 유추 하려고 시도 합니다 `apns-push-type` . 유추 논리는 표준 알림이 중단 되지 않도록 의도적으로 간단 합니다. 그러나이 방법을 사용 하면 voip 알림이 표준 알림과 동일한 규칙을 따르지 않는 특별 한 사례로 처리 되기 때문에이 방법으로 인해 VOIP 알림이 발생 합니다.

   VOIP 알림을 보내려면 헤더에 대해 명시적 값을 지정 해야 합니다 `apns-push-type` .

- Notification Hubs는 Apple에서 설명한 대로 APNS 페이로드를 4kb로 제한 합니다. VOIP 알림의 경우 Apple은 최대 5KB의 페이로드를 허용 합니다. Notification Hubs은 표준 알림과 VOIP 알림을 구분 하지 않습니다. 따라서 모든 알림은 4kb로 제한 됩니다.

   VOIP 알림을 보내려면 4kb 페이로드 크기 제한을 초과 하면 안 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 링크를 참조하십시오.

- [ `apns-topic` 및 헤더와 값에 대 한 설명서 `apns-push-type` (VOIP 알림에 대 한 특수 한 경우 포함](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)).

- [페이로드 크기 제한에 대 한 설명서](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)입니다.

- [IOS 13의 업데이트를 Notification Hubs](push-notification-updates-ios-13.md#apns-push-type)합니다.
