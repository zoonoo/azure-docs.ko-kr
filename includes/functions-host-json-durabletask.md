---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 92eb13165326f44432f09322ea97f3cee5ccec2b
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251246"
---
[지속형 함수](../articles/azure-functions/durable-functions-overview.md)에 대한 구성 설정입니다.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub",
    "ControlQueueBatchSize": 32,
    "PartitionCount": 4,
    "ControlQueueVisibilityTimeout": "00:05:00",
    "WorkItemQueueVisibilityTimeout": "00:05:00",
    "MaxConcurrentActivityFunctions": 10,
    "MaxConcurrentOrchestratorFunctions": 10,
    "AzureStorageConnectionStringName": "AzureWebJobsStorage",
    "TraceInputsAndOutputs": false,
    "LogReplayEvents": false,
    "EventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "EventGridKeySettingName":  "EventGridKey",
    "EventGridPublishRetryCount": 3,
    "EventGridPublishRetryInterval": "00:00:30"
  }
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정되지 않은 경우 함수 앱의 기본 작업 허브 이름은 **DurableFunctionsHub**입니다. 자세한 내용은 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md)를 참조하세요.

|자산  |기본값 | 설명 |
|---------|---------|---------|
|HubName|DurableFunctionsHub|여러 Durable Functions 애플리케이션이 동일한 저장소 백 엔드를 사용하더라도 대체 [작업 허브](../articles/azure-functions/durable-functions-task-hubs.md) 이름을 사용하면 이러한 애플리케이션을 서로 구분할 수 있습니다.|
|ControlQueueBatchSize|32|제어 큐에서 한 번에 끌어올 메시지의 수입니다.|
|PartitionCount |4|제어 큐에 대한 파티션 수입니다. 1에서 16 사이의 양의 정수일 수 있습니다.|
|ControlQueueVisibilityTimeout |5분|큐에서 제거된 제어 큐 메시지의 표시 여부 시간 제한입니다.|
|WorkItemQueueVisibilityTimeout |5분|큐에서 제거된 작업 항목 큐 메시지의 표시 여부 시간 제한입니다.|
|MaxConcurrentActivityFunctions |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 작업 함수는 최대 수입니다.|
|MaxConcurrentOrchestratorFunctions |현재 컴퓨터에 있는 프로세서 수의 10배입니다.|단일 호스트 인스턴스에서 동시에 처리할 수 있는 오케스트레이터 함수의 최대 개수입니다.|
|AzureStorageConnectionStringName |AzureWebJobsStorage|기본 Azure Storage 리소스를 관리하는 데 사용되는 Azure Storage 연결 문자열이 있는 앱 설정의 이름입니다.|
|TraceInputsAndOutputs |false|함수 호출의 입출력을 추적할지 여부를 나타내는 값입니다. 함수 실행 이벤트를 추적할 때의 기본 동작은 함수 호출에 대한 직렬화된 입출력에 바이트 수를 포함하는 것입니다. 이를 통해 로그를 블로트하거나 실수로 로그에 중요한 정보를 노출하지 않고도, 표시될 입출력에 대한 최소한의 정보를 제공할 수 있습니다. 이 속성을 true로 설정하면 기본 함수 로깅이 함수 입출력의 전체 내용을 기록하게 됩니다.|
|LogReplayEvents|false|Application Insights에 이벤트를 재생하는 오케스트레이션을 작성할 것인지 여부를 나타내는 값입니다.|
|EventGridTopicEndpoint ||Azure Event Grid 사용자 지정 항목 엔드포인트의 URL입니다. 이 속성이 설정되면 오케스트레이션 수명 주기 알림 이벤트가 이 엔드포인트에 게시됩니다. 이 속성은 앱 설정 해결을 지원합니다.|
|EventGridKeySettingName ||`EventGridTopicEndpoint`에서 Azure Event Grid 사용자 지정 항목으로 인증하는 데 사용되는 키를 포함하는 앱 설정의 이름입니다.|
|EventGridPublishRetryCount|0|Event Grid 항목에 게시가 실패하는 경우 다시 시도 횟수입니다.|
|EventGridPublishRetryInterval|5분|*hh:mm:ss* 형식의 Event Grid 게시 재시도 간격입니다.|

이중 대부분은 성능 최적화를 위한 것입니다. 자세한 내용은 [성능 및 크기 조정](../articles/azure-functions/durable-functions-perf-and-scale.md)을 참조하세요.