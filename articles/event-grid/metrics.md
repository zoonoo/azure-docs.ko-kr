---
title: Azure Event Grid에서 지원하는 메트릭
description: 이 문서에서는 Azure Event Grid 서비스에서 지원하는 Azure Monitor 메트릭을 제공합니다.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588735"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid에서 지원하는 메트릭
이 문서에는 네임스페이스로 분류된 Event Grid 메트릭이 나열되어 있습니다. 

## <a name="system-topics"></a>시스템 항목

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수.|EventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="custom-topics"></a>사용자 지정 항목

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수.|EventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|

## <a name="domains"></a>도메인

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수.|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, 오류, ErrorType|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|토픽, ErrorType, 오류|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|항목|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|

## <a name="event-subscriptions"></a>이벤트 구독

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|Error, ErrorType|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|차원 없음|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|차원 없음|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|차원 없음|


## <a name="extension-topics"></a>확장 항목

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|

## <a name="partner-namespaces"></a>파트너 네임스페이스

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간(밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="partner-topics"></a>파트너 토픽

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|예|고급 필터 평가|개수|합계|이 토픽의 이벤트 구독에서 평가되는 총 고급 필터 수.|EventSubscriptionName|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|예|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|예|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. [진단 로그](diagnostic-logs.md)
