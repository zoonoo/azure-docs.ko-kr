---
title: Azure Notification Hubs에서 Windows 푸시 알림 서비스 구성 | Microsoft Docs
description: Azure 알림 허브에 대 한 Windows 푸시 알림 서비스 설정을 구성 하는 방법에 알아봅니다.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60240277"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Azure portal에서 알림 허브에 대 한 Windows 푸시 알림 서비스 (WNS) 설정 구성
이 문서에서는 Azure portal을 사용 하 여 Azure 알림 허브에 대 한 Windows 알림 서비스 (WNS) 설정을 구성 하는 방법을 보여 줍니다.  

## <a name="prerequisites"></a>필수 조건
알림 허브를 아직 만들지 않은 경우 지금 만듭니다. 자세한 내용은 [Azure Portal에 Azure 알림 허브 만들기](create-notification-hub-portal.md)를 참조하세요. 

## <a name="configure-windows-push-notification-service-wns"></a>Windows 푸시 알림 서비스 (WNS) 구성

다음 절차는 알림 허브에 대 한 Windows 푸시 알림 서비스 (WNS) 설정을 구성 하는 단계를 제공 합니다. 

1. Azure portal에서에 **알림 허브** 페이지에서 **Windows (WNS)** 왼쪽된 메뉴에서.
2. **패키지 SID** 및 **보안 키**에 사용할 값을 입력합니다.
3. **저장**을 선택합니다.

   ![패키지 SID 및 보안 키 상자를 보여주는 스크린샷](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>다음 단계
Azure Notification Hubs 및 Windows 푸시 알림 서비스 (WNS)를 사용 하 여 유니버설 Windows 플랫폼 응용 프로그램에 알림을 푸시하는 것에 대 한 단계별 지침이 포함 된 자습서를 참조 하세요. [Azure를 사용 하 여 UWP 앱에 알림 보내기 Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)합니다.


