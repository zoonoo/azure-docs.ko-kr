---
title: "Azure Functions Queue Storage 바인딩"
description: "Azure Queue Storage 트리거를 사용하고 Azure Functions에서 바인딩을 출력하는 방법을 이해합니다."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: glenga
ms.openlocfilehash: 9cf506d571c8d67a1e48ce34860db3dbc3445509
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-queue-storage-bindings"></a>Azure Functions Queue Storage 바인딩

이 문서에서는 Azure Functions에서 Azure Queue Storage 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 큐에 대한 트리거 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="queue-storage-trigger"></a>Queue Storage 트리거

새 항목이 큐에 수신될 때 큐 트리거를 사용하여 함수를 시작합니다. 큐 메시지는 함수에 입력으로 제공됩니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [미리 컴파일된 C#](#trigger---c-example)
* [C# 스크립트](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제에서는 `myqueue-items` 큐를 폴링하고 큐 항목이 처리될 때마다 로그를 쓰는 [미리 컴파일된 C#](functions-dotnet-class-library.md) 코드를 보여줍니다.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 Blob 트리거 바인딩을 보여줍니다. 함수는 `myqueue-items` 큐를 폴링하고 큐 항목이 처리될 때마다 로그를 기록합니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

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

[사용](#trigger---usage) 섹션은 function.json에서 `name` 속성에 의해 명명된 `myQueueItem`을 설명합니다.  [메시지 메타데이터 섹션](#trigger---message-metadata)에서는 표시된 다른 모든 변수를 설명합니다.

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 함수](functions-reference-node.md)에서 Blob 트리거 바인딩을 보여줍니다. 함수는 `myqueue-items` 큐를 폴링하고 큐 항목이 처리될 때마다 로그를 기록합니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[사용](#trigger---usage) 섹션은 function.json에서 `name` 속성에 의해 명명된 `myQueueItem`을 설명합니다.  [메시지 메타데이터 섹션](#trigger---message-metadata)에서는 표시된 다른 모든 변수를 설명합니다.

## <a name="trigger---attributes-for-precompiled-c"></a>트리거 - 미리 컴파일된 C#의 특성
 
[미리 컴파일된 C#](functions-dotnet-class-library.md) 함수의 경우 다음 특성을 사용하여 큐 트리거를 구성합니다.

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)는 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지에 정의되어 있습니다.

  특성의 생성자는 다음 예제와 같이 모니터링할 큐의 이름을 사용합니다.

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  ```

  다음 예와 같이 사용할 저장소 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  ```
 
* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)는 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지에 정의되어 있습니다.

  사용할 저장소 계정을 지정하는 다른 방법을 제공합니다. 생성자는 저장소 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

사용할 저장소 계정은 다음과 같은 순서로 결정됩니다.

* `QueueTrigger` 특성의 `Connection` 속성
* `QueueTrigger` 특성과 동일한 매개 변수에 적용된 `StorageAccount` 특성
* 함수에 적용된 `StorageAccount` 특성
* 클래스에 적용된 `StorageAccount` 특성
* "AzureWebJobsStorage" 앱 설정

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `QueueTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음| `queueTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction**| 해당 없음 | *function.json* 파일에서만 적용됩니다. `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 |함수 코드에서 큐를 나타내는 변수의 이름입니다.  | 
|**queueName** | **QueueName**| 폴링할 큐의 이름입니다. | 
|**연결** | **연결** |이 바인딩에 사용할 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 저장소 연결 문자열을 사용합니다.<br/>로컬로 개발하는 경우 앱 설정은 [local.settings.json 파일](functions-run-local.md#local-settings-file) 값으로 이동합니다.|

## <a name="trigger---usage"></a>트리거 - 사용
 
C# 및 C# 스크립트에서는 `Stream paramName`과 같은 메서드 매개 변수를 사용하여 Blob 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 다음 중 원하는 형식으로 바인딩할 수 있습니다.

* POCO 개체 - Functions 런타임은 JSON 페이로드를 POCO 개체로 deserialize합니다. 
* `string`
* `byte[]`
* [CloudQueueMessage]

JavaScript에서 `context.bindings.<name>`을 사용하여 큐 항목 페이로드에 액세스합니다. 페이로드가 JSON인 경우 개체로 deserialize됩니다.

## <a name="trigger---message-metadata"></a>트리거 - 메시지 메타데이터

큐 트리거는 몇 가지 메타데이터 속성을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 값은 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage)와 동일한 의미 체계를 가집니다.

|속성|형식|설명|
|--------|----|-----------|
|`QueueTrigger`|`string`|큐 페이로드(유효한 문자열인 경우) 큐 메시지 페이로드를 문자열로 사용하는 경우 `QueueTrigger`는 *function.json*에서 `name` 속성에 의해 명명된 변수와 동일한 값을 가집니다.|
|`DequeueCount`|`int`|이 메시지가 큐에서 제거된 횟수입니다.|
|`ExpirationTime`|`DateTimeOffset?`|메시지가 만료되는 시간입니다.|
|`Id`|`string`|큐 메시지 ID입니다.|
|`InsertionTime`|`DateTimeOffset?`|메시지가 큐에 추가된 시간입니다.|
|`NextVisibleTime`|`DateTimeOffset?`|다음에 메시지가 표시되는 시간입니다.|
|`PopReceipt`|`string`|메시지의 PopReceipt입니다.|

## <a name="trigger---poison-messages"></a>트리거 - 포이즌 메시지

큐 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 큐 메시지에 대해 최대 5번(첫 번째 시도 포함) 다시 시도합니다. 5번 모두 실패할 경우 Functions 런타임은 *&lt;originalqueuename>-poison*이라는 큐에 메시지를 추가합니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다.

포이즌 메시지를 수동으로 처리하려면 큐 메시지의 [dequeueCount](#trigger---message-metadata)를 확인합니다.

## <a name="trigger---hostjson-properties"></a>트리거 - host.json 속성

[host.json](functions-host-json.md#queues) 파일에는 큐 트리거 동작을 제어하는 설정이 포함됩니다.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="queue-storage-output-binding"></a>Queue Storage 출력 바인딩

Azure Queue Storage 출력 바인딩을 사용하여 메시지를 큐에 씁니다.

## <a name="output---example"></a>출력 - 예제

언어 관련 예제를 참조하세요.

* [미리 컴파일된 C#](#output---c-example)
* [C# 스크립트](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>출력 - C# 예제

다음 예제에서는 수신된 각 HTTP 요청에 대한 큐 메시지를 만드는 [미리 컴파일된 C#](functions-dotnet-class-library.md) 코드를 보여줍니다.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>출력 - C# 스크립트 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 Blob 트리거 바인딩을 보여줍니다. 이 함수는 수신한 각 HTTP 요청에 대한 POCO 페이로드를 사용하여 큐 항목을 만듭니다.

*function.json* 파일은 다음과 같습니다.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

단일 큐 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
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

`ICollector` 또는 `IAsyncCollector` 매개 변수를 사용하여 한 번에 여러 메시지를 보낼 수 있습니다. 여러 개의 메시지를 보내는 C# 스크립트 코드, HTTP 요청 데이터를 포함하는 코드 및 하드코딩된 값을 포함하는 코드는 다음과 같습니다.

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItem, 
    TraceWriter log)
{
    myQueueItem.Add(input);
    myQueueItem.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>출력 - JavaScript 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 함수](functions-reference-node.md)에서 Blob 트리거 바인딩을 보여줍니다. 이 함수는 수신한 각 HTTP 요청에 대한 큐 항목을 만듭니다.

*function.json* 파일은 다음과 같습니다.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[구성](#output---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

`myQueueItem` 출력 바인딩에 대한 메시지 배열을 정의하여 한 번에 여러 메시지를 보낼 수 있습니다. 다음 JavaScript 코드는 수신된 각 HTTP 요청에 하드코딩된 값이 포함된 두 개의 큐 메시지를 보냅니다.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes-for-precompiled-c"></a>출력 - 미리 컴파일된 C#의 특성
 
[미리 컴파일된 C#](functions-dotnet-class-library.md) 함수의 경우 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)는 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지에 정의되어 있습니다.

특성은 함수의 `out` 매개 변수 또는 반환 값에 적용됩니다. 특성의 생성자는 다음 예제와 같이 큐의 이름을 사용합니다.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
```

다음 예와 같이 사용할 저장소 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items, Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
```

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 저장소 계정을 지정합니다. 자세한 내용은 [트리거 - 미리 컴파일된 C#의 특성](#trigger---attributes-for-precompiled-c)을 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `Queue` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | `queue`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `out`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐를 나타내는 변수의 이름입니다. `$return`으로 설정하여 함수 반환 값을 참조합니다.| 
|**queueName** |**QueueName** | 큐의 이름입니다. | 
|**연결** | **연결** |이 바인딩에 사용할 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 저장소 연결 문자열을 사용합니다.<br>로컬로 개발하는 경우 앱 설정은 [local.settings.json 파일](functions-run-local.md#local-settings-file) 값으로 이동합니다.|

## <a name="output---usage"></a>출력 - 사용
 
C# 및 C# 스크립트에서 `out T paramName`과 같은 메서드 매개 변수를 사용하여 단일 큐 메시지를 씁니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. `out` 매개 변수 대신 메서드 반환 형식을 사용할 수 있습니다. `T`는 다음 형식 중 하나일 수 있습니다.

* JSON으로 직렬화 가능한 POCO
* `string`
* `byte[]`
* [CloudQueueMessage] 

C# 및 C# 스크립트에서 다음 형식 중 하나를 사용하여 여러 큐 메시지를 씁니다. 

* `ICollector<T>` 또는 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

JavaScript 함수에서 `context.bindings.<name>`을 사용하여 출력 큐 메시지에 액세스합니다. 큐 항목 페이로드에 문자열 또는 JSON 직렬화 가능 개체를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Queue Storage 트리거를 사용하는 빠른 시작으로 이동](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Queue Storage 출력 바인딩을 사용하는 자습서로 이동](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage