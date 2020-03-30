---
title: Azure 알림 허브에서 Windows 푸시 알림 서비스 구성 | 마이크로 소프트 문서
description: Azure 알림 허브에 대한 Windows 푸시 알림 서비스 설정을 구성하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127319"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Azure 포털에서 Windows 푸시 알림 서비스 설정 구성

이 문서에서는 Azure 포털을 사용하여 Azure 알림 허브에 대한 WNS(Windows 알림 서비스) 설정을 구성하는 방법을 보여 주며, 이 문서에서는  

## <a name="prerequisites"></a>사전 요구 사항
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요. 

## <a name="configure-windows-push-notification-service-wns"></a>WINDOWS 푸시 알림 서비스(WNS) 구성

다음 절차에서는 알림 허브에 대한 WNS(Windows 푸시 알림 서비스) 설정을 구성하는 단계를 제공합니다. 

1. Azure 포털에서 알림 **허브** 페이지에서 왼쪽 메뉴에서 **WINDOWS(WNS)를** 선택합니다.
2. **패키지 SID** 및 **보안 키**에 사용할 값을 입력합니다.
3. **저장**을 선택합니다.

   ![패키지 SID 및 보안 키 상자를 보여주는 스크린샷](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>다음 단계
Azure 알림 허브 및 WNS(Windows 푸시 알림 서비스)를 사용하여 유니버설 Windows 플랫폼 응용 프로그램에 알림을 푸시하기 위한 단계별 지침이 있는 자습서의 경우 [Azure 알림 허브를 사용하여 UWP 앱에 알림 보내기](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)를 참조하십시오.


