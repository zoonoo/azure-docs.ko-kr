---
title: 지속성 함수의 함수 체이닝 - Azure
description: 일련의 함수를 실행하는 지속성 함수 샘플을 실행하는 방법에 대해 알아봅니다.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 0020f19e00f3365c4a0d80ebb67aeeedd7fe76df
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>지속성 함수의 함수 체이닝 - Hello 시퀀스 샘플

함수 체이닝은 특정 순서로 일련의 함수를 실행하는 패턴을 나타냅니다. 종종 한 함수의 출력을 다른 함수의 입력에 적용해야 합니다. 이 문서에서는 [지속성 함수](durable-functions-overview.md)를 사용하여 함수 체이닝을 구현하는 샘플에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

* [지속성 함수를 설치합니다](durable-functions-install.md).

## <a name="the-functions"></a>함수

이 문서에서는 샘플 앱의 다음 함수에 대해 설명합니다.

* `E1_HelloSequence`: 시퀀스에서 `E1_SayHello`를 여러 번 호출하는 오케스트레이터 함수입니다. `E1_SayHello` 호출의 출력을 저장하고 결과를 기록합니다.
* `E1_SayHello`: 문자열 앞에 "Hello"가 추가되는 작업 함수입니다.

다음 섹션에서는 C# 스크립팅에 사용되는 구성 및 코드에 대해 설명합니다. Visual Studio 개발을 위한 코드는 이 문서의 끝 부분에 나와 있습니다.
 
## <a name="functionjson-file"></a>function.json 파일

Visual Studio Code 또는 Azure Portal을 사용하여 개발하는 경우 오케스트레이터 함수에 대한 *function.json* 파일의 내용이 여기에 있습니다. 대부분의 오케스트레이터 *function.json* 파일은 거의 이와 비슷합니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

중요한 것은 `orchestrationTrigger` 바인딩 형식입니다. 오케스트레이터 함수는 모두 이 트리거 형식을 사용해야 합니다.

> [!WARNING]
> 오케스트레이터 함수의 "I/O 없음" 규칙을 준수하려면 `orchestrationTrigger` 트리거 바인딩을 사용할 때 입력 또는 출력 바인딩을 사용하지 마세요.  다른 입력 또는 출력 바인딩이 필요하면 오케스트레이터에서 호출하는 `activityTrigger` 함수의 컨텍스트에서 대신 사용해야 합니다.

## <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>C# 스크립트(Visual Studio Code 및 Azure Portal 샘플 코드) 

소스 코드는 다음과 같습니다.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

모든 C# 오케스트레이션 함수에는 `Microsoft.Azure.WebJobs.Extensions.DurableTask` 어셈블리에 있는 `DurableOrchestrationContext` 유형 매개 변수가 있어야 합니다. C# 스크립트를 사용하는 경우 `#r` 표기법을 사용하여 어셈블리를 참조할 수 있습니다. 이 컨텍스트 개체를 사용하면 다른 *작업* 함수를 호출하고 [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) 메서드를 사용하여 입력 매개 변수를 전달할 수 있습니다.

코드에서는 여러 매개 변수 값을 사용하여 `E1_SayHello`을 순서대로 세 번 호출합니다. 각 호출의 반환 값은 함수의 끝에서 반환되는 `outputs` 목록에 추가됩니다.

`E1_SayHello` 작업 함수에 대한 *function.json* 파일은 `orchestrationTrigger` 바인딩 형식 대신 `activityTrigger` 바인딩 형식을 사용한다는 점을 제외하고는 `E1_HelloSequence`의 것과 비슷합니다.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> 오케스트레이션 함수에서 호출하는 함수는 모두 `activityTrigger` 바인딩을 사용해야 합니다.

`E1_SayHello`의 구현은 비교적 간단한 문자열 형식 지정 작업입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

이 함수는 [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) 매개 변수 유형을 갖습니다. 이 매개 변수는 오케스트레이터 함수의 [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_) 호출로 전달된 입력을 가져오는 데 사용됩니다.

## <a name="run-the-sample"></a>샘플 실행

`E1_HelloSequence` 오케스트레이션을 실행하려면 다음 HTTP POST 요청을 전송합니다.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

예를 들어 "myfunctionapp"이라는 함수 앱에서 샘플을 실행하는 경우 "{host}"를 "myfunctionapp.azurewebsites.net"으로 바꿉니다.

결과는 다음과 같은 HTTP 202 응답입니다(간결하게 정리되었음).

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

이 시점에서 오케스트레이션은 큐에서 대기한 다음 즉시 실행되기 시작합니다. `Location` 헤더의 URL을 사용하여 실행 상태를 확인할 수 있습니다.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
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
> 오케스트레이터 함수를 시작한 HTTP POST 끝점은 샘플 앱에서 "HttpStart"라는 HTTP 트리거 함수로 구현됩니다. `queueTrigger`, `eventHubTrigger` 또는 `timerTrigger`와 같은 다른 트리거 형식에 대해 비슷한 시작 논리를 구현할 수 있습니다.

함수 실행 로그를 검토합니다. `E1_HelloSequence` 함수는 [개요](durable-functions-overview.md)에서 설명한 재생 동작으로 인해 여러 번 시작되고 완료되었습니다. 반면에 이러한 함수 실행이 재생되지 않으므로 `E1_SayHello`는 3회만 실행되었습니다.

## <a name="visual-studio-sample-code"></a>Visual Studio 샘플 코드

다음은 Visual Studio 프로젝트의 단일 C# 파일로서의 오케스트레이션입니다.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>다음 단계

이 샘플은 간단한 함수 체인 오케스트레이션을 보여 주었습니다. 다음 예제는 팬아웃/팬인 패턴의 구현 방법을 보여 줍니다. 

> [!div class="nextstepaction"]
> [팬아웃/팬인 샘플 실행](durable-functions-cloud-backup.md)
