---
title: Azure Functions의 Azure Queue Storage 바인딩
description: Azure Queue Storage 트리거를 사용하고 Azure Functions에서 바인딩을 출력하는 방법을 이해합니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 72460136f5fa0dcfec78716fc02e0aaf9e860840
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472286"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure Functions의 Azure Queue Storage 바인딩

이 문서에서는 Azure Functions에서 Azure Queue Storage 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 큐에 대한 트리거 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Queue Storage 바인딩은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

Queue Storage 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
함수에 *base64*로 인코딩된 문자열이 필요합니다. 인코딩 형식에 대한 조정(데이터를 *base64*로 인코딩된 문자열로 준비하기 위해)은 호출 서비스에 구현되어야 합니다.

## <a name="trigger"></a>트리거

새 항목이 큐에 수신될 때 큐 트리거를 사용하여 함수를 시작합니다. 큐 메시지는 함수에 입력으로 제공됩니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제에서는 `myqueue-items` 큐를 폴링하고 큐 항목이 처리될 때마다 로그를 쓰는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트(.csx)](functions-reference-csharp.md) 코드에서 큐 트리거 바인딩을 보여 줍니다. 함수는 `myqueue-items` 큐를 폴링하고 큐 항목이 처리될 때마다 로그를 기록합니다.

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

using Microsoft.Extensions.Logging;
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
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
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

다음 예제는 *function.json* 파일의 큐 트리거 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 함수는 `myqueue-items` 큐를 폴링하고 큐 항목이 처리될 때마다 로그를 기록합니다.

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

> [!NOTE]
> name 매개 변수는 JavaScript에서 큐 항목 페이로드를 포함하는 `context.bindings.<name>`을 반영합니다. 이 페이로드는 함수에도 두 번째 매개 변수로 전달됩니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
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

### <a name="trigger---java-example"></a>트리거 - Java 예제

다음 Java 예제에서는 큐 `myqueuename`에 배치된 트리거된 메시지를 기록하는 스토리지 큐 트리거 함수를 보여줍니다.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

