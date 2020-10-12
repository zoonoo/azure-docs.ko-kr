---
title: Azure Notification Hubs |에서 Google Firebase 클라우드 메시징 구성 Microsoft Docs
description: Google Firebase 클라우드 메시징 설정을 사용 하 여 Azure 알림 허브를 구성 하는 방법에 대해 알아봅니다.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760841"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure Portal에서 알림 허브에 대 한 Google Firebase 설정 구성

이 문서에서는 Azure Portal를 사용 하 여 Azure 알림 허브에 대 한 Google Firebase Cloud Messaging (FCM) 설정을 구성 하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>필수 구성 요소

알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요.

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Google Firebase 클라우드 메시징 구성 (FCM)

다음 절차에서는 알림 허브에 대 한 Google Firebase Cloud Messaging (FCM) 설정을 구성 하는 단계를 설명 합니다.

1. Azure Portal의 **알림 허브** 페이지에서 왼쪽 메뉴에 있는 **Google (GCM/FCM)** 을 선택 합니다.
2. 앞에서 저장한 FCM 프로젝트의 **API 키**를 붙여넣습니다.
3. **저장**을 선택합니다.

   ![Google FCM에 대한 Notification Hubs를 구성하는 방법을 보여주는 스크린샷](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>다음 단계

Azure Notification Hubs 및 Google Firebase 클라우드 메시징을 사용 하 여 Android 장치에 알림을 보내는 방법에 대 한 단계별 지침이 포함 된 자습서는 [Notification Hubs 및 GOOGLE FCM를 사용 하 여 android 장치에 푸시 알림 보내기](notification-hubs-android-push-notification-google-fcm-get-started.md)를 참조 하세요.
