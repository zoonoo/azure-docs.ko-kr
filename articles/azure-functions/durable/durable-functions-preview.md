---
title: Durable Functions 미리 보기 기능-Azure Functions
description: Durable Functions 미리 보기 기능에 대해 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933238"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2.0 미리 보기 (Azure Functions)

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. Durable Functions에 익숙하지 않은 경우 [개요 설명서](durable-functions-overview.md)를 참조 하세요.

Durable Functions 1.x는 Azure Functions의 GA (일반 공급) 기능 이지만 현재 공개 미리 보기로 제공 되는 몇 가지 하위 기능도 포함 하 고 있습니다. 이 문서에서는 새로 릴리스된 미리 보기 기능에 대해 설명 하 고 작동 방법 및 사용을 시작 하는 방법에 대해 자세히 설명 합니다.

> [!NOTE]
> 이러한 미리 보기 기능은 현재 몇 가지 주요 변경 내용이 포함 된 **미리 보기 품질 릴리스입니다** Durable Functions 2.0 릴리스에 포함 되어 있습니다. Azure Functions 영 속 확장 패키지 빌드는 **2.0.0-ax**형식의 버전을 포함 하는 nuget.org에서 찾을 수 있습니다. 이러한 빌드는 프로덕션 워크 로드를 위한 것이 아니며 후속 릴리스에는 추가 주요 변경 사항이 포함 될 수 있습니다.

## <a name="breaking-changes"></a>호환성이 손상되는 변경

Durable Functions 2.0에 몇 가지 주요 변경 사항이 도입 되었습니다. 기존 응용 프로그램은 코드를 변경 하지 않고 Durable Functions 2.0와 호환 될 것으로 예상 되지 않습니다. 이 섹션에서는 다음과 같은 몇 가지 변경 사항을 설명 합니다.

### <a name="hostjson-schema"></a>Host. json 스키마

다음 코드 조각은 호스트 json에 대 한 새 스키마를 보여 줍니다. 유의 해야 할 주요 사항은 새로운 하위 섹션입니다.

* `"storageProvider"`저장소 관련 구성 `"azureStorage"` 에 대 한 (및 하위 섹션)
* `"tracking"`추적 및 로깅 구성
* `"notifications"`event grid 알림 `"eventGrid"` 구성의 및 하위 섹션

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Durable Functions 2.0이 계속 안정화 되 면 호스트 json `durableTask` 섹션에 더 많은 변경 내용이 도입 됩니다. 이러한 변경 내용에 대 한 자세한 내용은 [이 GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/641)를 참조 하세요.

### <a name="public-interface-changes"></a>공용 인터페이스 변경

Durable Functions에서 지 원하는 다양 한 "context" 개체에는 단위 테스트에 사용 하기 위한 추상 기본 클래스가 있습니다. Durable Functions 2.0의 일부로 이러한 추상 기본 클래스는 인터페이스로 대체 되었습니다. 구체적 형식을 직접 사용 하는 함수 코드는 영향을 받지 않습니다.

다음 표는 주요 변경 내용을 나타냅니다.

| 이전 형식 | 새 형식 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

추상 기본 클래스가 가상 메서드를 포함 하는 경우 이러한 가상 메서드는에 `DurableContextExtensions`정의 된 확장 메서드로 대체 됩니다.

## <a name="entity-functions"></a>Entity 함수

Durable Functions v 2.0.0부터 시작 하 여 새로운 [엔터티 함수](durable-functions-entities.md) 개념을 도입 했습니다.

엔터티 함수는 *영 속 엔터티*라고 하는 작은 상태를 읽고 업데이트 하기 위한 작업을 정의 합니다. 오 케 스트레이 터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용 하는 함수입니다. Orchestrator 함수와 달리 entity 함수는 특정 코드 제약 조건을 포함 하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 표시 하지 않고 명시적으로 상태를 관리 합니다.

초기 사용자 피드백에 따라 나중에 Durable Functions v 2.0.0-beta1에서 엔터티에 대 한 클래스 기반 프로그래밍 모델에 대 한 지원이 추가 되었습니다.

자세히 알아보려면 [엔터티 함수](durable-functions-entities.md) 문서를 참조 하세요.

## <a name="durable-http"></a>지 속성 HTTP

Durable Functions v 2.0.0-beta2부터 다음을 수행할 수 있는 새로운 [내구성이 있는 HTTP](durable-functions-http-features.md) 기능을 도입 했습니다.

* 오케스트레이션 함수에서 직접 HTTP Api 호출 (몇 가지 문서화 된 제한 사항 있음)
* 자동 클라이언트 쪽 HTTP 202 상태 폴링을 구현 합니다.
* [Azure 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 에 대 한 기본 제공 지원

자세히 알아보려면 [HTTP 기능](durable-functions-http-features.md#consuming-http-apis) 문서를 참조 하세요.

## <a name="alternate-storage-providers"></a>대체 저장소 공급자

지 속성 작업 프레임 워크는 [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [메모리 내 에뮬레이터](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)및 실험적 [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) 공급자를 포함 하 여 오늘날 여러 저장소 공급자를 지원 합니다. 그러나 지금 까지는 Azure Functions에 대 한 지 속성 작업 확장이 Azure Storage 공급자만 지원 합니다. Durable Functions 2.0부터 대체 저장소 공급자에 대 한 지원이 Redis 공급자부터 추가 됩니다.

> [!NOTE]
> Durable Functions 2.0는 2.0 호환 공급자 .NET Standard 지원 합니다.

### <a name="emulator"></a>에뮬레이터

[Microsoft.azure.webjobs.extensions.durabletask](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) 공급자는 로컬 테스트 시나리오에 적합 한 로컬 메모리, 내구성이 없는 저장소 공급자입니다. 다음 최소 **호스트 json** 스키마를 사용 하 여 구성할 수 있습니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (실험적)

[Microsoft.azure.webjobs.extensions.durabletask Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) 공급자는 구성 된 Redis 클러스터에 모든 오케스트레이션 상태를 유지 합니다.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

는 `connectionStringName` 앱 설정 또는 환경 변수의 이름을 참조 해야 합니다. 해당 앱 설정 또는 환경 변수는 *server: port*형식의 Redis 연결 문자열 값을 포함 해야 합니다. 예를 `localhost:6379` 들어 로컬 Redis 클러스터에 연결 하는 경우입니다.

> [!NOTE]
> Redis 공급자는 현재 실험적 이며 단일 노드에서 실행 되는 함수 앱만 지원 합니다. Redis 공급자가 일반적으로 사용할 수 있게 될 것 이며 이후 릴리스에서 제거 될 수 있습니다.
