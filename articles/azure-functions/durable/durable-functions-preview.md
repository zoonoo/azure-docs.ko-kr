---
title: 미리 보기 기능-Azure Functions 지 속성 함수
description: Durable Functions에 대 한 미리 보기 기능에 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.date: 04/23/2019
ms.author: azfuncdf
ms.openlocfilehash: 8ceb84ab9e9c41ff6a9cbde62571fb12ae67d790
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596070"
---
# <a name="durable-functions-20-preview-azure-functions"></a>영 속 Functions 2.0 미리 보기 (Azure Functions)

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. 없는 경우 이미 친숙 한 지 속성 함수를 사용 하 여, 참조를 [개요 문서](durable-functions-overview.md)합니다.

Durable Functions는 Azure Functions의 GA (일반 공급) 기능 하지만 현재 공개 미리 보기에 있는 여러 하위 기능이 포함 되어 있습니다. 이 문서는 새로 출시 된 미리 보기 기능에 설명 하 고 작동 방식 및 사용을 시작할 수는 방법의 세부 정보로 이동.

> [!NOTE]
> 현재는 지 속성 함수 2.0 릴리스의 일부인이 미리 보기 기능을 **알파 품질 릴리스** 몇 가지 주요 변경 내용으로 합니다. Azure Functions 지 속성 확장 패키지 빌드 형식의 버전을 사용 하 여 nuget.org에서 찾을 수 **2.0.0-alpha**합니다. 이러한 빌드 모든 프로덕션 워크 로드에 적합 하지 않으며 후속 릴리스에서 추가 주요 변경 내용에 포함 될 수 있습니다.

## <a name="breaking-changes"></a>호환성이 손상되는 변경

몇 가지 주요 변경 사항이 지 속성 함수 2.0에 도입 됩니다. 기존 응용 프로그램은 코드 변경 없이 지 속성 함수 2.0과 호환 되도록 사용할 수 없습니다. 이 섹션에서는 일부 변경 내용을 나열합니다.

### <a name="dropping-net-framework-support"></a>.NET Framework 지원 삭제

지 속성 함수 2.0에 대 한.NET Framework (및 따라서 함수 1.0)에 대 한 지원을 삭제 했습니다. 기본 원인은 쉽게 빌드하고 macOS 및 Linux 플랫폼에서 지 속성 함수에 변경한 내용을 테스트 하려면 비-Windows 참가자를 사용 하도록 설정 하는 것입니다. 보조 이유 개발자가 Azure Functions 런타임의 최신 버전으로 전환 하는 데 도움이 됩니다.

### <a name="hostjson-schema"></a>Host.json 스키마

