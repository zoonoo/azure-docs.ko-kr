---
title: Azure Functions에 대한 모범 사례
description: Azure Functions에 대한 모범 사례 및 패턴에 알아봅니다.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 10/16/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa85f636233a067713d127938d674b359bd03696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227379"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Azure Functions의 성능 및 안정성 최적화

이 문서에서는 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 함수 앱의 성능 및 안정성을 개선하기 위한 지침을 제공합니다.  

## <a name="general-best-practices"></a>일반 모범 사례

Azure Functions를 사용하여 서버가 없는 솔루션을 빌드하고 설계하는 방법의 모범 사례는 다음과 같습니다.

### <a name="avoid-long-running-functions"></a>장기 실행 함수 방지

큰 장기 실행 함수는 예기치 않은 시간 초과 문제를 발생시킬 수 있습니다. To learn more about the timeouts for a given hosting plan, see [function app timeout duration](functions-scale.md#timeout). 

A function can become large because of many Node.js dependencies. 또한 종속성을 가져올 때 로드 시간이 증가하여 예기치 않은 시간 초과가 발생할 수 있습니다. 종속성은 명시적 및 암시적으로 로드됩니다. 코드를 통해 로드되는 단일 모듈은 자체 추가 모듈을 로드할 수 있습니다. 

큰 함수를 더 작은 함수 집합으로 리팩터링할 때마다 함께 작동하고 빠른 응답을 반환합니다. For example, a webhook or HTTP trigger function might require an acknowledgment response within a certain time limit; it's common for webhooks to require an immediate response. HTTP 트리거 페이로드를 큐 트리거 함수에 의해 처리되도록 큐에 전달할 수 있습니다. This approach lets you defer the actual work and return an immediate response.


### <a name="cross-function-communication"></a>함수 통신 교차

[지속형 함수](durable/durable-functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)는 여러 함수 간에 상태 전환 및 통신을 관리하도록 빌드됩니다.

If not using Durable Functions or Logic Apps to integrate with multiple functions, it's best to use storage queues for cross-function communication. The main reason is that storage queues are cheaper and much easier to provision than other storage options. 

스토리지 큐에 있는 개별 메시지 크기는 64KB로 제한됩니다. 함수 간에 더 큰 메시지를 전달해야 하는 경우 Azure Service Bus 큐를 사용하면 표준 계층에서는 최대 256KB, 프리미엄 계층에서는 최대 1MB의 메시지를 지원할 수 있습니다.

Service Bus 토픽은 메시지를 처리하기 전에 필터링해야 하는 경우에 유용합니다.

이벤트 허브는 고용량 통신을 지원하는 데 유용합니다.


### <a name="write-functions-to-be-stateless"></a>상태 비저장 함수 작성 

함수는 가능하면 상태 비저장이며 idempotent여야 합니다. 필요한 모든 상태 정보를 사용자 데이터에 연결합니다. 예를 들어 처리할 주문에 연결된 `state` 멤버가 있을 수 있습니다. 함수는 주문을 해당 상태에 따라 처리하며 함수 자체는 비저장 상태로 남아 있을 수 있습니다. 

Idempotent 함수는 특히 타이머 트리거 사용이 권장됩니다. For example, if you have something that absolutely must run once a day, write it so it can run anytime during the day with the same results. The function can exit when there's no work for a particular day. 또한 이전 실행이 완료되지 못한 경우 다음 실행은 중단되었던 부분으로 돌아가야 합니다.


### <a name="write-defensive-functions"></a>방어적 함수 작성

함수에는 언제든지 예외가 발생할 수 있다고 가정합니다. 다음 실행 동안 이전 실패 지점에서 계속할 수 있는 기능을 넣어 함수를 설계합니다. 다음과 같은 작업이 필요한 시나리오를 고려하세요.

1. Query for 10,000 rows in a database.
2. 앞으로 처리할 각 행에 대한 큐 메시지를 만듭니다.
 
Depending on how complex your system is, you may have: involved downstream services behaving badly, networking outages, or quota limits reached, etc. All of these can affect your function at any time. 함수가 이에 대비할 수 있도록 설계해야 합니다.

이러한 항목 중 5,000개를 처리를 위해 큐에 삽입한 후 오류가 발생한다면 코드는 어떻게 반응할까요? 사용자가 완료한 집합에서 항목을 추적합니다. 다른 방법으로 다음 번에 해당 항목을 삽입할 수도 있습니다. This double-insertion can have a serious impact on your work flow, so [make your functions idempotent](functions-idempotent.md). 

큐 항목을 이미 처리한 경우 함수는 수행되지 않습니다.

Azure Functions 플랫폼에서 사용하는 구성 요소를 위해 이미 제공된 방어 수단을 활용하세요. 예를 들어 [Azure Storage 큐 트리거 및 바인딩](functions-bindings-storage-queue.md#trigger---poison-messages)을 위한 설명서에서 **포이즌 큐 메시지 처리**를 참조하세요. 

## <a name="scalability-best-practices"></a>확장성 모범 사례

There are a number of factors that impact how instances of your function app scale. 자세한 내용은 [함수 크기 조정](functions-scale.md)의 설명서에서 제공됩니다.  함수 앱에 최적의 확장성을 보장하는 몇 가지 모범 사례는 다음과 같습니다.

### <a name="share-and-manage-connections"></a>연결 공유 및 관리

Reuse connections to external resources whenever possible.  [Azure Functions에서 연결을 관리하는 방법](./manage-connections.md)을 참조하세요.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>동일한 함수 앱에서 테스트와 프로덕션 코드의 혼합 금지

함수 앱 공유 리소스 내에서 작용합니다. 예를 들어 메모리는 공유됩니다. 프로덕션 환경에서 함수 앱을 사용하는 경우 테스트 관련 함수 및 리소스를 추가하지 마세요. 프로덕션 코드를 실행하는 동안 예기치 않은 오버 헤드가 발생할 수 있습니다.

프로덕션 함수 앱에서 로드하는 것에 주의하세요. 메모리는 앱에서 각 함수가 사용하는 것의 평균으로 계산됩니다.

If you have a shared assembly referenced in multiple .NET functions, put it in a common shared folder. Otherwise, you could accidentally deploy multiple versions of the same binary that behave differently between functions.

Don't use verbose logging in production code, which has a negative performance impact.

### <a name="use-async-code-but-avoid-blocking-calls"></a>비동기 코드는 사용, 호출 차단 방지

비동기 프로그래밍은 권장되는 최상의 방법입니다. 그러나 `Task` 인스턴스의 `Result` 속성을 참조하거나 `Wait` 메서드를 호출하는 것은 항상 피하세요. 이 방법은 스레드를 소진시킬 수 있습니다.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>가능하면 항상 일괄 처리로 메시지를 수신합니다.

Event Hub와 같은 일부 트리거는 단일 호출에서 일괄 처리 메시지를 수신할 수 있습니다.  메시지를 일괄 처리하면 성능이 향상됩니다.  [host.json 참조 설명서](functions-host-json.md)에 설명된 대로 `host.json` 파일에서 최대 일괄 처리 크기를 구성할 수 있습니다.

For C# functions, you can change the type to a strongly-typed array.  예를 들어 메서드 서명은 `EventData sensorEvent` 대신 `EventData[] sensorEvent`일 수 있습니다.  For other languages, you'll need to explicitly set the cardinality property in your `function.json` to `many` in order to enable batching [as shown here](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>동시성을 처리하도록 호스트 동작 구성

호스트 런타임 및 트리거 동작의 구성에 함수 앱의 `host.json` 파일을 사용할 수 있습니다.  동작을 일괄 처리하는 것 외에도 여러 트리거에 대한 동시성을 관리할 수 있습니다. 이러한 옵션의 값을 조정하면 호출된 함수의 요구에 맞게 각 인스턴스의 크기를 조정할 수 있습니다.

Settings in the host.json file apply across all functions within the app, within a *single instance* of the function. For example, if you had a function app with two HTTP functions and [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) requests set to 25, a request to either HTTP trigger would count towards the shared 25 concurrent requests.  When that function app is scaled to 10 instances, the two functions effectively allow 250 concurrent requests (10 instances * 25 concurrent requests per instance). 

Other host configuration options are found in the [host.json configuration article](functions-host-json.md).

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)
* [Azure App Service 모범 사례](../app-service/app-service-best-practices.md)
