---
title: Azure 알림 허브에서 Google Firebase 클라우드 메시징 구성 | 마이크로 소프트 문서
description: Google Firebase 클라우드 메시징 설정을 사용하여 Azure 알림 허브를 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127462"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure 포털에서 알림 허브에 대한 Google Firebase 설정 구성

이 문서에서는 Azure 포털을 사용하여 Azure 알림 허브에 대한 FCM(Google Firebase 클라우드 메시징) 설정을 구성하는 방법을 보여 주며 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요. 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>구글 파이어베이스 클라우드 메시징 구성 (FCM)

다음 절차에서는 알림 허브에 대한 Google Firebase 클라우드 메시징(FCM) 설정을 구성하는 단계를 제공합니다. 

1. Azure 포털에서 알림 **허브** 페이지에서 왼쪽 메뉴에서 **Google(GCM/FCM)을** 선택합니다. 
2. 앞에서 저장한 FCM 프로젝트의 **API 키**를 붙여넣습니다. 
3. **저장**을 선택합니다. 

   ![Google FCM에 대한 Notification Hubs를 구성하는 방법을 보여주는 스크린샷](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>다음 단계
Azure 알림 허브 및 Google Firebase 클라우드 메시징을 사용하여 Android 장치에 알림을 푸시하기 위한 단계별 지침이 포함된 자습서의 경우 [알림 허브 및 Google FCM을 사용하여 Android 장치에 대한 푸시 알림을](notification-hubs-android-push-notification-google-fcm-get-started.md)참조하십시오.