다음 코드 조각은 host.json에 대 한 새 스키마를 보여 줍니다. 알아야 할 주요 변경 내용이 새 `"storageProvider"` 섹션인 및 `"azureStorage"` 아래의 섹션입니다. 이 변경을 지원 하기 위해 수행한 [저장소 공급자를 대체](durable-functions-preview.md#alternate-storage-providers)합니다.

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
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "traceInputAndOutputs": <bool?>,
      "eventGridTopicEndpoint": <string?>,
      "eventGridKeySettingName": <string?>,
      "eventGridPublishRetryCount": <string?>,
      "eventGridPublishRetryInterval": <hh:mm:ss?>,
      "eventGridPublishRetryHttpStatus": <int[]?>,
      "eventgridPublishEventTypes": <string[]?>,
      "customLifeCycleNotificationHelperType"
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "logReplayEvents": <bool?>
  }
}
```

지 속성 함수 2.0 안정화 계속, 더 많은 내용을 소개 합니다 `durableTask` host.json 섹션입니다. 이러한 변경 내용에 대 한 자세한 내용은 참조 하세요. [이 GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/641)합니다.

### <a name="public-interface-changes"></a>공용 인터페이스 변경

Durable Functions에서 지 원하는 다양 한 "컨텍스트" 개체는 단위 테스트에 사용할 목적으로 하는 추상 기본 클래스를 했습니다. 지 속성 함수 2.0의 일부로, 이러한 추상 기본 클래스 인터페이스를 사용 하 여 대체 되었습니다. 구체적인 형식을 직접 사용 하는 함수 코드를 받지 않습니다.

다음 표에서 주요 변경 내용을 나타냅니다.

| 이전 형식 | 새 형식 |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

추상 기본 클래스 가상 메서드를 포함 하는 있는 경우에서 이러한 가상 메서드 바뀌었습니다 확장 메서드가 정의 `DurableContextExtensions`합니다.

## <a name="entity-functions"></a>엔터티 함수

엔터티 함수 읽고 소량의 이라는 상태를 업데이트 하기 위한 작업을 정의할 *영구 엔터티*합니다. 오 케 스트레이 터 함수와 마찬가지로 엔터티 함수는 특별 한 트리거 형식과 함수 *엔터티 트리거*합니다. 오 케 스트레이 터 함수와 달리 엔터티 함수는 특정 코드 제약 조건을 갖지 않습니다. 엔터티 함수 상태를 관리할 수도 암시적으로 제어 흐름을 통해 상태를 나타내는 대신 명시적으로 합니다.

다음 코드는 간단한 엔터티 함수를 정의 하는 예제는 *카운터* 엔터티. 세 가지 작업을 정의 하는 함수 `add`, `subtract`, 및 `reset`각각의 정수 값을 업데이트 하는 `currentValue`합니다.

```csharp
[FunctionName("Counter")]
public static async Task Counter(
    [EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    int operand = ctx.GetInput<int>();

    switch (ctx.OperationName)
    {
        case "add":
            currentValue += operand;
            break;
        case "subtract":
            currentValue -= operand;
            break;
        case "reset":
            await SendResetNotificationAsync();
            currentValue = 0;
            break;
    }

    ctx.SetState(currentValue);
}
```

엔터티 *인스턴스* 고유 식별자를 통해 액세스 되는 *엔터티 ID*합니다. 엔터티 ID는 단순히 한 쌍의 문자열 엔터티 인스턴스를 고유 하 게 식별 하는. ASE는 다음으로 구성됩니다.

1. **엔터티 이름**: (예를 들어, "카운터")는 엔터티의 형식을 식별 하는 이름
2. **엔터티 키**: 기타 모든 엔터티를 동일한 이름 (예를 들어, GUID) 간에 엔터티를 고유 하 게 식별 하는 문자열

예를 들어, 한 *카운터* 엔터티 함수 온라인 게임의 점수를 유지 하는 데 사용할 수 있습니다. 게임의 각 인스턴스에 해야 고유한 엔터티 ID가 같은 `@Counter@Game1`, `@Counter@Game2`등입니다.

### <a name="comparison-with-virtual-actors"></a>가상 행위자를 사용 하 여 비교

영향 영구 엔터티 디자인을 크게 받습니다 합니다 [행위자 모델](https://en.wikipedia.org/wiki/Actor_model)합니다. 행위자에 익숙한 경우 영구 엔터티 개념 익숙할 수 해야 합니다. 특히, 영구 엔터티 비슷합니다 [가상 행위자](https://research.microsoft.com/en-us/projects/orleans/) 다양 한 방식에서:

* 영구 엔터티를 통해 주소를 지정할 수는 *엔터티 ID*합니다.
* 지속형 엔터티 작업 경합을 방지 하기 위해 한 번에 하나씩 순차적으로 실행 합니다.
* 영구 엔터티 호출 되거나 신호를 받을 때 자동으로 만들어집니다.
* 작업 중 영구 엔터티 메모리에서 자동으로 로드 되지 않습니다.

몇 가지 중요 한 차이점이, 주목할 만한 않습니다.

* 순수 함수로 영구 엔터티 모델링 됩니다. 이 디자인 클래스, 속성 및 메서드에 대 한 언어별 지원을 사용 하 여 행위자를 나타내는 대부분의 개체 지향 프레임 워크와 다릅니다.
* 영구 엔터티 우선 순위를 지정 *내구성* 위에 *대기 시간*, 따라서 않을 엄격한 대기 시간 요구 사항이 있는 응용 프로그램에 적합 합니다.
* 엔터티 간에 전송 된 메시지는 안정적이 고 순서 대로 배달 됩니다.
* 영구 엔터티 지 속성 오케스트레이션과 함께에서 사용할 수 있으며이 문서의 뒷부분에서 설명 하는 분산된 잠금을 역할도 할 수 있습니다.
* 엔터티에서 요청/응답 패턴은 오케스트레이션 제한 됩니다. 엔터티를 엔터티 통신에 대 한 단방향 메시지만 (라고도 "신호")와 원래 행위자 모델이 허용 됩니다. 이 동작은 분산된 교착 상태를 방지합니다.

### <a name="durable-entity-apis"></a>영 속 엔터티 Api

엔터티 지원 여러 Api를 포함 합니다. 한 가지는 경우 새 API 엔터티 함수 정의 대 한 위에 표시 된 대로 작업 엔터티에 대해 호출 될 때 수행할 동작을 지정 하는 또한 엔터티 상호 작용에 대 한 새로운 기능을 사용 하 여 클라이언트 및 오케스트레이션 기존 Api 업데이트 되었습니다.

### <a name="implementing-entity-operations"></a>엔터티 작업 구현

엔터티에 대 한 작업의 실행 컨텍스트 개체에서 이러한 멤버를 호출할 수 있습니다 (`IDurableEntityContext` .net에서):

* **OperationName**: 작업의 이름을 가져옵니다.
* **GetInput\<T >**: 작업에 대 한 입력을 가져옵니다.
* **GetState\<T >**: 엔터티의 현재 상태를 가져옵니다.
* **SetState**: 엔터티의 상태를 업데이트 합니다.
* **SignalEntity**: 엔터티의에 단방향 메시지를 보냅니다.
* **자체**: 엔터티의 ID를 가져옵니다.
* **반환**: 클라이언트 또는 작업 호출에 값을 반환 합니다.
* **IsNewlyConstructed**: 반환 `true` 엔터티 작업 전에 존재 하지 않은 경우.
* **DestructOnExit**: 작업을 완료 한 후 엔터티를 삭제 합니다.

작업은 오케스트레이션 보다 덜 제한 됩니다.

* 작업 (좋습니다 비동기 것만을 사용 하 여) 하는 동기 또는 비동기 Api를 사용 하 여 외부 I/O를 호출할 수 있습니다.
* 작업은 명확 하지 않은 수 있습니다. 호출 하는 예를 들어 `DateTime.UtcNow`하십시오 `Guid.NewGuid()` 또는 `new Random()`합니다.

### <a name="accessing-entities-from-clients"></a>엔터티 클라이언트에서 액세스

영구 엔터티를 통해 일반 함수에서 호출할 수는 `orchestrationClient` 바인딩 (`IDurableOrchestrationClient` .NET에서). 다음 메서드가 지원 됩니다.

* **ReadEntityStateAsync\<T >**: 엔터티의 상태를 읽습니다.
* **SignalEntityAsync**: 엔터티를 단방향 메시지를 보내고 큐에 넣을 수 되기를 기다립니다.

이러한 메서드 일관성 보다 성능을 우선: `ReadEntityStateAsync` 유효 하지 않은 값을 반환할 수 있습니다 및 `SignalEntityAsync` 작업이 완료 되기 전에 반환할 수 있습니다. 반면에 (설명한 다음) 엔터티 오케스트레이션에 서 호출는 강력한 일관성입니다.

### <a name="accessing-entities-from-orchestrations"></a>오케스트레이션에 서 엔터티 액세스

오케스트레이션에 서 context 개체를 사용 하 여 엔터티를 액세스할 수 있습니다. 단방향 통신 중에서 선택할 수 있습니다 (시작 하 고 잊어버릴) 및 양방향 통신 (요청 및 응답). 해당 메서드는

* **SignalEntity**: 엔터티의에 단방향 메시지를 보냅니다.
* **CallEntityAsync**: 엔터티를 메시지를 보내고 응답 표시 하는 작업이 완료 될 때까지 대기 합니다.
* **CallEntityAsync\<T >**: 엔터티를 메시지를 보내고 T. 형식의 결과 포함 하는 응답 대기

양방향 통신을 사용할 경우 작업을 실행 하는 동안 throw 된 예외도 호출 오케스트레이션으로 전송 되며 다시 throw 합니다. 반면, 실행 후 제거를 사용 하는 경우 예외 관찰 되지 됩니다.

### <a name="locking-entities-from-orchestrations"></a>오케스트레이션에 서 잠금 엔터티

오케스트레이션 entities 잠글 수 있습니다. 이 기능은 사용 하 여 원치 않는 경합을 방지 하는 간단한 방법을 *임계*합니다.

컨텍스트 개체는 다음 메서드를 제공합니다.

* **LockAsync**: 하나 이상의 엔터티에 대 한 잠금을 획득 합니다.
* **IsLocked**: true를 반환 하는 경우 현재 임계 섹션을 false 그렇지 않은 경우.

중요 섹션 종료 및 모든 잠금 출시 되 면 오케스트레이션이 종료 될 때입니다. .NET에서는 `LockAsync` 반환를 `IDisposable` 끝나는 중요 섹션을 삭제 하는 경우와 함께 사용할 수 있는 `using` 절을 중요 섹션의 구문 표현을 가져옵니다.

예를 들어 두 플레이어는 사용할 수 있는지 여부를 테스트 해야 하는 오케스트레이션을 고려 하 고 게임에 모두 할당 합니다. 이 작업은 다음과 같이 중요 한 섹션을 사용 하 여 구현할 수 있습니다.

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

중요 한 섹션에서 두 플레이어 엔터티가 잠겨 즉, 중요 섹션 내에서 호출 되는 것 이외의 모든 작업 실행). 충돌 하는 작업을 사용 하 여 경합을 방지 하는이 동작, 예: 다른 할당할 플레이어 게임, 또는 서명 해제 합니다.

몇 가지 제한 사항을 적용 했습니다 어떻게 중요 섹션에 사용할 수 있습니다. 이러한 제한 사항은 교착 상태 및 재진입 방지를 제공 합니다.

* 임계 영역을 중첩할 수 없습니다.
* 임계 영역 suborchestrations를 만들 수 없습니다.
* 중요 섹션에는 이러한을 잠갔을 엔터티만 호출할 수 있습니다.
* 중요 섹션에는 동일한 엔터티의 여러 병렬 호출을 사용 하 여 호출할 수 없습니다.
* 임계 영역 잠기지 없는 엔터티만 신호를 보낼 수 있습니다.

## <a name="alternate-storage-providers"></a>대체 저장소 공급자

지 속성 작업 프레임 워크에서는 여러 저장소 공급자 오늘 포함 [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage)를 [Azure Service Bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), [메모리 에뮬레이터](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator), 테스트 및 [Redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) 공급자입니다. 그러나 지금까지 Azure Functions의 지 속성 작업 확장만 지원 되는 Azure Storage 공급자입니다. 대체 저장소 공급자에 대 한 지원이 지 속성 함수 2.0 부터는 추가, Redis 공급자를 사용 하 여 시작 합니다.

> [!NOTE]
> 지 속성 함수 2.0는.NET Standard 2.0 호환 공급자만 지원합니다. 작성할 당시 Azure Service Bus 공급자.NET Standard 2.0을 지원 하지 않습니다 이므로 없습니다 대체 저장소 공급자를 사용할 수 있습니다.

### <a name="emulator"></a>에뮬레이터

합니다 [DurableTask.Emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) 공급자는 로컬 메모리를 영구 저장소 공급자 로컬 테스트 시나리오에 적합 합니다. 최소한 다음을 사용 하 여 구성할 수 있습니다 **host.json** 스키마:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (실험적)

합니다 [DurableTask.Redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) 공급자 Redis 클러스터를 구성된 하려면 모든 오케스트레이션 상태를 유지 합니다.

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

`connectionStringName` 앱 설정 또는 환경 변수 이름을 참조 해야 합니다. 해당 앱 설정 또는 환경 변수 형태로 Redis 연결 문자열 값을 포함 해야 *서버: 포트*합니다. 예를 들어 `localhost:6379` 로컬 Redis 클러스터에 연결 합니다.

> [!NOTE]
> Redis 공급자는 현재 실험적 및 단일 노드에서 실행 되는 함수 앱만 지원 합니다.
