---
title: Azure Notification Hubs에서 Windows 푸시 알림 서비스 구성 | Microsoft Docs
description: Azure 알림 허브에 대 한 Windows 푸시 알림 서비스 설정을 구성 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212420"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure Portal에서 알림 허브에 대 한 WNS (Windows 푸시 알림 서비스) 설정을 구성 합니다.
이 문서에서는 Azure Portal를 사용 하 여 Azure 알림 허브에 대 한 WNS (Windows 알림 서비스) 설정을 구성 하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>사전 요구 사항
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요. 

## <a name="configure-windows-push-notification-service-wns"></a>WNS (Windows 푸시 알림 서비스) 구성

다음 절차에서는 알림 허브에 대 한 WNS (Windows 푸시 알림 서비스) 설정을 구성 하는 단계를 제공 합니다. 

1. Azure Portal의 **알림 허브** 페이지에서 왼쪽 메뉴에 있는 **Windows (WNS)** 를 선택 합니다.
2. **패키지 SID** 및 **보안 키**에 사용할 값을 입력합니다.
3. **저장**을 선택합니다.

   ![패키지 SID 및 보안 키 상자를 보여주는 스크린샷](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>다음 단계
Azure Notification Hubs 및 WNS (Windows 푸시 알림 서비스)를 사용 하 여 유니버설 Windows 플랫폼 응용 프로그램에 알림을 푸시하는 방법에 대 한 단계별 지침이 포함 된 자습서는 [Azure 알림을 사용 하 여 UWP 앱에 알림 보내기를 참조 하세요. 허브](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


