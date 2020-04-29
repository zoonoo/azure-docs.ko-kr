---
title: 지속성 함수의 함수 체이닝 - Azure
description: 일련의 함수를 실행하는 지속성 함수 샘플을 실행하는 방법에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: 8da4ce7801cc98f9ffb32eb7b506eaf1ccd877dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77562067"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>지속성 함수의 함수 체이닝 - Hello 시퀀스 샘플

함수 체이닝은 특정 순서로 일련의 함수를 실행하는 패턴을 나타냅니다. 종종 한 함수의 출력을 다른 함수의 입력에 적용해야 합니다. 이 문서에서는 Durable Functions 빠른 시작([C#](durable-functions-create-first-csharp.md) 또는 [JavaScript](quickstart-js-vscode.md))를 완료할 때 생성되는 체이닝 시퀀스에 대해 설명합니다. Durable Functions에 대한 자세한 내용은 [Durable Functions 개요](durable-functions-overview.md)를 참조하세요.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* `E1_HelloSequence`: 시퀀스 [orchestrator function](durable-functions-bindings.md#orchestration-trigger) 에서 여러 번 호출 `E1_SayHello` 하는 오 케 스트레이 터 함수입니다. `E1_SayHello` 호출의 출력을 저장하고 결과를 기록합니다.
* `E1_SayHello`: 문자열 앞에 "Hello"를 사용 하는 [작업 함수](durable-functions-bindings.md#activity-trigger) 입니다.
* `HttpStart`: Orchestrator의 인스턴스를 시작 하는 HTTP 트리거 함수입니다.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence orchestrator 함수

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

모든 C# 오케스트레이션 함수에는 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 어셈블리에 있는 `DurableOrchestrationContext` 유형 매개 변수가 있어야 합니다. 이 컨텍스트 개체를 사용 하면 다른 *작업* 함수를 호출 하 고 해당 `CallActivityAsync` 메서드를 사용 하 여 입력 매개 변수를 전달할 수 있습니다.

코드에서는 여러 매개 변수 값을 사용하여 `E1_SayHello`을 순서대로 세 번 호출합니다. 각 호출의 반환 값은 함수의 끝에서 반환되는 `outputs` 목록에 추가됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> JavaScript Durable Functions는 함수 2.0 런타임에만 사용할 수 있습니다.

#### <a name="functionjson"></a>function.json

Visual Studio Code 또는 Azure Portal을 사용하여 개발하는 경우 오케스트레이터 함수에 대한 *function.json* 파일의 내용이 여기에 있습니다. 대부분의 오케스트레이터 *function.json* 파일은 거의 이와 비슷합니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

중요한 것은 `orchestrationTrigger` 바인딩 형식입니다. 오케스트레이터 함수는 모두 이 트리거 형식을 사용해야 합니다.

> [!WARNING]
> 오케스트레이터 함수의 "I/O 없음" 규칙을 준수하려면 `orchestrationTrigger` 트리거 바인딩을 사용할 때 입력 또는 출력 바인딩을 사용하지 마세요.  다른 입력 또는 출력 바인딩이 필요하면 오케스트레이터에서 호출하는 `activityTrigger` 함수의 컨텍스트에서 대신 사용해야 합니다. 자세한 내용은 [orchestrator 함수 코드 제약 조건](durable-functions-code-constraints.md) 문서를 참조 하세요.

#### <a name="indexjs"></a>index.js

함수는 다음과 같습니다.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

모든 JavaScript 오케스트레이션 함수는 [ `durable-functions` 모듈](https://www.npmjs.com/package/durable-functions)을 포함 해야 합니다. JavaScript에 Durable Functions를 쓸 수 있도록 하는 라이브러리입니다. 오케스트레이션 함수 및 다른 JavaScript 함수 사이에는 다음과 같은 세 가지 중요한 차이점이 있습니다.

1. 함수는 [생성기 함수입니다.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. 함수가 `durable-functions` 모듈의 `orchestrator` 호출에 래핑됩니다(여기서는 `df`).
3. 동기 함수여야 합니다. '오케스트레이터' 메서드가 'context.done' 호출을 처리하므로 함수는 단순히 '반환'만 하면 됩니다.

개체 `context` 에는 다른 `df` *작업* 함수를 호출 하 고 해당 `callActivity` 메서드를 사용 하 여 입력 매개 변수를 전달할 수 있는 지 속성 오케스트레이션 컨텍스트 개체가 포함 되어 있습니다. 코드는 다른 매개 변수 값을 사용하여 `E1_SayHello`를 순차적으로 3번 호출하고, `yield`를 사용하여 비동기 작업 함수 호출이 반환될 때까지 실행을 대기해야 함을 나타냅니다. 각 호출의 반환 값은 함수 끝에 반환 `outputs` 되는 배열에 추가 됩니다.

---

### <a name="e1_sayhello-activity-function"></a>E1_SayHello activity 함수

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

작업은 특성 `ActivityTrigger` 을 사용 합니다. 제공 `IDurableActivityContext` 된를 사용 하 여를 사용 하 여 `GetInput<T>`입력 값에 액세스 하는 등의 작업 관련 작업을 수행 합니다.

`E1_SayHello`의 구현은 비교적 간단한 문자열 형식 지정 작업입니다.

에 `IDurableActivityContext`바인딩하는 대신 작업 함수로 전달 되는 형식에 직접 바인딩할 수 있습니다. 다음은 그 예입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/i s json

`E1_SayHello` 작업 함수에 대한 *function.json* 파일은 `orchestrationTrigger` 바인딩 형식 대신 `activityTrigger` 바인딩 형식을 사용한다는 점을 제외하고는 `E1_HelloSequence`의 것과 비슷합니다.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> 오케스트레이션 함수에서 호출하는 함수는 모두 `activityTrigger` 바인딩을 사용해야 합니다.

`E1_SayHello`의 구현은 비교적 간단한 문자열 형식 지정 작업입니다.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/svjs

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

JavaScript 오케스트레이션 함수와 달리, 작업 함수는 특별한 설정이 필요 없습니다. 오케스트레이터 함수에 의해 전달되는 입력은 `activityTrigger` 바인딩 이름(이 경우 `context.bindings.name`) 아래의 `context.bindings` 개체에 있습니다. 바인딩 이름은 내보낸 함수의 매개 변수로 설정되고 직접 액세스될 수 있습니다. 샘플 코드는 이 작업을 수행합니다.

---

### <a name="httpstart-client-function"></a>HttpStart 클라이언트 함수

클라이언트 함수를 사용 하 여 orchestrator 함수 인스턴스를 시작할 수 있습니다. `HttpStart` HTTP 트리거 함수를 사용 하 여 인스턴스를 시작 `E1_HelloSequence`합니다.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Orchestrator와 상호 작용 하려면 함수에 `DurableClient` 입력 바인딩이 포함 되어야 합니다. 클라이언트를 사용 하 여 오케스트레이션을 시작 합니다. 또한 새 오케스트레이션의 상태를 확인 하는 Url을 포함 하는 HTTP 응답을 반환 하는 데 도움이 될 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/함수. json

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Orchestrator와 상호 작용 하려면 함수에 `durableClient` 입력 바인딩이 포함 되어야 합니다.

#### <a name="httpstartindexjs"></a>HttpStart/node.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

를 `df.getClient` 사용 하 여 `DurableOrchestrationClient` 개체를 가져옵니다. 클라이언트를 사용 하 여 오케스트레이션을 시작 합니다. 또한 새 오케스트레이션의 상태를 확인 하는 Url을 포함 하는 HTTP 응답을 반환 하는 데 도움이 될 수 있습니다.

---

## <a name="run-the-sample"></a>샘플 실행

`E1_HelloSequence` 오케스트레이션을 실행 하려면 다음 HTTP POST 요청을 `HttpStart` 함수로 보냅니다.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> 이전 HTTP 코드 조각에서는 모든 HTTP 트리거 함수 URL에서 기본 `api/` 접두사를 제거하는 항목이 `host.json` 파일에 있다고 가정합니다. 샘플의 `host.json` 파일에서 이 구성에 대한 변경 내용을 찾을 수 있습니다.

예를 들어 "myfunctionapp"이라는 함수 앱에서 샘플을 실행하는 경우 "{host}"를 "myfunctionapp.azurewebsites.net"으로 바꿉니다.

결과는 다음과 같은 HTTP 202 응답입니다(간결하게 정리되었음).

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

이 시점에서 오케스트레이션은 큐에서 대기한 다음 즉시 실행되기 시작합니다. `Location` 헤더의 URL을 사용하여 실행 상태를 확인할 수 있습니다.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

결과는 오케스트레이션의 상태입니다. 빠르게 실행되고 완료되므로 다음과 같은 응답과 함께 *완료됨* 상태로 표시됩니다(간결하게 정리되었음).

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

여기서 볼 수 있듯이 인스턴스의 `runtimeStatus`는 *완료됨*이고 `output`에는 JSON 직렬화된 오케스트레이터 함수 실행 결과가 포함됩니다.

> [!NOTE]
> `queueTrigger`, `eventHubTrigger` 또는 `timerTrigger`와 같은 다른 트리거 형식에 대해 비슷한 시작 논리를 구현할 수 있습니다.

함수 실행 로그를 검토합니다. 함수 `E1_HelloSequence` 는 [오케스트레이션 안정성](durable-functions-orchestrations.md#reliability) 항목에 설명 된 재생 동작으로 인해 여러 번 시작 되 고 완료 됩니다. 반면에 이러한 함수 실행이 재생되지 않으므로 `E1_SayHello`는 3회만 실행되었습니다.

## <a name="next-steps"></a>다음 단계

이 샘플은 간단한 함수 체인 오케스트레이션을 보여 주었습니다. 다음 예제는 팬아웃/팬인 패턴의 구현 방법을 보여줍니다.

> [!div class="nextstepaction"]
> [팬아웃/팬인 샘플 실행](durable-functions-cloud-backup.md)
