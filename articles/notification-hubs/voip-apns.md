---
title: Azure Notification Hubs를 사용하여 APNS VOIP 알림 보내기
description: Azure Notification Hubs를 통해 APNS VOIP 알림을 보내는 방법을 알아봅니다 (공식적으로 지원되지 않음).
author: sethmanheim
ms.author: sethm
ms.date: 3/23/2020
ms.topic: how-to
ms.service: notification-hubs
ms.openlocfilehash: c99af881b8f93b75633741c2352dc5df17dd2963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80146889"
---
# <a name="use-apns-voip-through-notification-hubs-not-officially-supported"></a>Notification Hubs를 통해 APNS VOIP 사용 (공식적으로 지원되지 않음)

Azure Notification Hubs를 통해 APNS VOIP 알림을 사용할 수 있습니다. 그러나 이 시나리오는 공식적으로 지원되지는 않습니다.

## <a name="considerations"></a>고려 사항

Notification Hubs를 통해 APNS VOIP 알림을 보내도록 선택하는 경우에는 다음과 같은 제한 사항을 고려해야 합니다.

- VOIP 알림을 보내려면 `apns-topic` 헤더를 애플리케이션 번들 ID + `.voip` 접미사로 설정해야 합니다. 예를 들어 번들 ID `com.microsoft.nhubsample`을 사용하는 샘플 앱의 경우 `apns-topic` 헤더를 `com.microsoft.nhubsample.voip.`으로 설정해야 합니다.

   앱의 번들 ID는 허브의 APNS 자격 증명의 일부로 구성되어야 하고, 값을 변경할 수 없기 때문에 이 방법은 Azure Notification Hubs에서 잘 작동하지 않습니다. 또한 Notification Hubs는 런타임에 `apns-topic` 헤더의 값을 재정의할 수 없습니다.

   VOIP 알림을 보내려면 `.voip` 앱 번들 ID를 사용하여 별도의 알림 허브를 구성해야 합니다.

- VOIP 알림을 보내려면 `apns-push-type` 헤더를 값 `voip`로 설정해야 합니다.

   고객이 iOS 13으로 전환하는 데 도움이 되도록 Notification Hubs `apns-push-type` 헤더의 올바른 값을 유추하려고 시도합니다. 유추 논리는 표준 알림이 중단되지 않도록 하기 위한 노력에서 의도적으로 간단합니다. 유감스럽게도 이 방법을 사용하면 Apple에서 VOIP 알림이 표준 알림과 동일한 규칙을 따르지 않는 특별한 사례로 처리되기 때문에 VOIP 알림으로 인한 문제가 발생합니다.

   VOIP 알림을 보내려면 `apns-push-type` 헤더에 대해 명시적 값을 지정해야 합니다.

- Notification Hubs는 Apple에서 설명한 대로 APNS 페이로드를 4KB로 제한합니다. VOIP 알림의 경우 Apple은 최대 5KB의 페이로드를 허용합니다. Notification Hubs는 표준 알림과 VOIP 알림을 구분하지 않습니다. 따라서 모든 알림은 4KB로 제한됩니다.

   VOIP 알림을 보내려면 4KB 페이로드 크기 제한을 초과하면 안됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 링크를 참조하십시오.

- [VOIP 알림에 대한 특수한 경우 포함한 `apns-topic` 및 `apns-push-type` 헤더와 값에 대한 설명서](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns).

- [페이로드 크기 제한에 대한 설명서](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)입니다.

- [iOS 13용 Notification Hubs 업데이트](push-notification-updates-ios-13.md#apns-push-type).
