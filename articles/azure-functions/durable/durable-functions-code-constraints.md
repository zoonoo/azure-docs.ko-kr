---
title: 지속적 오케스트레이터 코드 제약 조건 - Azure 함수
description: Azure 지속 형 함수에 대 한 오케스트레이션 함수 재생 및 코드 제약 조건입니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4ed604302ca187ad4953e865d68dc73030a37c02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562142"
---
# <a name="orchestrator-function-code-constraints"></a>오케스트레이터 함수 코드 제약 조건

지속형 함수는 상태 확장 앱을 빌드할 수 있는 [Azure Functions의](../functions-overview.md) 확장입니다. [오케스트레이터 함수를](durable-functions-orchestrations.md) 사용하여 함수 앱 내에서 다른 지속가능한 함수의 실행을 오케스트레이션할 수 있습니다. 오케스트레이터 함수는 상태 조정이 용이하고 안정적이며 잠재적으로 장기 실행됩니다.

## <a name="orchestrator-code-constraints"></a>오케스트레이터 코드 제약 조건

오케스트레이터 함수는 [이벤트 소싱을](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) 사용하여 안정적인 실행을 보장하고 로컬 변수 상태를 유지합니다. 오케스트레이터 코드의 [재생 동작은](durable-functions-orchestrations.md#reliability) 오케스트레이터 함수에서 작성할 수 있는 코드 형식에 대한 제약 조건을 만듭니다. 예를 들어 오케스트레이터 함수는 *결정적이어야*합니다: 오케스트레이터 함수는 여러 번 재생되며 매번 동일한 결과를 생성해야 합니다.

### <a name="using-deterministic-apis"></a>결정적인 API 사용

이 섹션에서는 코드가 결정적이지 확인하는 데 도움이 되는 몇 가지 간단한 지침을 제공합니다.

오케스트레이터 함수는 대상 언어의 모든 API를 호출할 수 있습니다. 그러나 오케스트레이터 함수는 결정적 API만 호출하는 것이 중요합니다. *결정적 API는* 호출되는 시기나 빈도에 관계없이 동일한 입력을 통해 항상 동일한 값을 반환하는 API입니다.

다음 표에서는 결정적이지 않으므로 피해야 하는 API의 예를 보여 주며, 이러한 API는 결정적이지 *않습니다.* 이러한 제한은 오케스트레이터 함수에만 적용됩니다. 다른 함수 형식에는 이러한 제한이 없습니다.

| API 범주 | 이유 | 해결 방법 |
| ------------ | ------ | ---------- |
| 날짜 및 시간  | 반환된 값이 각 재생마다 다르기 때문에 현재 날짜 또는 시간을 반환하는 API는 결정적이지 않습니다. | .NET의`CurrentUtcDateTime` API 또는 `currentUtcDateTime` JavaScript의 API를 사용하여 재생할 수 있습니다. |
| GUID및UU중고  | 생성된 값은 각 리플레이마다 다르기 때문에 임의의 GUID 또는 UUID를 반환하는 API는 결정적이지 않습니다. | .NET 또는 `NewGuid` `newGuid` 자바스크립트에서 사용하여 임의의 GUID를 안전하게 생성할 수 있습니다. |
| 난수 | 생성된 값은 각 재생마다 다르기 때문에 난수를 반환하는 API는 결정적이지 않습니다. | 활동 함수를 사용하여 난수를 오케스트레이션으로 반환합니다. 활동 함수의 반환 값은 항상 재생에 안전합니다. |
| 바인딩 | 입력 및 출력 바인딩은 일반적으로 I/O를 수행하며 결정적이지 않습니다. 오케스트레이터 함수는 [오케스트레이션 클라이언트와](durable-functions-bindings.md#orchestration-client) [엔터티 클라이언트](durable-functions-bindings.md#entity-client) 바인딩도 직접 사용해서는 안 됩니다. | 클라이언트 또는 활동 함수 내에서 입력 및 출력 바인딩을 사용합니다. |
| 네트워크 | 네트워크 호출에는 외부 시스템이 포함되며 결정적이지 않습니다. | 활동 함수를 사용하여 네트워크 통화를 합니다. 오케스트레이터 함수에서 HTTP 호출을 수행해야 하는 경우 [내구성있는 HTTP API를](durable-functions-http-features.md#consuming-http-apis)사용할 수도 있습니다. |
| API 차단 | .NET 및 `Thread.Sleep` 유사한 API와 같은 API를 차단하면 오케스트레이터 함수의 성능 및 배율 문제가 발생할 수 있으므로 피해야 합니다. Azure Functions 소비 계획에서는 불필요한 런타임 요금이 발생할 수도 있습니다. | API를 사용할 수 있을 때 차단하는 대신 사용할 수 있습니다. 예를 들어 `CreateTimer` 오케스트레이션 실행이 지연될 때 를 사용합니다. [지속형 타이머](durable-functions-timers.md) 지연은 오케스트레이터 함수의 실행 시간에 포함되지 않습니다. |
| 비동기 API | 오케스트레이터 코드는 `IDurableOrchestrationContext` API 또는 `context.df` 개체의 API를 사용하지 않는 한 비동기 작업을 시작해서는 안 됩니다. 예를 들어 .NET 및 `Task.Run` `HttpClient.SendAsync` `setTimeout` `setInterval` `Task.Delay`JavaScript에서 을 사용할 수 없습니다. 지속적 작업 프레임워크는 단일 스레드에서 오케스트레이터 코드를 실행합니다. 다른 비동기 API에서 호출할 수 있는 다른 스레드와 상호 작용할 수 없습니다. | 오케스트레이터 함수는 내구성있는 비동기 호출만 수행해야 합니다. 활동 함수는 다른 비동기 API 호출을 수행해야 합니다. |
| 비동기 자바 스크립트 기능 | node.js 런타임이 비동기 `async` 함수가 결정적임을 보장하지 않기 때문에 JavaScript 오케스트레이터 함수를 선언할 수 없습니다. | JavaScript 오케스트레이터 함수를 동기 생성기 함수로 선언합니다. |
| 스레딩 API | 지속적 작업 프레임워크는 단일 스레드에서 오케스트레이터 코드를 실행하며 다른 스레드와 상호 작용할 수 없습니다. 오케스트레이션의 실행에 새 스레드를 도입하면 결정적이지 않은 실행 또는 교착 상태가 발생할 수 있습니다. | 오케스트레이터 함수는 스레딩 API를 거의 사용하지 않아야 합니다. 예를 들어 .NET에서 다음을 사용하지 `ConfigureAwait(continueOnCapturedContext: false)`마십시오. 이렇게 하면 오케스트레이터 함수의 원본 `SynchronizationContext`에서 작업 연속이 실행됩니다. 이러한 API가 필요한 경우 해당 API의 사용을 활동 기능으로만 제한합니다. |
| 정적 변수 | 오케스트레이터 함수에서 비정수정적 변수를 사용하지 마십시오. | 상수를 사용하거나 정적 변수의 사용을 활동 함수로 제한합니다. |
| 환경 변수 | 오케스트레이터 함수에서 환경 변수를 사용하지 마십시오. 해당 값은 시간이 지남에 따라 변경될 수 있으므로 결정적이지 않은 런타임 동작이 발생합니다. | 환경 변수는 클라이언트 함수 또는 활동 함수 내에서만 참조해야 합니다. |
| 무한 루프 | 오케스트레이터 함수에는 무한 루프를 사용하지 않도록 방지하세요. 지속적 작업 프레임워크는 오케스트레이션 함수가 진행됨에 따라 실행 기록을 저장하므로 무한 루프로 인해 오케스트레이터 인스턴스의 메모리가 부족해질 수 있습니다. | 무한 루프 시나리오의 경우 `ContinueAsNew` .NET 또는 `continueAsNew` JavaScript와 같은 API를 사용하여 함수 실행을 다시 시작하고 이전 실행 기록을 삭제합니다. |

이러한 제약 조건을 적용하는 것은 처음에는 어려워 보일 수 있지만 실제로는 따라하기 쉽습니다.

지속적 작업 프레임워크는 이전 규칙의 위반을 검색하려고 시도합니다. 위반이 발견되면 프레임워크는 **비결정적오케스트라예외를 throw합니다.** 그러나 이 검색 동작이 모든 위반을 catch하지는 않으며 이에 의존해서는 안 됩니다.

## <a name="versioning"></a>버전 관리

내구성있는 오케스트레이션은 며칠, 몇 달, 몇 년 또는 영원히 계속 실행될 수 [있습니다.](durable-functions-eternal-orchestrations.md) 완료되지 않은 오케스트레이션에 영향을 주는 내디언트 함수 앱에 대한 코드 업데이트는 오케스트레이션의 재생 동작을 중단할 수 있습니다. 따라서 코드를 업데이트할 때 신중하게 계획하는 것이 중요합니다. 코드 버전 링하는 방법에 대한 자세한 설명은 [버전 검토 문서를](durable-functions-versioning.md)참조하십시오.

## <a name="durable-tasks"></a>지속성 작업

> [!NOTE]
> 이 섹션에서는 지속성 작업 프레임워크의 내부 구현에 대해 자세히 설명합니다. 이 정보를 알지 못해도 지속형 함수를 사용할 수 있습니다. 여기서는 재생 동작을 이해하는 데에만 도움이 됩니다.

오케스트레이터 함수에서 안전하게 기다릴 수 있는 작업을 경우에 따라 *지속형 작업이라고*합니다. 지속형 작업 프레임워크는 이러한 작업을 만들고 관리합니다. 예를 들어 **CallActivityAsync,** **WaitForExternalEvent**및 .NET 오케스트레이터 함수의 **CreateTimer에서** 반환되는 작업이 있습니다.

이러한 지속형 작업은 .NET의 `TaskCompletionSource` 개체 목록에 의해 내부적으로 관리됩니다. 재생 중에 이러한 작업은 오케스트레이터 코드 실행의 일부로 만들어집니다. 디스패처가 해당 기록 이벤트를 연수할 때 완료됩니다.

모든 기록이 재생될 때까지 단일 스레드를 사용하여 작업이 동기적으로 실행됩니다. 역사 재생이 끝날 때까지 완료되지 않은 지속 적 작업은 적절한 작업을 수행합니다. 예를 들어 활동 함수를 호출하기 위해 메시지가 큐에 대기될 수 있습니다.

이 섹션의 런타임 동작 설명은 오케스트레이터 함수가 사용할 `await` `yield` 수 없거나 지속되지 않는 작업에서 사용할 수 없는 이유를 이해하는 데 도움이 됩니다. 디스패처 스레드는 작업이 완료될 때까지 기다릴 수 없으며 해당 작업의 콜백이 오케스트레이터 함수의 추적 상태를 손상시킬 수 있습니다. 이러한 위반을 감지하는 데 도움이 되는 일부 런타임 검사가 있습니다.

지속적 작업 프레임워크가 오케스트레이터 함수를 실행하는 방법에 대해 자세히 알아보려면 [GitHub의 지속적 작업 소스 코드를](https://github.com/Azure/durabletask)참조하십시오. 특히 [TaskOrchestrationExecutor.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationExecutor.cs) 및 [TaskOrchestrationContext.cs](https://github.com/Azure/durabletask/blob/master/src/DurableTask.Core/TaskOrchestrationContext.cs).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [하위 오케스트레이션을 호출하는 방법 알아보기](durable-functions-sub-orchestrations.md)

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)
