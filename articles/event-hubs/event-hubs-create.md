---
title: Azure 빠른 시작 - Azure Portal을 사용하여 이벤트 허브 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure 이벤트 허브를 만든 다음, .NET 표준 SDK를 사용하여 이벤트를 보내고 받는 방법을 알아봅니다.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: d053edaa187a3e0626f5ea0864d778f44f394bd7
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455779"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기
Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 장치에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

Azure 빠른 시작에서 [Azure Portal](https://portal.azure.com)을 사용하여 이벤트 허브를 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

- 동작합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](http://www.visualstudio.com/vs) 이상
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스에 대한 논리적 컬렉션입니다. 모든 리소스는 리소스 그룹에서 배포 및 관리됩니다. 다음을 수행하여 리소스 그룹을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색에서 **리소스 그룹**을 클릭합니다. 그런 다음, **추가**를 클릭합니다.

   ![리소스 그룹 - 추가 단추](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. 리소스 그룹에 대한 고유한 이름을 입력합니다. 시스템에서 즉시 이 이름이 현재 선택한 Azure 구독에서 사용 가능한지 확인합니다.

3. **구독**에서 리소스 그룹을 만들려는 Azure 구독의 이름을 클릭합니다.

4. 리소스 그룹의 지리적 위치를 선택합니다.

5. **만들기**를 클릭합니다.

   ![리소스 그룹 - 만들기](./media/event-hubs-quickstart-portal/resource-groups2.png)

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

Event Hubs 네임스페이스는 정규화된 도메인 이름으로 참조되는 고유한 범위 지정 컨테이너를 제공하며, 하나 이상의 이벤트 허브를 만듭니다. 포털을 사용하여 리소스 그룹에 네임스페이스를 만들려면 다음 작업을 수행합니다.

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기**를 클릭합니다.

2. **사물 인터넷**을 클릭한 다음, **Event Hubs**를 클릭합니다.

3. **네임스페이스 만들기**에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.

   ![이벤트 허브 네임스페이스 만들기](./media/event-hubs-create/create-event-hub1.png)

4. 네임스페이스 이름을 사용할 수 있게 설정한 후 가격 책정 계층(기본 또는 표준)을 선택합니다. 또한 리소스를 만들 Azure 구독, 리소스 그룹 및 위치를 선택합니다.
 
5. **만들기** 를 클릭하여 네임스페이스를 만듭니다. 시스템에서 리소스를 완전히 프로비전하기까지 몇 분 동안 기다려야 할 수 있습니다.
6. **경고**를 선택한 다음, 이벤트 허브 네임스페이스와 이름이 같은 **배포**를 선택합니다. 

   ![리소스 그룹 - 만들기에서 경고](./media/event-hubs-quickstart-portal/create-alert.png)
6. 배포에서 만든 리소스 목록에서 이벤트 허브 네임스페이스를 선택합니다. 

   ![배포에서 네임스페이스 선택](./media/event-hubs-quickstart-portal/deployment-namespace.png)
7. **Event Hubs 네임스페이스** 페이지에서 **공유 액세스 정책**을 선택하고 **RootManageSharedAccessKey**를 클릭합니다.
    
8. 복사 단추를 클릭하여 클립보드에 대한 **RootManageSharedAccessKey** 연결 문자열을 복사합니다. 나중에 사용하기 위해 이 연결 문자열을 임시 위치(예: 메모장)에 저장합니다.
    
## <a name="create-an-event-hub"></a>이벤트 허브 만들기

네임스페이스에 이벤트 허브를 만들려면 다음 작업을 수행합니다.

1. [Event Hubs 네임스페이스] 페이지에서 **Event Hubs**를 클릭합니다.
   
    ![왼쪽 메뉴에서 [Event Hubs] 선택](./media/event-hubs-quickstart-portal/create-event-hub3.png)

1. 창의 위쪽에서 **+ 이벤트 허브**를 클릭합니다.
   
    ![이벤트 허브 추가 - 버튼](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. 이벤트 허브의 이름을 입력한 다음, **만들기**를 클릭합니다.
   
    ![이벤트 허브 만들기](./media/event-hubs-quickstart-portal/create-event-hub5.png)


축하합니다! 포털을 사용하여 Event Hubs 네임스페이스와, 그 네임스페이스 안에 이벤트 허브를 만들었습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 리소스 그룹, Event Hubs 네임스페이스 및 이벤트 허브를 만들었습니다. 이벤트 허브로 이벤트를 보내고 받는 단계별 지침은 다음 자습서를 참조하세요.  

- **이벤트 허브로 이벤트 전송**: [.NET 표준](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **이벤트 허브에서 이벤트 수신**: [.NET 표준](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
