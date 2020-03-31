---
title: Azure 이벤트 그리드 - 토픽에 대한 진단 로그 사용
description: 이 문서에서는 Azure 이벤트 그리드 항목에 대한 진단 로그를 사용하도록 설정하는 방법에 대한 단계별 지침을 제공합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960504"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure 이벤트 그리드 항목에 대한 진단 로그
진단 설정을 사용하면 Event Grid 사용자가 Azure 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역 중 하나에서 게시 및 배달 실패 로그를 캡처하고 볼 수 있습니다. 이 문서에서는 이벤트 그리드 항목에 대한 진단 로그를 사용하도록 설정하는 단계별 지침을 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 프로비저닝된 이벤트 그리드 토픽
- 진단 로그를 캡처하기 위한 프로비전된 대상입니다. 다음 목적지 중 하나를 수행할 수 있습니다.
    - Azure Storage 계정
    - 이벤트 허브
    - Log Analytics 작업 영역


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>토픽에 대한 진단 로그를 사용하도록 설정하는 단계

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 진단 로그 설정을 사용하도록 설정하려는 이벤트 그리드 항목으로 이동합니다. 
3. 왼쪽 메뉴에서 **모니터링** 에서 **진단 설정을** 선택합니다.
4. 진단 **설정** 페이지에서 **새 진단 설정 추가를**선택합니다. 
    
    ![진단 설정 버튼 추가](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 진단 설정의 **이름을** 지정합니다. 

    ![진단 설정 - 이름](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. 로그에 대해 하나 이상의 캡처 대상을 활성화한 다음 이전에 만든 캡처 리소스를 선택하여 구성합니다. 
    - **저장소 계정에 보관을**선택한 경우 **저장소 계정 - 구성**을 선택한 다음 Azure 구독에서 저장소 계정을 선택합니다. 

        ![Azure 저장소 계정에 보관](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **이벤트 허브로 스트림을**선택한 경우 **이벤트 허브 - 구성**을 선택한 다음 이벤트 허브 네임스페이스, 이벤트 허브 및 액세스 정책을 선택합니다. 
        ![이벤트 허브로 스트리밍](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **로그 분석으로 보내기를**선택한 경우 로그 분석 작업 영역을 선택합니다.
        ![Log Analytics에 보내기](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. **로그** 섹션에서 **배달 실패** 및 게시실패 옵션을 **선택합니다.** 
    ![오류 선택](./media/enable-diagnostic-logs-topic/log-failures.png)
8. **저장**을 선택합니다. 오른쪽 모서리에서 **X를** 선택하여 페이지를 닫습니다. 
9. 이제 **진단 설정** 페이지에서 진단 **설정** 테이블에 새 항목이 표시되는지 확인합니다. 
    ![목록의 진단 설정](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     토픽에 대한 모든 메트릭의 컬렉션을 활성화할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [스택 오버플로 포럼에](https://stackoverflow.com/questions/tagged/azure-eventgrid) 문제를 게시하거나 [지원 티켓을](https://azure.microsoft.com/support/options/)엽니다. 
