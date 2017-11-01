---
title: "지속성 함수의 상태 저장 단일 항목 - Azure"
description: "Azure Functions의 지속성 함수 확장에서 상태 저장 단일 항목을 구현하는 방법을 알아봅니다."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ec7d51d3f30eb3417a48fbf8d31a9b8359e39ab9
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2017
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>지속성 함수의 상태 저장 단일 항목 - Counter 샘플

상태 저장 단일 항목은 상태를 저장하고 다른 함수로 호출되고 쿼리될 수 있는 장기 실행(잠재적으로 영구) 오케스트레이터 함수입니다. 상태 저장 단일 항목은 분산 컴퓨팅의 [행위자 모델](https://en.wikipedia.org/wiki/Actor_model)과 비슷합니다.

적절한 "행위자" 구현은 아니지만 오케스트레이터 함수에는 동일한 런타임 특징이 많이 있습니다. 예를 들어 상태 저장, 신뢰할 수 있는 단일 스레드, 위치 투명성 및 전역 주소 지정 등이 있습니다. 이러한 특징은 실제 행위자 구현을 특히 유용하게 만들지만 별도의 프레임워크가 필요 없는 특징입니다.

이 문서에서는 *카운터* 샘플을 실행하는 방법을 보여 줍니다. 이 샘플은 *증분* 및 *감소* 작업을 지원하고, 이에 따라 내부 상태를 업데이트하는 단일 항목 개체를 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* [지속성 함수 설치](durable-functions-install.md)의 지침에 따라 샘플을 설정합니다.
* 이 문서에서는 [Hello 시퀀스](durable-functions-sequence.md) 샘플 연습을 이미 살펴보았다고 가정합니다.

## <a name="scenario-overview"></a>시나리오 개요

카운터 시나리오는 일반적인 상태 비저장 함수를 사용하여 구현하기가 매우 어렵습니다. 주요 과제 중 하나는 **동시성**을 관리하는 것입니다. *증분* 및 *감소*와 같은 작업에는 원자성이 있어야 하거나, 그렇지 않으면 작업이 서로 덮어쓰는 경합 상태가 있을 수 있습니다.

단일 VM을 사용하여 카운터 데이터를 호스팅하는 것도 하나의 옵션이지만, 비용이 많이 들고 단일 VM을 정기적으로 다시 부팅할 수 있으므로 **안정성** 관리가 어려울 수 있습니다. 다른 방법으로 Blob 임대와 같은 동기화 도구와 함께 분산된 플랫폼을 사용하여 동시성을 관리할 수 있지만, 이 경우 많은 양의 **복잡성**을 초래합니다.

오케스트레이션 인스턴스가 단일 VM에 적합하고 오케스트레이터 함수 실행이 항상 단일 스레드이므로 지속성 함수에서 이러한 유형의 시나리오를 구현하기가 쉽습니다. 또한 장기 실행되고, 상태를 저장하며, 외부 이벤트에 대응할 수 있습니다. 아래 샘플 코드에서는 장기 실행 오케스트레이터 함수와 같은 카운터를 구현하는 방법을 보여 줍니다.

## <a name="the-sample-function"></a>샘플 함수

이 문서에서는 샘플 앱의 **E3_Counter** 함수를 단계별로 살펴봅니다.

다음 섹션에서는 Visual Studio 개발에 사용되는 코드에 대해 설명합니다. Azure Portal 개발 코드와 비슷합니다.

## <a name="the-counter-orchestration"></a>카운터 오케스트레이션

다음은 오케스트레이터 함수를 구현하는 코드입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)]

이 오케스트레이터 함수는 기본적으로 다음을 수행합니다.

1. [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_)를 사용하여 *operation*이라는 외부 이벤트를 수신 대기합니다.
2. 요청한 작업에 따라 `counterState` 지역 변수를 늘리거나 줄입니다.
3. [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) 메서드를 사용하여 오케스트레이터를 다시 시작하고 `counterState`의 최신 값을 새 입력으로 설정합니다.
4. 영구적으로 또는 *end* 메시지를 받을 때까지 계속 실행합니다.

이는 잠재적으로 결코 종료되지 않는 *영구 오케스트레이션*에 대한 예제입니다.&mdash; 오케스트레이터가 아닌 함수에서 호출할 수 있는 [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) 메서드로 전송된 메시지에 응답합니다.

이 오케스트레이터 함수의 한 가지 고유한 특징은 기록이 없어 효율적이라는 것입니다. `ContinueAsNew` 메서드에서 처리된 각 이벤트 후에 기록을 다시 설정합니다. 이는 임의의 수명이 있는 오케스트레이터를 구현하는 데 선호되는 방법입니다. `while` 반복을 사용하면 오케스트레이터 함수의 기록이 무한정 증가하여 불필요하게 많은 메모리 사용을 초래할 수 있습니다.

> [!NOTE]
> `ContinueAsNew` 메서드에는 영구 오케스트레이션 이외의 다른 사용 사례가 있습니다. 자세한 내용은 [영구 오케스트레이션](durable-functions-eternal-orchestrations.md)을 참조하세요.

## <a name="run-the-sample"></a>샘플 실행

다음 HTTP POST 요청을 전송하여 오케스트레이션을 시작할 수 있습니다. `counterState`가 0(`int`의 기본값)에서 시작될 수 있도록 이 요청에는 내용이 없습니다.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

**E3_Counter** 인스턴스가 시작되는 즉시 `RaiseEventAsync`를 사용하거나 202 응답에서 참조된 **sendEventUrl** HTTP POST 웹후크를 사용하여 이벤트를 보낼 때까지 기다립니다. 유효한 `eventName` 값에는 *incr*, *decr* 및 *end*가 포함됩니다.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Azure Functions 포털의 함수 로그를 검토하여 "incr" 작업의 결과를 확인할 수 있습니다.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

마찬가지로 오케스트레이터 상태를 확인하는 경우 `input` 필드가 업데이트된 값(1)으로 설정되어 있음을 알 수 있습니다.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

이 인스턴스에 새 작업을 계속 보내고, 이에 따라 상태가 업데이트되는지 관찰할 수 있습니다. 인스턴스를 종료하려면 *end* 작업을 보내면 됩니다.

> [!WARNING]
> 이 문서를 작성하는 시점에서 외부 이벤트 또는 종료 요청과 같은 메시지를 동시에 처리하는 동안 `ContinueAsNew`를 호출할 때 알려진 경합 상태가 있습니다. 이러한 경합 상태에 대한 최신 정보는 이 [GitHub 문제](https://github.com/Azure/azure-functions-durable-extension/issues/67)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 샘플은 [외부 이벤트](durable-functions-external-events.md)를 처리하고 [상태 저장 단일 항목](durable-functions-singletons.md)에서 [내부 오케스트레이션](durable-functions-eternal-orchestrations.md)을 구현합니다. 다음 샘플에서는 외부 이벤트 및 [지속형 타이머](durable-functions-timers.md)를 사용하여 인간 상호 작용을 처리하는 방법을 보여 줍니다.

> [!div class="nextstepaction"]
> [인간 상호 작용 샘플 실행](durable-functions-phone-verification.md)