## <a name="trigger---attributes"></a>트리거 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서는 다음 특성을 사용하여 큐 트리거를 구성합니다.

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  특성의 생성자는 다음 예제와 같이 모니터링할 큐의 이름을 사용합니다.

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  다음 예와 같이 사용할 저장소 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  전체 예제는 [트리거 - C# 예제](#trigger---c-example)를 참조하세요.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  사용할 저장소 계정을 지정하는 다른 방법을 제공합니다. 생성자는 저장소 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
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
|**name** | 해당 없음 |함수 코드에서 큐 항목 페이로드를 포함하는 변수 이름입니다.  |
|**queueName** | **QueueName**| 폴링할 큐의 이름입니다. |
|**연결** | **연결** |이 바인딩에 사용할 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 저장소 연결 문자열을 사용합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>트리거 - 사용

C# 및 C# 스크립트에서는 `string paramName`과 같은 메서드 매개 변수를 사용하여 메시지 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 다음 중 원하는 형식으로 바인딩할 수 있습니다.

* 개체 - Functions 런타임은 JSON 페이로드를 코드에 정의된 임의 클래스 인스턴스로 역직렬화합니다. 
* `string`
* `byte[]`
* [CloudQueueMessage]

`CloudQueueMessage`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

JavaScript에서 `context.bindings.<name>`을 사용하여 큐 항목 페이로드에 액세스합니다. 페이로드가 JSON인 경우 개체로 deserialize됩니다.

## <a name="trigger---message-metadata"></a>트리거 - 메시지 메타데이터

큐 트리거는 몇 가지 [메타데이터 속성](./functions-bindings-expressions-patterns.md#trigger-metadata)을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) 클래스의 속성은 다음과 같습니다.

|자산|Type|설명|
|--------|----|-----------|
|`QueueTrigger`|`string`|큐 페이로드(유효한 문자열인 경우) 큐 메시지 페이로드를 문자열로 사용하는 경우 `QueueTrigger`는 *function.json*에서 `name` 속성에 의해 명명된 변수와 동일한 값을 가집니다.|
|`DequeueCount`|`int`|이 메시지가 큐에서 제거된 횟수입니다.|
|`ExpirationTime`|`DateTimeOffset`|메시지가 만료되는 시간입니다.|
|`Id`|`string`|큐 메시지 ID입니다.|
|`InsertionTime`|`DateTimeOffset`|메시지가 큐에 추가된 시간입니다.|
|`NextVisibleTime`|`DateTimeOffset`|다음에 메시지가 표시되는 시간입니다.|
|`PopReceipt`|`string`|메시지의 PopReceipt입니다.|

## <a name="trigger---poison-messages"></a>트리거 - 포이즌 메시지

큐 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 큐 메시지에 대해 최대 5번(첫 번째 시도 포함) 다시 시도합니다. 5번 모두 실패할 경우 Functions 런타임은 *&lt;originalqueuename>-poison*이라는 큐에 메시지를 추가합니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다.

포이즌 메시지를 수동으로 처리하려면 큐 메시지의 [dequeueCount](#trigger---message-metadata)를 확인합니다.

## <a name="trigger---polling-algorithm"></a>트리거 - 폴링 알고리즘

큐 트리거는 무작위 지수 백오프 알고리즘을 구현하여 유휴 큐 폴링이 저장소 트랜잭션 비용에 미치는 영향을 줄입니다.  메시지가 발견되면 런타임은 2초 대기하고 다른 메시지가 있는지 확인하며, 메시지가 발견되지 않으면 4초 정도 대기하고 나서 다시 시도합니다. 후속 시도로 큐 메시지를 가져오지 못하면 최대 대기 시간(기본값 1분)에 도달할 때까지 대기 시간이 계속 증가합니다. 최대 대기 시간은 [host.json 파일](functions-host-json.md#queues)의 `maxPollingInterval` 속성을 통해 구성할 수 있습니다.

## <a name="trigger---concurrency"></a>트리거 - 동시성

대기 중인 큐 메시지가 여러 개 있을 때, 큐 트리거는 일괄 처리 메시지를 검색하고 동시에 함수 인스턴스를 호출하여 처리합니다. 기본적으로 일괄 처리 크기는 16입니다. 처리되는 개수가 8로 감소하면 런타임은 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 VM(가상 머신) 1개에서 함수당 처리되는 최대 동시 메시지 수는 24입니다. 이 제한은 각 VM의 큐 트리거 함수에 개별적으로 적용됩니다. 함수 앱이 여러 VM으로 스케일 아웃되면 각 VM은 트리거를 기다리고 함수 실행을 시도합니다. 예를 들어, 함수 탭이 3개의 VM으로 스케일 아웃될 경우 큐 트리거 함수 1개에 대한 최대 동시 인스턴스 수의 기본값은 72입니다.

일괄 처리 크기 및 새 일괄 처리를 가져오기 위한 임계값은 [host.json 파일](functions-host-json.md#queues)에서 구성 가능합니다. 함수 앱에서 큐 트리거 함수의 병렬 실행을 최소화하려는 경우 일괄 처리 크기를 1로 설정할 수 있습니다. 이 설정은 함수 앱이 단일 VM(가상 머신)에서 실행되는 동안에만 동시성을 제거합니다. 

큐 트리거는 함수가 큐 메시지를 여러 번 처리하는 것을 방지합니다. 함수를 idempotent로 작성할 필요가 없습니다.

## <a name="trigger---hostjson-properties"></a>트리거 - host.json 속성

[host.json](functions-host-json.md#queues) 파일에는 큐 트리거 동작을 제어하는 설정이 포함됩니다. 참조 된 [host.json 설정](#hostjson-settings) 사용 가능한 설정에 대 한 자세한 내용은 섹션입니다.

## <a name="output"></a>출력

Azure Queue Storage 출력 바인딩을 사용하여 메시지를 큐에 씁니다.

## <a name="output---example"></a>출력 - 예제

언어 관련 예제를 참조하세요.

* [C#](#output---c-example)
* [C# 스크립트(.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>출력 - C# 예제

다음 예제에서는 수신된 각 HTTP 요청에 대한 큐 메시지를 만드는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>출력 - C# 스크립트 예제

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트(.csx)](functions-reference-csharp.md) 코드에서 HTTP 트리거 바인딩을 보여 줍니다. 이 함수는 수신한 각 HTTP 요청에 대한 **CustomQueueMessage** 개체 페이로드를 사용하여 큐 항목을 만듭니다.

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
      "connection": "MyStorageConnectionAppSetting"
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

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

`ICollector` 또는 `IAsyncCollector` 매개 변수를 사용하여 한 번에 여러 메시지를 보낼 수 있습니다. 여러 개의 메시지를 보내는 C# 스크립트 코드, HTTP 요청 데이터를 포함하는 코드 및 하드코딩된 값을 포함하는 코드는 다음과 같습니다.

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>출력 - JavaScript 예제

다음 예제는 *function.json* 파일의 HTTP 트리거 바인딩과 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 이 함수는 수신한 각 HTTP 요청에 대한 큐 항목을 만듭니다.

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
      "connection": "MyStorageConnectionAppSetting"
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

### <a name="output---java-example"></a>출력 - Java 예제

 다음 예제에서는 HTTP 요청에 의해 트리거되는 경우 큐 메시지를 만드는 Java 함수를 보여줍니다.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Queue Storage에 작성되는 매개 변수에 대한 `@QueueOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 POJO의 원시 Java 형식입니다.


## <a name="output---attributes"></a>출력 - 특성

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs)를 사용합니다.

특성은 함수의 `out` 매개 변수 또는 반환 값에 적용됩니다. 특성의 생성자는 다음 예제와 같이 큐의 이름을 사용합니다.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

다음 예와 같이 사용할 저장소 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#output---c-example)를 참조하세요.

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 저장소 계정을 지정합니다. 자세한 내용은 트리거 - 특성을 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `Queue` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | `queue`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `out`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐를 나타내는 변수의 이름입니다. `$return`으로 설정하여 함수 반환 값을 참조합니다.|
|**queueName** |**QueueName** | 큐의 이름입니다. |
|**연결** | **연결** |이 바인딩에 사용할 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 저장소 연결 문자열을 사용합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>출력 - 사용

C# 및 C# 스크립트에서 `out T paramName`과 같은 메서드 매개 변수를 사용하여 단일 큐 메시지를 씁니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. `out` 매개 변수 대신 메서드 반환 형식을 사용할 수 있습니다. `T`는 다음 형식 중 하나일 수 있습니다.

* JSON으로 직렬화 가능한 개체
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

C# 및 C# 스크립트에서 다음 형식 중 하나를 사용하여 여러 큐 메시지를 씁니다. 

* `ICollector<T>` 또는 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

JavaScript 함수에서 `context.bindings.<name>`을 사용하여 출력 큐 메시지에 액세스합니다. 큐 항목 페이로드에 문자열 또는 JSON 직렬화 가능 개체를 사용할 수 있습니다.


## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 |  참조 |
|---|---|
| 큐 | [큐 오류 코드](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, 테이블, 큐 | [저장소 오류 코드](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, 테이블, 큐 |  [문제 해결](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x에서 이 바인딩에 사용할 수 있는 글로벌 구성 설정을 설명합니다. 아래 예제 host.json 파일에는 이 바인딩에 대한 버전 2.x 설정만 포함되어 있습니다. 버전 2.x의 글로벌 구성 설정에 대한 자세한 내용은 [Azure Functions 버전 2.x에 대한 host.json 참조](functions-host-json.md)를 참조하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|자산  |Default | 설명 |
|---------|---------|---------|
|maxPollingInterval|00:00:01|큐 폴링 사이의 최대 간격입니다. 최소 00:00:00.100 (100ms) 이며 최대 00시 01분: 00 (1 분 미만)를 증가 시킵니다. |
|visibilityTimeout|00:00:00|메시지 처리가 실패하는 경우 재시도 사이의 간격입니다. |
|batchSize|16|함수 런타임이 동시에 검색하고 병렬로 처리하는 큐 메시지 수입니다. 처리되는 개수가 `newBatchThreshold`로 감소하면 런타임은 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리되는 최대 동시 메시지 수는 `batchSize` + `newBatchThreshold`입니다. 이 제한은 큐 트리거 함수에 개별적으로 적용됩니다. <br><br>하나의 큐에 수신된 메시지에 대해 병렬 실행을 방지하려면 `batchSize`을 1로 설정합니다. 그러나 이 설정은 함수 앱이 단일 VM(가상 머신)에서 실행되는 동안에만 동시성을 제거합니다. 함수 앱이 여러 VM에 확장되면 각 VM은 각 큐 트리거 함수의 인스턴스 하나를 실행할 수 있습니다.<br><br>최대 `batchSize`은 32입니다. |
|maxDequeueCount|5|포이즌 큐로 이동하기 전에 메시지 처리를 시도할 횟수입니다.|
|newBatchThreshold|batchSize/2|동시에 처리되는 메시지의 수가 이 숫자로 내려갈 때마다 런타임은 다른 일괄 처리를 검색합니다.|

## <a name="next-steps"></a>다음 단계

* [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Queue Storage 출력 바인딩을 사용하는 자습서로 이동](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
