---
title: Azure Event Grid에서 지 원하는 메트릭
description: 이 문서에서는 Azure Event Grid 서비스에서 지 원하는 Azure Monitor 메트릭을 제공 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82630136"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid에서 지 원하는 메트릭
이 문서에서는 네임 스페이스로 분류 된 Event Grid 메트릭의 목록을 제공 합니다. 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|항목|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|토픽, ErrorType, 오류|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|None|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|None|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, 오류|
|UnmatchedEventCount|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|None|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|None|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft EventGrid/systemTopics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|None|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|UnmatchedEventCount|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|None|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|None|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|MatchedEventCount|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|None|
|DeliveryAttemptFailCount|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|Error, ErrorType|
|DeliverySuccessCount|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|None|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|None|
|DroppedEventCount|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason|
|DeadLetteredCount|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|None|
|PublishFailCount|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|UnmatchedEventCount|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|None|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|None|

## <a name="next-steps"></a>다음 단계
[진단 로그](diagnostic-logs.md) 문서를 참조 하세요.
