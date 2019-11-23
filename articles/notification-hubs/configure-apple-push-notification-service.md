---
title: Configure Apple Push Notification Service in Azure Notification Hubs | Microsoft Docs
description: Learn how to configure an Azure notification hub with Apple Push Notification Service (APNS) settings.
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
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406122"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure Apple Push Notification Service (APNS) settings for a notification hub in the Azure portal
This article shows you how to configure Apple Push Notification Service (APNS) settings for an Azure notification hub by using the Azure portal. 

## <a name="prerequisites"></a>전제 조건
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요. 

## <a name="configure-apple-push-notification-service"></a>Configure Apple Push Notification Service

The following procedure gives you steps to configure Apple Push Notification Service (APNS) settings for a notification hub:

1. In the Azure portal, on the **Notification Hub** page, select **Apple (APNS)** on the left menu.

1. **인증 모드**의 경우 **인증서** 또는 **토큰**을 선택합니다.

   a. **인증서**를 선택한 경우:
   * 파일 아이콘을 선택한 다음, 업로드할 *.p12* 파일을 선택합니다.
   * 암호를 입력합니다.
   * **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 인증서 구성 스크린샷](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. **토큰**을 선택한 경우:

   * **키 ID**, **번들 ID**, **팀 ID** 및 **토큰**에 대한 값을 입력합니다.
   * **샌드박스** 모드를 선택합니다. 또는 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려면 **프로덕션** 모드를 선택합니다.

     ![Azure Portal의 APNS 토큰 구성 스크린샷](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>다음 단계
For a tutorial with step-by-step instructions for pushing notifications to iOS devices, see the following article: [Push notifications to iOS devices by using Notification Hubs and APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
