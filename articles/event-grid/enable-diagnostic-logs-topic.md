---
title: Azure Event Grid-토픽 또는 도메인의 진단 로그를 사용 하도록 설정
description: 이 문서에서는 Azure event grid 토픽에 대해 진단 로그를 사용 하도록 설정 하는 방법에 대 한 단계별 지침을 제공 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 06/10/2020
ms.author: spelluru
ms.openlocfilehash: 253d1fb933c32735f68cf6a2d471a7687caf5301
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84670171"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Azure event grid 토픽 또는 도메인에 대 한 진단 로그 사용
진단 설정을 통해 사용자는 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역 중 하나에서 **게시 및 배달 오류** 로그를 캡처하고 볼 수 Event Grid 있습니다. 이 문서에서는 Event Grid 항목에서 이러한 설정을 사용 하도록 설정 하는 단계별 지침을 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 프로 비전 된 event grid 항목
- 진단 로그를 캡처하기 위해 프로 비전 된 대상입니다. Event grid 토픽과 동일한 위치에 있는 다음 대상 중 하나를 사용할 수 있습니다.
    - Azure Storage 계정
    - 이벤트 허브
    - Log Analytics 작업 영역

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>사용자 지정 항목에 대 한 진단 로그 사용

> [!NOTE]
> 다음 절차에서는 항목에 대 한 진단 로그를 사용 하도록 설정 하는 단계별 지침을 제공 합니다. 도메인에 대 한 진단 로그를 사용 하도록 설정 하는 단계는 매우 유사 합니다. 2 단계에서 Azure Portal의 event grid **도메인** 으로 이동 합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 진단 로그 설정을 사용 하도록 설정 하려는 event grid 항목으로 이동 합니다. 
    1. 위쪽의 검색 표시줄에서 **Event Grid 항목**을 검색 합니다. 
    
        ![사용자 지정 항목 검색](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. 진단 설정을 구성 하려는 목록에서 **항목** 을 선택 합니다. 
1. 왼쪽 메뉴의 **모니터링** 아래에서 **진단 설정** 을 선택 합니다.
1. **진단 설정** 페이지에서 **새 진단 설정 추가**를 선택 합니다. 
    
    ![진단 설정 추가 단추](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 진단 설정의 **이름을** 지정 합니다. 
7. **로그** 섹션에서 **DeliveryFailures** 및 기타 **오류** 옵션을 선택 합니다. 
    ![오류 선택](./media/enable-diagnostic-logs-topic/log-failures.png)
6. 로그에 대 한 캡처 대상을 하나 이상 사용 하도록 설정 하 고 이전에 만든 캡처 리소스를 선택 하 여 구성 합니다. 
    - **저장소 계정에 보관**을 선택 하는 경우 **저장소 계정-구성**을 선택 하 고 Azure 구독에서 저장소 계정을 선택 합니다. 

        ![Azure storage 계정에 보관](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **이벤트 허브로 스트림**을 선택 하는 경우 **이벤트 허브-구성**을 선택한 다음 Event Hubs 네임 스페이스, 이벤트 허브 및 액세스 정책을 선택 합니다. 
        ![이벤트 허브로 스트림](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **Log Analytics 보내기를**선택 하는 경우 Log Analytics 작업 영역을 선택 합니다.
        ![Log Analytics에 보내기](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. **저장**을 선택합니다. 그런 다음 오른쪽 모서리에 있는 **X** 를 선택 하 여 페이지를 닫습니다. 
9. 이제 **진단 설정** 페이지로 돌아가 **진단 설정** 테이블에 새 항목이 표시 되는지 확인 합니다. 
    ![목록의 진단 설정](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     토픽의 모든 메트릭 수집을 사용 하도록 설정할 수도 있습니다. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>시스템 항목에 대 한 진단 로그 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 진단 로그 설정을 사용 하도록 설정 하려는 event grid 항목으로 이동 합니다. 
    1. 위쪽의 검색 표시줄에서 **Event Grid 시스템 항목**을 검색 합니다. 
    
        ![시스템 항목 검색](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. 진단 설정을 구성 하려는 **시스템 항목** 을 선택 합니다. 
    
        ![시스템 항목 선택](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. 왼쪽 메뉴의 **모니터링** 아래에서 **진단 설정** 을 선택 하 고 **진단 설정 추가**를 선택 합니다. 

    ![진단 설정 추가-단추](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. 진단 설정의 **이름을** 지정 합니다. 
7. **로그** 섹션에서 **DeliveryFailures** 를 선택 합니다. 
    ![배달 실패 선택](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. 로그에 대 한 캡처 대상을 하나 이상 사용 하도록 설정 하 고 이전에 만든 캡처 리소스를 선택 하 여 구성 합니다. 
    - **Log Analytics 보내기를**선택 하는 경우 Log Analytics 작업 영역을 선택 합니다.
        ![Log Analytics에 보내기](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - **저장소 계정에 보관**을 선택 하는 경우 **저장소 계정-구성**을 선택 하 고 Azure 구독에서 저장소 계정을 선택 합니다. 

        ![Azure storage 계정에 보관](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - **이벤트 허브로 스트림**을 선택 하는 경우 **이벤트 허브-구성**을 선택한 다음 Event Hubs 네임 스페이스, 이벤트 허브 및 액세스 정책을 선택 합니다. 
        ![이벤트 허브로 스트림](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. **저장**을 선택합니다. 그런 다음 오른쪽 모서리에 있는 **X** 를 선택 하 여 페이지를 닫습니다. 
9. 이제 **진단 설정** 페이지로 돌아가 **진단 설정** 테이블에 새 항목이 표시 되는지 확인 합니다. 
    ![목록의 진단 설정](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     시스템 항목에 대 한 모든 **메트릭** 수집을 사용 하도록 설정할 수도 있습니다.

    ![시스템 항목-모든 메트릭 사용](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Azure Storage에서 진단 로그 보기 

1. 저장소 계정을 캡처 대상으로 사용 하도록 설정 하 Event Grid 고 진단 로그 내보내기를 시작 하는 경우 **deliveryfailures** 이라는 새 컨테이너와 저장소 계정에 insights- **로그 기록 오류가** 표시 되어야 합니다. 

    ![저장소-진단 로그에 대 한 컨테이너](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. 컨테이너 중 하나를 탐색할 때 JSON 형식의 blob에서 종료 됩니다. 이 파일은 배달 실패 또는 게시 오류에 대 한 로그 항목을 포함 합니다. 탐색 경로는 이벤트 그리드 토픽의 **ResourceId** 및 로그 항목을 내보낸 시간에 대 한 타임 스탬프 (분 수준)를 나타냅니다. 최종에서 다운로드할 수 있는 blob/JSON 파일은 다음 섹션에 설명 된 스키마를 따릅니다. 

    [![저장소 ](./media/enable-diagnostic-logs-topic/select-json.png) 의 JSON 파일](./media/enable-diagnostic-logs-topic/select-json.png)
3. 다음 예제와 유사 하 게 JSON 파일에 콘텐츠가 표시 됩니다. 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>다음 단계
항목 또는 도메인의 진단 로그에 대 한 로그 스키마 및 기타 개념 정보는 [진단 로그](diagnostic-logs.md)를 참조 하세요.
