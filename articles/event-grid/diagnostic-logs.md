---
title: Azure Event Grid-토픽 또는 도메인에 대 한 진단 로그
description: 이 문서에서는 Azure event grid 토픽 또는 도메인의 진단 로그에 대 한 개념 정보를 제공 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 93e7e47cbcc1ab9542ba333b89f7dd655a412489
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630253"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>Azure Event Grid 토픽/도메인에 대 한 진단 로그
진단 설정을 통해 사용자는 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역 중 하나에서 **게시 및 배달 오류** 로그를 캡처하고 볼 수 Event Grid 있습니다. 이 문서에서는 로그 및 예제 로그 항목에 대 한 스키마를 제공 합니다.


## <a name="schema-for-publishdelivery-failure-logs"></a>게시/배달 실패 로그의 스키마

| 속성 이름 | 데이터 형식 | 설명 |
| ------------- | --------- | ----------- | 
| 시간 | DateTime | 로그 항목이 생성 된 시간입니다. <p>**값 예:** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | 이벤트 구독의 이름 <p>**값 예:** "EVENTSUB1"</p> <p>이 속성은 배달 실패 로그에 대해서만 존재 합니다.</p>  |
| 범주 | String | 로그 범주 이름입니다. <p>**예제 값:** "DeliveryFailures" 또는 "오류 발생" | 
| OperationName | String | 오류가 발생 하는 동안 수행 되는 작업의 이름입니다.<p>**예제 값:** 배달 실패를 "전달" 합니다. |
| 메시지 | String | 오류의 원인과 기타 추가 정보를 설명 하는 사용자에 대 한 로그 메시지입니다. |
| ResourceId | String | 토픽/도메인 리소스에 대 한 리소스 ID<p>**예제 값:**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>예

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
토픽 또는 도메인에 대해 진단 로그를 사용 하도록 설정 하는 방법을 알아보려면 [진단 로그 사용](enable-diagnostic-logs-topic.md)을 참조 하세요.
