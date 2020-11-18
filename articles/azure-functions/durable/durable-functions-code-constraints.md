---
title: 영 속 orchestrator 코드 제약 조건-Azure Functions
description: Azure Durable Functions에 대 한 오케스트레이션 함수 재생 및 코드 제약 조건입니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: dc301cf7149ad9fcd5bd5c02226afedc4df5e3ee
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833098"
---
# <a name="orchestrator-function-code-constraints"></a>Orchestrator 함수 코드 제약 조건

Durable Functions은 상태 저장 앱을 빌드할 수 있는 [Azure Functions](../functions-overview.md) 의 확장입니다. [Orchestrator 함수](durable-functions-orchestrations.md) 를 사용 하 여 함수 앱 내에서 다른 지 속성 함수의 실행을 오케스트레이션 할 수 있습니다. 오 케 스트레이 터 함수는 상태 저장, 신뢰할 수 있으며 장기 실행 될 수 있습니다.

## <a name="orchestrator-code-constraints"></a>오케스트레이터 코드 제약 조건

Orchestrator 함수는 [이벤트 소싱](/azure/architecture/patterns/event-sourcing) 을 사용 하 여 신뢰할 수 있는 실행을 보장 하 고 로컬 변수 상태를 유지 합니다. Orchestrator 코드의 [재생 동작은](durable-functions-orchestrations.md#reliability) orchestrator 함수에서 작성할 수 있는 코드 형식에 대 한 제약 조건을 만듭니다. 예를 들어 오 케 스트레이 터 함수는 *결정적* 이어야 합니다. 오 케 스트레이 터 함수는 여러 번 재생 되며 매번 동일한 결과를 생성 해야 합니다.

### <a name="using-deterministic-apis"></a>결정적 Api 사용

이 섹션에서는 코드를 결정적으로 확인 하는 데 도움이 되는 몇 가지 간단한 지침을 제공 합니다.

Orchestrator 함수는 대상 언어의 모든 API를 호출할 수 있습니다. 그러나 오 케 스트레이 터 함수는 결정적 Api만 호출 하는 것이 중요 합니다. *결정적 api* 는 동일한 입력에서 항상 동일한 값을 반환 하는 api로,이를 호출 하는 시기 또는 빈도에 관계 없이 항상 동일한 값을 반환 합니다.

다음 표에서 *는 결정적이 지 않기* 때문에 사용 하지 않아야 하는 api의 예를 보여 줍니다. 이러한 제한은 오 케 스트레이 터 함수에만 적용 됩니다. 다른 함수 형식에는 이러한 제한이 없습니다.

| API 범주 | 이유 | 해결 방법 |
| ------------ | ------ | ---------- |
| 날짜 및 시간  | 반환 된 값은 각 재생 마다 다르므로 현재 날짜 또는 시간을 반환 하는 Api는 비결 정적입니다. | .NET의 [CurrentUtcDateTime](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.currentutcdatetime) 속성, `currentUtcDateTime` JavaScript의 api 또는 `current_utc_datetime` 재생에 안전한 Python의 api를 사용 합니다. |
| Guid 및 Uuid  | 임의 GUID 또는 UUID를 반환 하는 Api는 생성 된 값이 각 재생 마다 다르기 때문에 비결 정적입니다. | .NET 또는 JavaScript에서 [Newguid](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.idurableorchestrationcontext.newguid) `newGuid` 를 사용 하 여 임의의 guid를 안전 하 게 생성 합니다. |
| 난수 | 난수를 반환 하는 Api는 생성 된 값이 각 재생 마다 다르기 때문에 비결 정적입니다. | 작업 함수를 사용 하 여 임의의 숫자를 오케스트레이션에 반환 합니다. 작업 함수의 반환 값은 항상 재생에 안전 합니다. |
| 바인딩 | 입력 및 출력 바인딩은 일반적으로 i/o를 수행 하 고 비결 정적입니다. 오 케 스트레이 터 함수는 [오케스트레이션 클라이언트](durable-functions-bindings.md#orchestration-client) 및 [엔터티 클라이언트](durable-functions-bindings.md#entity-client) 바인딩을 직접 사용 하지 않아야 합니다. | 클라이언트 또는 작업 함수 내에서 입력 및 출력 바인딩을 사용 합니다. |
| 네트워크 | 네트워크 호출은 외부 시스템과 관련 되며 비결 정적입니다. | 작업 함수를 사용 하 여 네트워크 호출을 수행 합니다. Orchestrator 함수에서 HTTP 호출을 수행 해야 하는 경우 [내구성이 있는 Http api](durable-functions-http-features.md#consuming-http-apis)를 사용할 수도 있습니다. |
| Api 차단 | .NET 및 유사한 Api에서와 같은 Api를 차단 하면 오 케 스트레이 터 `Thread.Sleep` 함수에 대 한 성능 및 확장성 문제가 발생할 수 있으므로 피해 야 합니다. Azure Functions 소비 계획에서는 불필요 한 런타임 요금이 발생할 수도 있습니다. | Api를 사용할 수 있는 경우 차단 하는 대체 방법을 사용 합니다. 예를 들어를 사용  `CreateTimer` 하 여 오케스트레이션 실행에서 지연을 발생 시킵니다. 지 [속성 타이머](durable-functions-timers.md) 지연은 오 케 스트레이 터 함수의 실행 시간을 계산 하지 않습니다. |
| 비동기 Api | Orchestrator 코드는 `IDurableOrchestrationContext` api, `context.df` JavaScript의 Api 또는 Python의 api를 사용 하는 경우를 제외 하 고는 비동기 작업을 시작 해서는 안 `context` 됩니다. 예를 들어 `Task.Run` `Task.Delay` `HttpClient.SendAsync` .net 또는 JavaScript에서, `setTimeout` 및를 사용할 수 없습니다 `setInterval` . 지 속성 작업 프레임 워크는 단일 스레드에서 orchestrator 코드를 실행 합니다. 다른 비동기 Api에서 호출 될 수 있는 다른 스레드와 상호 작용할 수 없습니다. | Orchestrator 함수는 영 속 비동기 호출만 수행 해야 합니다. 작업 함수는 다른 비동기 API 호출을 수행 해야 합니다. |
| 비동기 JavaScript 함수 | `async`node.js 런타임이 비동기 함수가 결정적 임을 보장 하지 않기 때문에 JavaScript orchestrator 함수를로 선언할 수 없습니다. | JavaScript orchestrator 함수를 동기 생성기 함수로 선언 합니다. |
| Python 코 루틴 | Python orchestrator 함수를 코 루틴로 선언할 수 없습니다. 즉, `async`코 루틴 의미 체계가 Durable Functions 재생 모델과 일치 하지 않기 때문에 키워드를 사용 하 여 선언 합니다. | Python 오 케 스트레이 터 orchestrator 함수를 생성기로 선언 합니다. 즉, API가 대신을 사용 해야 합니다 `context` `yield` `await` .   |
| 스레딩 Api | 지 속성 작업 프레임 워크는 단일 스레드에서 orchestrator 코드를 실행 하 고 다른 스레드와 상호 작용할 수 없습니다. 오케스트레이션의 실행에 새 스레드를 도입 하면 비결 정적 실행 또는 교착 상태가 발생할 수 있습니다. | Orchestrator 함수는 스레드 Api를 거의 사용 하지 않습니다. 예를 들어 .NET에서는를 사용 하지 않도록 합니다. 이렇게 하면 오 케 스트레이 터 `ConfigureAwait(continueOnCapturedContext: false)` 함수의 원래에서 작업 연속이 실행 `SynchronizationContext` 됩니다. 이러한 Api가 필요한 경우 작업 함수만 사용 하도록 제한 합니다. |
| 정적 변수 | Orchestrator 함수에서는 시간이 지남에 따라 값이 변경 될 수 있으므로 비상수 정적 변수를 사용 하지 않는 것이 좋습니다. 이렇게 하면 비결 정적 런타임 동작이 발생 합니다. | 상수를 사용 하거나 정적 변수를 활동 함수로 사용 하도록 제한 합니다. |
| 환경 변수 | Orchestrator 함수에서 환경 변수를 사용 하지 마세요. 해당 값은 시간이 지남에 따라 변경 될 수 있으므로 비결 정적 런타임 동작이 발생 합니다. | 환경 변수는 클라이언트 함수 또는 작업 함수 내 에서만 참조 해야 합니다. |
| 무한 루프 | 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 지 속성 작업 프레임 워크는 오케스트레이션 함수가 진행 됨에 따라 실행 기록을 저장 하므로 무한 루프를 수행 하면 orchestrator 인스턴스의 메모리가 부족 해질 수 있습니다. | 무한 루프 시나리오의 경우 .NET, JavaScript 또는 Python에서와 같은 Api를 사용 `ContinueAsNew` `continueAsNew` 하 여 `continue_as_new` 함수 실행을 다시 시작 하 고 이전 실행 기록을 삭제 합니다. |

이러한 제약 조건을 적용 하는 것은 처음에 어려울 수 있지만 실제로는 쉽게 따를 수 있습니다.

지 속성 작업 프레임 워크는 이전 규칙의 위반을 감지 하려고 합니다. 위반을 발견 하면 프레임 워크에서 **NonDeterministicOrchestrationException** 예외를 throw 합니다. 그러나이 검색 동작은 모든 위반을 catch 하지 않으며이에 종속 되지 않아야 합니다.

## <a name="versioning"></a>버전 관리

지 속성 오케스트레이션은 일, 월, 년 또는 심지어 [eternally](durable-functions-eternal-orchestrations.md)계속 실행 될 수 있습니다. 완료 되지 않은 오케스트레이션에 영향을 주는 Durable Functions 앱에 대 한 모든 코드 업데이트는 오케스트레이션의 재생 동작을 중단할 수 있습니다. 따라서 코드를 업데이트할 때 신중 하 게 계획 하는 것이 중요 합니다. 코드 버전을 지정 하는 방법에 대 한 자세한 설명은 [버전 관리 문서](durable-functions-versioning.md)를 참조 하세요.

## <a name="durable-tasks"></a>지속성 작업

> [!NOTE]
> 이 섹션에서는 지속성 작업 프레임워크의 내부 구현에 대해 자세히 설명합니다. 이 정보를 몰라도 지 속성 함수를 사용할 수 있습니다. 여기서는 재생 동작을 이해하는 데에만 도움이 됩니다.

오 케 스트레이 터 함수에서 안전 하 게 대기할 수 있는 작업을 종종 *내구성이 있는 작업* 이라고 합니다. 지 속성 작업 프레임 워크는 이러한 작업을 만들고 관리 합니다. .NET orchestrator 함수에서 **Callactivityasync**, **Waitforexternalevent** 및 **createtimer** 에 의해 반환 되는 작업을 예로 들 수 있습니다.

이러한 지 속성 작업은 .NET의 개체 목록에 의해 내부적으로 관리 됩니다 `TaskCompletionSource` . 재생 하는 동안 이러한 작업은 orchestrator 코드 실행의 일부로 생성 됩니다. 디스패처가 해당 기록 이벤트를 열거 하는 것 처럼 완료 됩니다.

작업은 모든 기록이 재생 될 때까지 단일 스레드를 사용 하 여 동기적으로 실행 됩니다. 기록 재생이 끝날 때까지 완료 되지 않은 내구성이 있는 작업에는 적절 한 작업이 수행 됩니다. 예를 들어 작업 함수를 호출 하기 위해 메시지를 큐에 넣을 수 있습니다.

이 섹션의 런타임 동작에 대 한 설명은 오 케 스트레이 터 함수 `await` 에서 또는 내구성이 없는 작업에서 사용할 수 없는 이유를 이해 하는 데 도움이 됩니다 `yield` . 두 가지 이유는 다음과 같습니다. 디스패처 스레드는 작업이 완료 될 때까지 기다릴 수 없으며, 해당 태스크의 콜백은 오 케 스트레이 터 함수의 추적 상태가 손상 될 수 있습니다. 이러한 위반을 검색 하는 데 도움이 되는 몇 가지 런타임 검사가 준비 되어 있습니다.

지 속성 작업 프레임 워크에서 orchestrator 함수를 실행 하는 방법에 대 한 자세한 내용은 [GitHub의 지 속성 작업 소스 코드](https://github.com/Azure/durabletask)를 참조 하세요. 특히 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 및 [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [하위 오케스트레이션을 호출 하는 방법 알아보기](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)
