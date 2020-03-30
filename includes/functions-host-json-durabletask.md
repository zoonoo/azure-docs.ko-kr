---
title: 포함 파일
description: 포함 파일
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80116895"
---
[지속형 함수](../articles/azure-functions/durable-functions-overview.md)에 대한 구성 설정입니다.

### <a name="durable-functions-1x"></a>내구성 기능 1.x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>내구성 기능 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정되지 않은 경우 함수 앱의 기본 작업 허브 이름은 **DurableFunctionsHub**입니다. 자세한 내용은 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md)를 참조하세요.

|속성  |기본값 | 설명 |
|---------|---------|---------|
|hubName|DurableFunctionsHub|여러 Durable Functions 애플리케이션이 동일한 스토리지 백 엔드를 사용하더라도 대체 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md) 이름을 사용하면 이러한 애플리케이션을 서로 구분할 수 있습니다.|
|제어큐배치크기|32|제어 큐에서 한 번에 끌어올 메시지의 수입니다.|
|컨트롤큐 버퍼 임계값|256|한 번에 메모리에서 버퍼링할 수 있는 제어 큐 메시지의 수로, 이 때 디스패처는 추가 메시지를 큐에 추가하기 전에 기다립니다.|
|partitionCount |4|제어 큐에 대한 파티션 수입니다. 1에서 16 사이의 양의 정수일 수 있습니다.|
|제어큐가시성 시간 표시 시간 |5분|큐에서 제거된 제어 큐 메시지의 표시 여부 시간 제한입니다.|
|작업항목큐가시성 시간 표시 시간 |5분|큐에서 제거된 작업 항목 큐 메시지의 표시 여부 시간 제한입니다.|
|최대 동시 활동 기능 |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 작업 함수는 최대 수입니다.|
|maxConcurrentOrchestrator기능 |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 오케스트레이터 함수의 최대 개수입니다.|
|최대큐폴링간격|30초|*hh:mm:ss* 형식의 최대 제어 및 작업 항목 큐 폴링 간격입니다. 값이 높을수록 메시지 처리 대기 가 더 오래 될 수 있습니다. 값이 낮을수록 저장소 트랜잭션이 증가하여 저장소 비용이 증가할 수 있습니다.|
|azureStorage연결문자열이름 |AzureWebJobsStorage|기본 Azure Storage 리소스를 관리하는 데 사용되는 Azure Storage 연결 문자열이 있는 앱 설정의 이름입니다.|
|추적스토어연결문자열이름||기록 및 인스턴스 테이블에 사용할 연결 문자열의 이름입니다. 지정하지 않으면 `azureStorageConnectionStringName` 연결이 사용됩니다.|
|추적스토어네임프리픽스||지정될 때 `trackingStoreConnectionStringName` 기록 및 인스턴스 테이블에 사용할 접두사입니다. 설정하지 않으면 기본 접두사 `DurableTask`값이 됩니다. 지정하지 않으면 `trackingStoreConnectionStringName` 히스토리 및 인스턴스 테이블은 `hubName` 값을 접두사로 사용하고 에 `trackingStoreNamePrefix` 대한 모든 설정은 무시됩니다.|
|추적입력및출력 |false|함수 호출의 입출력을 추적할지 여부를 나타내는 값입니다. 함수 실행 이벤트를 추적할 때의 기본 동작은 함수 호출에 대한 직렬화된 입출력에 바이트 수를 포함하는 것입니다. 이 동작은 로그를 팽창하거나 중요한 정보를 실수로 노출하지 않고 입력 및 출력의 모양에 대한 최소한의 정보를 제공합니다. 이 속성을 true로 설정하면 기본 함수 로깅이 함수 입출력의 전체 내용을 기록하게 됩니다.|
|로그리플레이이벤트|false|Application Insights에 이벤트를 재생하는 오케스트레이션을 작성할 것인지 여부를 나타내는 값입니다.|
|이벤트그리드토픽엔드포인트 ||Azure Event Grid 사용자 지정 항목 엔드포인트의 URL입니다. 이 속성을 설정하면 오케스트레이션 수명 주기 알림 이벤트가 이 끝점에 게시됩니다. 이 속성은 앱 설정 해결을 지원합니다.|
|이벤트그리드키세팅네임 ||`EventGridTopicEndpoint`에서 Azure Event Grid 사용자 지정 항목으로 인증하는 데 사용되는 키를 포함하는 앱 설정의 이름입니다.|
|이벤트그리드게시리카운트|0|Event Grid 항목에 게시가 실패하는 경우 다시 시도 횟수입니다.|
|이벤트그리드게시리간격|5분|*hh:mm:ss* 형식의 Event Grid 게시 재시도 간격입니다.|
|이벤트그리드게시이벤트유형||이벤트 그리드에 게시할 이벤트 유형 목록입니다. 지정하지 않으면 모든 이벤트 유형이 게시됩니다. 허용된 값에는 `Failed` `Terminated`" 가 포함됩니다. `Started` `Completed`|
|유그레이스풀셧다운|false|(미리 보기) 호스트 종료가 프로세스 내 기능 실행에 실패할 가능성을 줄이기 위해 정상적으로 종료할 수 있도록 합니다.|

이러한 설정의 대부분은 성능을 최적화하기 위한 것입니다. 자세한 내용은 [성능 및 크기 조정](../articles/azure-functions/durable-functions-perf-and-scale.md)을 참조하세요.
