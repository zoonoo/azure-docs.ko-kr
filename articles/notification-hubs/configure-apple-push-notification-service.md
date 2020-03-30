---
title: Azure 알림 허브에서 Apple 푸시 알림 서비스 구성 | 마이크로 소프트 문서
description: Apple 푸시 알림 서비스(APNS) 설정을 사용하여 Azure 알림 허브를 구성하는 방법에 대해 알아봅니다.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127525"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure 포털의 알림 허브에 대한 Apple 푸시 알림 서비스 설정 구성

이 문서에서는 Azure 포털을 사용하여 Azure 알림 허브에 대한 APPLE 푸시 알림 서비스(APNS) 설정을 구성하는 방법을 보여 주며, 이 문서에서는 

## <a name="prerequisites"></a>사전 요구 사항
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요. 

## <a name="configure-apple-push-notification-service"></a>애플 푸시 알림 서비스 구성

다음 절차에서는 알림 허브에 대한 APNS(Apple Push Notification Service) 설정을 구성하는 단계를 제공합니다.

1. Azure 포털에서 알림 **허브** 페이지에서 왼쪽 메뉴에서 **Apple(APNS)을** 선택합니다.

1. **인증 모드**의 경우 **인증서** 또는 **토큰**을 선택합니다.

   a. **인증서**를 선택한 경우:
   * 파일 아이콘을 선택한 다음, 업로드할 *.p12* 파일을 선택합니다.
   * 암호를 입력합니다.
   * **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 인증서 구성 스크린샷](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. **토큰**을 선택한 경우:

   * **키 ID,** **번들 ID,** **팀 ID**및 **토큰에**대한 값을 입력합니다.
   * **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 토큰 구성 스크린샷](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>다음 단계
iOS 장치에 알림을 푸시하기 위한 단계별 지침이 포함된 자습서의 경우 [알림 허브 및 APNS를 사용하여 iOS 장치에 알림을 푸시하는](notification-hubs-ios-apple-push-notification-apns-get-started.md) 다음 문서를 참조하세요.
