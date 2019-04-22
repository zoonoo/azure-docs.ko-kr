---
title: 개념, 용어 및 엔터티 - Azure Scheduler | Microsoft Docs
description: Azure Scheduler에서 작업 및 작업 컬렉션을 포함한 개념, 용어 및 엔터티 계층 구조를 알아봅니다.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: d701fba39685d781d1a4c2d8a6cf194ca7eb2908
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683055"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Azure Scheduler에서 개념, 용어 및 엔터티

> [!IMPORTANT]
> Azure Scheduler는 사용이 중지되며 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 대신 제공됩니다. 작업을 예약하려는 경우 [Azure Logic Apps를 대신 사용해 보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>엔터티 계층 구조

Azure Scheduler REST API는 다음과 같은 주요 엔터티 또는 리소스를 공개하고 사용합니다.

| 엔터티 | 설명 |
|--------|-------------|
| **작업** | 간단하거나 복잡한 실행 전략을 통해 단일 되풀이 작업을 정의합니다. 작업에는 HTTP, 저장소 큐, Service Bus 큐 또는 Service Bus 항목 요청이 포함될 수 있습니다. | 
| **작업 컬렉션** | 작업 그룹을 포함하며 컬렉션에서 작업이 공유하는 설정, 할당량 및 제한을 유지합니다. Azure 구독 소유자는 작업 컬렉션을 만들고, 사용 또는 애플리케이션 경계를 기준으로 작업을 함께 그룹화할 수 있습니다. 작업 컬렉션은 다음과 같은 특성이 있습니다. <p>- 한 지역으로 제한됩니다. <br>- 컬렉션의 모든 작업에 대해 사용량을 제한할 수 있도록 할당량을 적용할 수 있습니다. <br>- 할당량은 MaxJobs 및 MaxRecurrence를 포함합니다. | 
| **작업 기록** | 예를 들어, 상태 및 응답 세부 정보와 같은 작업 실행에 대한 정보를 설명합니다. |
||| 

## <a name="entity-management"></a>엔터티 관리

상위 수준에서 Scheduler REST API는 엔터티 관리를 위해 이러한 작업을 노출시킵니다.

### <a name="job-management"></a>작업 관리

작업을 만들고 편집하기 위한 조작을 지원합니다. 모든 작업은 기존 작업 컬렉션에 속해야 하므로 암시적으로 만들어지지 않습니다. 자세한 내용은 [Scheduler REST API - 작업](https://docs.microsoft.com/rest/api/scheduler/jobs)을 참조하세요. 이러한 작업에 대 한 URI 주소는 다음과 같습니다.

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>작업 컬렉션 관리

할당량 및 공유 설정에 매핑되는 작업 및 작업 컬렉션을 만들고 편집하기 위한 조작을 지원합니다. 예를 들어 할당량은 최대 작업 수와 최소 되풀이 간격을 지정합니다. 자세한 내용은 [Scheduler REST API - 작업 컬렉션](https://docs.microsoft.com/rest/api/scheduler/jobcollections)을 참조하세요. 이러한 작업에 대 한 URI 주소는 다음과 같습니다.

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>작업 기록 관리

작업 경과 시간, 작업 실행 결과 등, 60일 간의 작업 실행 기록을 가져오기 위한 GET 작업을 지원합니다. 상태를 기초로 한 필터링을 위해 쿼리 문자열 매개 변수 지원을 포함합니다. 자세한 내용은 [Scheduler REST API - 작업 기록 나열](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory)을 참조하세요. 이 작업에 대 한 URI 주소는 다음과 같습니다.

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>작업 유형

Azure Scheduler는 여러 작업 유형을 지원합니다. 

* 기존 서비스 또는 워크로드에 대한 엔드포인트가 있는 경우를 위한 HTTP 작업(SSL을 지원하는 HTTPS 작업 포함)
* 저장소 큐에 메시지를 게시하는 등, 저장소 큐를 사용하는 워크로드를 위한 저장소 큐 작업
* Service Bus 큐를 사용하는 워크로드를 위한 Service Bus 큐 작업
* Service Bus 항목을 사용하는 워크로드를 위한 Service Bus 항목 작업

## <a name="job-definition"></a>작업 정의

높은 수준에서 Scheduler 작업에는 다음과 같은 기본적인 부분이 있습니다.

* 작업 타이머가 발생할 때 실행할 동작
* 선택 사항: 작업 실행 시간
* 선택 사항: 시간과 작업을 반복 하는 빈도
* 선택 사항: 기본 작업이 실패 하는 경우 실행 되는 오류 작업

작업에는 작업의 예약된 다음 실행 시간 등의 시스템 제공 데이터도 포함됩니다. 작업 코드 정의는 다음과 같은 요소를 가진 JSON(JavaScript Object Notation) 형식의 개체입니다.

| 요소 | 필수 | 설명 | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | 아닙니다. | [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)의 표준 시간대 오프셋을 사용하는 작업의 시작 시간 | 
| [**action**](#action) | 예 | 기본 동작의 세부 정보로, **errorAction** 개체를 포함할 수 있습니다. | 
| [**errorAction**](#error-action) | 아닙니다. | 기본 동작이 실패하는 경우 실행되는 보조 동작에 대한 세부 정보 |
| [**recurrence**](#recurrence) | 아닙니다. | 되풀이 작업에 대한 빈도 및 간격 등의 세부 정보 | 
| [**retryPolicy**](#retry-policy) | 아닙니다. | 동작을 다시 시도하는 빈도에 대한 세부 정보 | 
| [**state**](#state) | 예 | 작업의 현재 상태에 대한 세부 정보 |
| [**status**](#status) | 예 | 서비스에 의해 제어되는 작업의 현재 상태에 대한 세부 정보 |
||||

다음은 이후 섹션에서 설명하는 전체 요소 세부 정보가 포함된 HTTP 동작에 대한 포괄적인 작업 정의를 보여 주는 예제입니다. 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

**startTime** 개체에서 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)으로 시작 시간 및 표준 시간대 오프셋을 지정할 수 있습니다.

<a name="action"></a>

## <a name="action"></a>action

Scheduler 작업은 지정된 일정에 따라 기본 **action**을 실행합니다. Scheduler는 HTTP, 저장소 큐, Service Bus 큐 또는 Service Bus 항목 동작을 지원합니다. 기본 **action**이 실패하는 경우 Scheduler는 오류를 처리하는 보조 [**errorAction**](#erroraction)을 실행할 수 있습니다. **action** 개체는 다음과 같은 요소를 설명합니다.

* 동작의 서비스 유형
* 동작의 세부 정보
* 대체 **errorAction**

이전 예제에서는 HTTP 동작을 설명합니다. 다음은 저장소 큐 동작의 예입니다.

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

다음은 Service Bus 큐 동작의 예입니다.

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

다음은 Service Bus 항목 동작의 예입니다.

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

SAS(공유 액세스 서명) 토큰에 대한 자세한 내용은 [공유 액세스 서명을 사용한 인증](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

작업의 기본 **action**이 실패하는 경우 Scheduler는 오류를 처리하는 **errorAction**을 실행할 수 있습니다. 기본 **action**에서 Scheduler가 오류 처리 엔드포인트를 호출하거나 사용자 알림을 보낼 수 있도록 **errorAction** 개체를 지정할 수 있습니다. 

예를 들어 기본 엔드포인트에서 재해가 발생하는 경우 **errorAction**을 사용하여 보조 엔드포인트를 호출하거나 오류 처리 엔드포인트에 알릴 수 있습니다. 

기본 **action**과 마찬가지로 오류 동작은 다른 동작에 따라 단순 또는 복합 논리를 사용할 수 있습니다. 

<a name="recurrence"></a>

## <a name="recurrence"></a>되풀이

다음과 같이 작업의 JSON 정의에 **recurrence** 개체가 포함된 경우 작업이 되풀이됩니다.

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| 자산 | 필수 | Value | 설명 | 
|----------|----------|-------|-------------| 
| **frequency** | **recurrence**가 사용된 경우, 예 | "Minute", "Hour", "Day", "Week", "Month", "Year" | 발생 간격 간의 시간 단위 | 
| **interval** | 아닙니다. | 1에서 1000(포함) 사이 | **frequency**에 따른 각 발생 간의 시간 단위 수를 결정하는 양의 정수 | 
| **schedule** | 아닙니다. | 다름 | 더 복잡한 일정 및 고급 일정에 대한 세부 정보입니다. **hours**, **minutes**, **weekDays**, **months** 및 **monthDays**를 참조하세요. | 
| **hours** | 아닙니다. | 1 - 24 | 작업 실행 시기에 대한 시간 표식이 포함된 배열 | 
| **minutes** | 아닙니다. | 0 ~ 59 | 작업 실행 시기에 대한 분 표식이 포함된 배열 | 
| **months** | 아닙니다. | 1 - 12 | 작업 실행 시기에 대한 월 표식이 포함된 배열 | 
| **monthDays** | 아닙니다. | 다름 | 작업 실행 시기에 대한 날짜 표식이 포함된 배열 | 
| **weekDays** | 아닙니다. | "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday" | 작업 실행 시기에 대한 요일 표식이 포함된 배열 | 
| **count** | 아닙니다. | <*none*> | 되풀이 횟수입니다. 기본값은 무한 반복입니다. **count** 및 **endTime**을 둘 다 사용할 수 없지만 먼저 완료되는 규칙이 적용됩니다. | 
| **endTime** | 아닙니다. | <*none*> | 되풀이를 중지할 날짜 및 시간입니다. 기본값은 무한 반복입니다. **count** 및 **endTime**을 둘 다 사용할 수 없지만 먼저 완료되는 규칙이 적용됩니다. | 
||||

이러한 요소에 대한 자세한 내용은 [복잡한 일정 및 고급 되풀이 빌드](../scheduler/scheduler-advanced-complexity.md)를 참조하세요.

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Scheduler 작업이 실패할 경우 Scheduler에서 동작을 재시도할지 여부 및 방법을 결정하는 재시도 정책을 설정할 수 있습니다. 기본적으로, Scheduler는 30초 간격으로 4회 더 작업을 시도합니다. 이 정책을 더 적극적으로 만들 수 있습니다. 예를 들어 하루에 두 번 동작을 다시 시도하도록 이 정책을 설정할 수 있습니다.

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| 자산 | 필수 | Value | 설명 | 
|----------|----------|-------|-------------| 
| **retryType** | 예 | **Fixed**, **None** | 재시도 정책을 지정할지(**fixed**) 또는 지정하지 않을지(**none**) 결정합니다. | 
| **retryInterval** | 아닙니다. | PT30S | 재시도 사이의 간격 및 빈도를 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)으로 지정합니다. 최솟값은 15초이고 최댓값은 18개월입니다. | 
| **retryCount** | 아닙니다. | 4 | 재시도 횟수입니다. 최댓값은 20입니다. | 
||||

자세한 내용은 [고가용성 및 안정성](../scheduler/scheduler-high-availability-reliability.md)을 참조하세요.

<a name="status"></a>

## <a name="state"></a>state

작업 상태는 **Enabled**, **Disabled**, **Completed** 또는 **Faulted** 중 하나로, 예를 들어 다음과 같습니다. 

`"state": "Disabled"`

작업을 **Enabled** 또는 **Disabled** 상태로 변경하면 해당 작업에서 PUT 또는 PATCH 작업을 사용할 수 있습니다.
그러나 작업이 **Completed** 또는 **Faulted** 상태인 경우 작업에 대해 DELETE 작업을 수행할 수 있지만 상태를 업데이트할 수는 없습니다. Scheduler는 완료 및 오류 작업을 60일 후 삭제합니다. 

<a name="status"></a>

## <a name="status"></a>status

작업이 시작된 후 Scheduler는 Scheduler만 제어하는 **status** 개체를 통해 작업의 상태에 대한 정보를 반환합니다. 하지만 **job** 개체 내에서 **status** 개체를 찾을 수 있습니다. 작업의 상태를 포함하는 정보는 다음과 같습니다.

* 이전 실행 시간(있는 경우)
* 진행 중인 작업에 대해 예약된 다음 실행 시간
* 작업 실행 횟수
* 실패 횟수(해당되는 경우)
* 오류 수(해당되는 경우)

예를 들면 다음과 같습니다.

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>참고 항목

* [Azure Scheduler란?](scheduler-intro.md)
* [개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [복잡한 일정 및 고급 되풀이 빌드](scheduler-advanced-complexity.md)
* [한도, 할당량, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API 참조](/rest/api/scheduler)
* [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)
