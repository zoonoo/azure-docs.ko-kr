---
title: Azure Notification Hubs에서 Apple 푸시 알림 서비스 구성 | Microsoft Docs
description: Apple 푸시 알림 서비스 (APNS) 설정을 사용 하 여 Azure 알림 허브를 구성 하는 방법에 알아봅니다.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488389"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal에서 알림 허브에 대 한 Apple 푸시 알림 서비스 (APNS) 설정 구성
이 문서에서는 Azure portal을 사용 하 여 Azure 알림 허브에 대 한 Apple 푸시 알림 서비스 (APNS) 설정을 구성 하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure portal에서 Azure 알림 허브 만들기](create-notification-hub-portal.md)합니다. 

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service를 구성 합니다.

다음 절차는 알림 허브에 대 한 Apple 푸시 알림 서비스 (APNS) 설정을 구성 하는 단계를 제공 합니다.

1. Azure portal에서에 **알림 허브** 페이지에서 **APNS (Apple)** 왼쪽된 메뉴에서.

1. 에 대 한 **인증 모드**, 선택 **인증서** 하거나 **토큰**합니다.

   a. 선택 하는 경우 **인증서**:
   * 파일 아이콘을 선택한 다음 선택 합니다 *.p12* 업로드 하려는 파일입니다.
   * 암호를 입력합니다.
   * **샌드박스** 모드를 선택합니다. 앱 스토어에서 구매한 사용자에 게 푸시 알림을 보내도록 선택 하거나 **프로덕션** 모드입니다.

     ![스크린샷은 APNS 인증서는 Azure 포털에서 구성](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. 선택 하는 경우 **토큰**:

   * 값을 입력 **키 ID**를 **번들 ID**합니다 **팀 ID**, 및 **토큰**합니다.
   * **샌드박스** 모드를 선택합니다. 앱 스토어에서 구매한 사용자에 게 푸시 알림을 보내도록 선택 하거나 **프로덕션** 모드입니다.

     ![APNS 스크린샷 토큰이 Azure portal에서 구성](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>다음 단계
IOS 장치로 알림을 푸시하는 것에 대 한 단계별 지침을 사용 하 여 자습서의 경우 다음 문서를 참조 합니다. [Notification Hubs 및 APNS를 사용 하 여 iOS 장치에 알림 푸시](notification-hubs-ios-apple-push-notification-apns-get-started.md)
