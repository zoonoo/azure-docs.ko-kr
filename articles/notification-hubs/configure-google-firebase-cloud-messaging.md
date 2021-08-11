---
title: Azure Notification Hubs에서 Google Firebase Cloud Messaging 구성 | Microsoft Docs
description: Google Firebase Cloud Messaging 설정을 통해 Azure 알림 허브를 구성하는 방법을 알아봅니다.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003568"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure Portal에서 알림 허브에 대한 Google Firebase 설정 구성

이 문서에서는 Azure Portal을 사용하여 Azure 알림 허브에 Google FCM(Firebase Cloud Messaging) 설정을 구성하는 방법을 보여줍니다.  

## <a name="prerequisites"></a>사전 요구 사항

알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요.

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Google FCM(Firebase Cloud Messaging) 구성

다음 프로시저는 알림 허브에 Google FCM(Firebase Cloud Messaging) 설정을 구성하는 단계를 설명합니다.

1. Azure Portal의 **알림 허브** 페이지 왼쪽 메뉴에서 **Google(GCM/FCM)** 을 선택합니다.
2. 앞에서 저장한 FCM 프로젝트의 **API 키** 를 붙여넣습니다.
3. **저장** 을 선택합니다.

   ![Google FCM에 대한 Notification Hubs를 구성하는 방법을 보여주는 스크린샷](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>다음 단계

Azure Notification Hubs 및 Google Firebase Cloud Messaging을 사용하여 Android 디바이스에 알림을 보내기 위한 단계별 지침을 담은 자습서는 [Notification Hubs 및 Google FCM을 사용하여 Android 디바이스에 푸시 알림 보내기](notification-hubs-android-push-notification-google-fcm-get-started.md)를 참조하세요.
