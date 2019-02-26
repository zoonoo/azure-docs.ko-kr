---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: a5d29e77f6ba10ed3069cb3f5a3c8089f49c237d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313775"
---
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스**를 선택하고, **모바일** 섹션에서 **Notification Hubs**를 선택합니다. 서비스 이름 옆에서 별표(`*`)를 선택하여 왼쪽 메뉴의 **즐겨찾기** 섹션에 추가합니다. **Notification Hubs**가 **즐겨찾기**에 추가되면 왼쪽 메뉴에서 선택합니다. 

      ![Azure Portal - Notification Hubs 선택](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. **Notification Hubs** 페이지의 도구 모음에서 **추가**를 선택합니다. 

      ![Notification Hubs - 추가 도구 모음 단추](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. **Notification Hub** 페이지에서 다음 단계를 수행합니다. 
    1. **알림 허브**의 **이름**을 지정합니다.  
    2. **네임스페이스**의 **이름**을 지정합니다. 네임스페이스에는 하나 이상의 허브가 포함되어 있습니다. 
    3. 알림 허브를 만들려는 **위치**를 선택합니다. 
    4. 기존 리소스 그룹을 선택하거나 새 **리소스 그룹**의 이름을 입력합니다.
    5. **만들기**를 선택합니다. 

        ![Azure Portal - 알림 허브 속성 설정](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. **알림**(벨 아이콘)을 선택하고 **리소스로 이동**을 선택합니다. **Notification Hubs** 페이지의 목록을 새로 고치고 알림 허브를 선택할 수도 있습니다. 

      ![[Azure Portal] - [알림] -> [리소스로 이동]](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. 목록에서 **액세스 정책**을 선택합니다. 사용할 수 있는 두 가지 연결 문자열을 적어 둡니다. 나중에 푸시 알림을 처리하는 데 필요합니다.

      >[!IMPORTANT]
      >애플리케이션에서 DefaultFullSharedAccessSignature를 **사용하지 마세요**. 이는 백 엔드에서만 사용할 수 있습니다.
      >

      ![Azure Portal - 알림 허브 연결 문자열](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
