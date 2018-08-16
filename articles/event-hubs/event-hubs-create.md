---
title: Azure 이벤트 허브 만들기 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Event Hubs 네임스페이스 및 이벤트 허브 만들기
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: shvija
ms.openlocfilehash: fb2020f7bfc8521e141adb4eefbc227e8123a269
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002623"
---
# <a name="create-an-event-hubs-namespace-and-an-event-hub-using-the-azure-portal"></a>Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

1. [Azure Portal][Azure portal]에 로그온하고 화면 왼쪽 위에서 **리소스 만들기**를 클릭합니다.
2. **사물 인터넷**을 클릭한 다음, **Event Hubs**를 클릭합니다.
   
    ![](./media/event-hubs-create/create-event-hub9.png)

3. **네임스페이스 만들기**에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.  

4. 네임스페이스 이름을 사용할 수 있게 설정한 후 가격 책정 계층(기본 또는 표준)을 선택합니다. 또한 리소스를 만들 Azure 구독, 리소스 그룹 및 위치를 선택합니다.
 
5. **만들기** 를 클릭하여 네임스페이스를 만듭니다. 시스템에서 리소스를 완전히 프로비전하기까지 몇 분 동안 기다려야 할 수 있습니다.

    ![](./media/event-hubs-create/create-event-hub1.png)

6. 네임스페이스 포털 목록에서 새로 만든 네임스페이스를 클릭합니다.

7. **공유 액세스 정책**을 클릭한 후 **RootManageSharedAccessKey**를 클릭합니다.
    
    ![](./media/event-hubs-create/create-event-hub7.png)

8. 복사 단추를 클릭하여 클립보드에 대한 **RootManageSharedAccessKey** 연결 문자열을 복사합니다. 나중에 사용하기 위해 이 연결 문자열을 임시 위치(예: 메모장)에 저장합니다.
    
    ![](./media/event-hubs-create/create-event-hub8.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

1. Event Hubs 네임스페이스 목록에서 새로 만든 네임스페이스를 클릭합니다.      
   
    ![](./media/event-hubs-create/create-event-hub2.png) 

2. 네임스페이스 블레이드에서 **Event Hubs**를 클릭합니다.
   
    ![](./media/event-hubs-create/create-event-hub3.png)

3. 블레이드의 위쪽에서 **+ Event Hubs**를 클릭합니다.
   
    ![](./media/event-hubs-create/create-event-hub4.png)
4. 이벤트 허브의 이름을 입력한 다음, **만들기**를 클릭합니다. 

이제 이벤트 허브가 생성되었고 이벤트를 보내고 받는 데 필요한 연결 문자열이 있습니다.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 방문하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [Event Hubs API 개요](event-hubs-api-overview.md)

[Azure portal]: https://portal.azure.com/