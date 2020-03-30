---
title: Azure 알림 허브를 통해 APNS VOIP 알림 보내기
description: Azure 알림 허브를 통해 APNS VOIP 알림을 보내는 방법(공식적으로 지원되지 않음)을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80146889"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>알림 허브를 통해 APNS VOIP 사용(공식적으로 지원되지 않음).

Azure 알림 허브를 통해 APNS VOIP 알림을 사용할 수 있습니다. 그러나 이 시나리오에 대한 공식적인 지원은 없습니다.

## <a name="considerations"></a>고려 사항

알림 허브를 통해 APNS VOIP 알림을 계속 전송하도록 선택한 경우 다음 제한 사항에 유의하십시오.

- VOIP 알림을 보내려면 `apns-topic` 헤더를 응용 프로그램 번들 `.voip` ID + 접미사로 설정해야 합니다. 예를 들어 번들 ID가 `com.microsoft.nhubsample`있는 샘플 `apns-topic` 앱의 경우 헤더를 다음으로 설정해야 합니다.`com.microsoft.nhubsample.voip.`

   이 메서드는 Azure Notification Hubs에서 잘 작동 하지 않습니다., 응용 프로그램의 번들 ID 허브의 APNS 자격 증명의 일부로 구성 해야 하 고 값을 변경할 수 없습니다 때문에. 또한 알림 허브는 런타임에 `apns-topic` 헤더 값을 재정의할 수 없습니다.

   VOIP 알림을 보내려면 앱 번들 ID로 `.voip` 별도의 알림 허브를 구성해야 합니다.

- VOIP 알림을 보내려면 `apns-push-type` 헤더를 값으로 `voip`설정해야 합니다.

   iOS 13으로 전환하는 고객을 돕기 위해 알림 허브는 `apns-push-type` 헤더에 대한 올바른 값을 추론하려고 시도합니다. 추론 논리는 표준 알림을 위반하지 않도록 하기 위해 의도적으로 간단합니다. 안타깝게도 이 방법은 VOIP 알림을 표준 알림과 동일한 규칙을 따르지 않는 특별한 경우로 취급하기 때문에 VOIP 알림에 문제가 발생합니다.

   VOIP 알림을 보내려면 `apns-push-type` 헤더에 대한 명시적 값을 지정해야 합니다.

- 알림 허브는 Apple에서 설명한 대로 APNS 페이로드를 4KB로 제한합니다. VOIP 알림의 경우 Apple은 최대 5KB의 페이로드를 허용합니다. 알림 허브는 표준 알림과 VOIP 알림을 구분하지 않습니다. 따라서 모든 알림은 4KB로 제한됩니다.

   VOIP 알림을 보내려면 4KB 페이로드 크기 제한을 초과해서는 안 됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 링크를 참조하십시오.

- [VOIP `apns-topic` `apns-push-type` 알림에 대한 특수 한 경우를 포함하여 헤더 및 값에 대한 설명서.](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns)

- [페이로드 크기 제한에 대한 설명서.](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)

- [iOS 13에 대한 알림 허브 업데이트](push-notification-updates-ios-13.md#apns-push-type).
