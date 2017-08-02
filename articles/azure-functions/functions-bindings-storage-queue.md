---
title: "Azure Functions Queue Storage 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: 4e6a837d-e64f-45a0-87b7-aa02688a75f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: donnam, glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: f39f674bf576a2661a0e03710b9005b0515b3aa5
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions Queue Storage 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure Queue Storage 큐 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 Azure 큐에 대한 트리거 및 출력 바인딩을 지원합니다. 모든 바인딩에서 사용할 수 있는 기능은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="queue-storage-trigger"></a>Queue Storage 트리거
Azure Queue Storage 트리거를 사용하면 새 메시지에 대해 Queue Storage를 모니터링하고 이에 대응할 수 있습니다. 

Functions 포털에서 **통합** 탭을 사용하여 큐 트리거를 정의합니다. 이 포털에서는 *function.json*의 **bindings** 섹션에 다음 정의를 만듭니다.

```json
{
    "type": "queueTrigger",
    "direction": "in",
    "name": "<The name used to identify the trigger data in your code>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>"
}
```

* `connection` 속성은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 선택하는 경우 이 앱 설정을 구성합니다.

host.json 파일에 [추가 설정](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)을 입력하여 Queue Storage 트리거를 더욱 세밀하게 조정할 수 있습니다. 예를 들어 host.json에서 큐 폴링 간격을 변경할 수 있습니다.

<a name="triggerusage"></a>

## <a name="using-a-queue-trigger"></a>큐 트리거 사용
Node.js 함수에서는 `context.bindings.<name>`을 사용하여 큐 데이터에 액세스합니다.


.NET 함수에서는 `CloudQueueMessage paramName`과 같은 메서드 매개 변수를 사용하여 큐 페이로드에 액세스합니다. 여기에서 `paramName`은 [트리거 구성](#trigger)에서 지정한 값입니다. 큐 메시지를 다음 중 원하는 형식으로 역직렬화할 수 있습니다.

* POCO 개체입니다. 큐 페이로드가 JSON 개체인 경우에 사용합니다. Functions 런타임은 페이로드를 POCO 개체로 deserialize합니다. 
* `string`
* `byte[]`
* [`CloudQueueMessage`]

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>큐 트리거 메타데이터
큐 트리거는 몇 가지 메타데이터 속성을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 값은 [`CloudQueueMessage`]와 동일한 의미 체계를 가집니다.

* **QueueTrigger** - 큐 페이로드(유효한 문자열인 경우)
* **DequeueCount** - `int` 형식입니다. 이 메시지가 큐에서 제거된 횟수입니다.
* **ExpirationTime** - `DateTimeOffset?` 형식입니다. 메시지가 만료되는 시간입니다.
* **Id** - `string` 형식입니다. 큐 메시지 ID입니다.
* **InsertionTime** - `DateTimeOffset?` 형식입니다. 메시지가 큐에 추가된 시간입니다.
* **NextVisibleTime** - `DateTimeOffset?` 형식입니다. 다음에 메시지가 표시되는 시간입니다.
* **PopReceipt** - `string` 형식입니다. 메시지의 PopReceipt입니다.

[트리거 샘플](#triggersample)에서 큐 메타데이터를 사용하는 방법을 참조하세요.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>트리거 샘플
큐 트리거를 정의하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionString"
        }
    ]
}
```

큐 메타데이터를 검색하고 기록하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C#에서 트리거 샘플 #
```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger sample in F# ## 
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js에서 트리거 샘플

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id=', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

### <a name="handling-poison-queue-messages"></a>포이즌 큐 메시지 처리
큐 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 큐 메시지에 대해 최대 5번(첫 번째 시도 포함) 다시 시도합니다. 5번 모두 실패할 경우 Functions 런타임은 *&lt;originalqueuename>-poison*이라는 Queue Storage에 메시지를 추가합니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다. 

포이즌 메시지를 수동으로 처리하려면 큐 메시지의 `dequeueCount`를 확인합니다([큐 트리거 메타데이터](#meta) 참조).

<a name="output"></a>

## <a name="queue-storage-output-binding"></a>Queue Storage 출력 바인딩
Azure Queue Storage 출력 바인딩을 사용하면 메시지를 큐에 쓸 수 있습니다. 

Functions 포털에서 **통합** 탭을 사용하여 큐 출력 바인딩을 정의합니다. 이 포털에서는 *function.json*의 **bindings** 섹션에 다음 정의를 만듭니다.

```json
{
   "type": "queue",
   "direction": "out",
   "name": "<The name used to identify the trigger data in your code>",
   "queueName": "<Name of queue to write to>",
   "connection":"<Name of app setting - see below>"
}
```

* `connection` 속성은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 선택하는 경우 이 앱 설정을 구성합니다.

<a name="outputusage"></a>

## <a name="using-a-queue-output-binding"></a>큐 출력 바인딩 사용
Node.js 함수에서는 `context.bindings.<name>`을 사용하여 출력 큐에 액세스합니다.

.NET 함수에서는 다음 중 원하는 형식으로 출력할 수 있습니다. 형식 매개 변수 `T`가 있는 경우 `T`는 지원되는 출력 형식(예: `string` 또는 POCO) 중 하나여야 합니다

* `out T`(JSON으로 serialize됨)
* `out string`
* `out byte[]`
* `out` [`CloudQueueMessage`] 
* `ICollector<T>`
* `IAsyncCollector<T>`
* [`CloudQueue`](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

또한 메서드 반환 형식을 출력 바인딩으로 사용할 수 있습니다.

<a name="outputsample"></a>

## <a name="queue-output-sample"></a>큐 출력 샘플
다음 *function.json*은 큐 출력 바인딩을 사용하여 HTTP 트리거를 정의합니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionString",
    }
  ]
}
``` 

들어오는 HTTP 페이로드가 포함된 큐 메시지를 출력하는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="queue-output-sample-in-c"></a>C#의 큐 출력 샘플 #

```cs
// C# example of HTTP trigger binding to a custom POCO, with a queue output binding
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

여러 메시지를 보내려면 `ICollector`를 사용합니다.

```cs
public static void Run(CustomQueueMessage input, ICollector<CustomQueueMessage> myQueueItem, TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

<a name="outnodejs"></a>

### <a name="queue-output-sample-in-nodejs"></a>Node.js의 큐 출력 샘플

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

또는 여러 메시지를 전송하려면 다음을 수행합니다.

```javascript
module.exports = function(context) {
    // Define a message array for the myQueueItem output binding. 
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="next-steps"></a>다음 단계

Queue Storage 트리거 및 바인딩을 사용하는 함수의 예제는 [Azure 서비스에 연결된 Azure Function 만들기](functions-create-an-azure-connected-function.md)를 참조하세요.

[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

<!-- LINKS -->

[`CloudQueueMessage`]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage

