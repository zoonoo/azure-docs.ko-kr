---
title: Azure Functions에 대 한 Azure Queue storage 출력 바인딩
description: Azure Functions에서 Azure Queue storage 메시지를 만드는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 778424cbb81f8fe51a57dd41d94aa9015ffad94e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381514"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Azure Functions에 대 한 Azure Queue storage 출력 바인딩

Azure Functions는 출력 바인딩을 설정 하 여 새 Azure 큐 저장소 메시지를 만들 수 있습니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](./functions-bindings-storage-queue.md)를 참조하세요.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 수신된 각 HTTP 요청에 대한 큐 메시지를 만드는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

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
      "name": "$return"
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

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

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

# <a name="java"></a>[Java](#tab/java)

 다음 예제에서는 HTTP 요청에 의해 트리거되는 경우에 대 한 큐 메시지를 만드는 Java 함수를 보여 줍니다.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Queue Storage에 작성되는 매개 변수에 대한 `@QueueOutput` 주석을 사용합니다.  매개 변수 형식은 이어야 `OutputBinding<T>` `T` 합니다. 여기서은 pojo의 네이티브 Java 형식입니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

`myQueueItem` 출력 바인딩에 대한 메시지 배열을 정의하여 한 번에 여러 메시지를 보낼 수 있습니다. 다음 JavaScript 코드는 수신된 각 HTTP 요청에 하드코딩된 값이 포함된 두 개의 큐 메시지를 보냅니다.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

다음 코드 예제에서는 HTTP로 트리거되는 함수에서 큐 메시지를 출력 하는 방법을 보여 줍니다. 가 인 구성 섹션은 `type` `queue` 출력 바인딩을 정의 합니다.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

PowerShell 함수는이 바인딩 구성을 사용 하 여를 사용 하 여 큐 메시지를 만들 수 있습니다 `Push-OutputBinding` . 이 예에서는 쿼리 문자열이 나 본문 매개 변수에서 메시지를 만듭니다.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

여러 메시지를 한 번에 보내려면 메시지 배열을 정의 하 고 `Push-OutputBinding` 를 사용 하 여 메시지를 큐 출력 바인딩으로 보냅니다.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 단일 및 여러 값을 저장소 큐에 출력 하는 방법을 보여 줍니다. *function.js* 에 필요한 구성은 모두 동일 합니다.

저장소 큐 바인딩은 *형식이* 로 설정 된 *function.js* 에서 정의 됩니다 `queue` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

큐에서 개별 메시지를 설정 하려면 메서드에 단일 값을 전달 `set` 합니다.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

큐에서 여러 메시지를 만들려면 매개 변수를 적절 한 목록 유형으로 선언 하 고 목록 유형과 일치 하는 값 배열을 `set` 메서드에 전달 합니다.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs)를 사용합니다.

특성은 함수의 `out` 매개 변수 또는 반환 값에 적용됩니다. 특성의 생성자는 다음 예제와 같이 큐의 이름을 사용합니다.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

전체 예제는 [출력 예제](#example)를 참조 하세요.

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 트리거 - 특성을 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`QueueOutput`주석을 사용 하면 함수의 출력으로 메시지를 작성할 수 있습니다. 다음 예제에서는 큐 메시지를 만드는 HTTP 트리거 함수를 보여 줍니다.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| 속성    | 설명 |
|-------------|-----------------------------|
|`name`       | 함수 시그니처의 매개 변수 이름을 선언 합니다. 함수가 트리거되면이 매개 변수의 값에 큐 메시지의 내용이 포함 됩니다. |
|`queueName`  | 저장소 계정에서 큐 이름을 선언 합니다. |
|`connection` | 저장소 계정 연결 문자열을 가리킵니다. |

주석과 연결 된 매개 변수는 `QueueOutput` [outputbinding \<T\> ](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) 인스턴스로 형식화 됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

특성은 PowerShell에서 지원 되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `Queue` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `queue`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `out`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 큐를 나타내는 변수의 이름입니다. `$return`으로 설정하여 함수 반환 값을 참조합니다.|
|**queueName** |**QueueName** | 큐의 이름입니다. |
|**connection** | **연결** |이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다.<br><br>예를 들어를 `connection` "mystorage"로 설정 하는 경우 함수 런타임은 "MyStorage" 라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.<br><br>연결 문자열 대신 [버전 4.x 이상의 확장](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)을 사용 하는 경우 연결을 정의 하는 구성 섹션에 대 한 참조를 제공할 수 있습니다. [연결](./functions-reference.md#connections)을 참조 하세요.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>기본값

와 같은 메서드 매개 변수를 사용 하 여 단일 큐 메시지를 작성 `out T paramName` 합니다. `out` 매개 변수 대신 메서드 반환 형식을 사용할 수 있습니다. `T`는 다음 형식 중 하나일 수 있습니다.

* JSON으로 직렬화 가능한 개체
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

C# 및 C# 스크립트에서 다음 형식 중 하나를 사용하여 여러 큐 메시지를 씁니다. 

* `ICollector<T>` 또는 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>추가 형식

[5.0.0 이상의 저장소 확장 버전](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) 을 사용 하는 앱은 [Azure SDK for .net](/dotnet/api/overview/azure/storage.queues-readme)의 형식을 사용할 수도 있습니다. 이 버전은 레거시 및 형식에 대 한 지원을 `CloudQueue` `CloudQueueMessage` 다음 형식으로 대체 합니다.

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- 여러 큐 메시지를 쓰기 위한 [QueueClient](/dotnet/api/azure.storage.queues.queueclient)

이러한 유형을 사용 하는 예제는 [확장에 대 한 GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)를 참조 하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>기본값

와 같은 메서드 매개 변수를 사용 하 여 단일 큐 메시지를 작성 `out T paramName` 합니다. 는 `paramName` `name` *function.js* 의 속성에 지정 된 값입니다. `out` 매개 변수 대신 메서드 반환 형식을 사용할 수 있습니다. `T`는 다음 형식 중 하나일 수 있습니다.

* JSON으로 직렬화 가능한 개체
* `string`
* `byte[]`
* [CloudQueueMessage] 

`CloudQueueMessage`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

C# 및 C# 스크립트에서 다음 형식 중 하나를 사용하여 여러 큐 메시지를 씁니다. 

* `ICollector<T>` 또는 `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>추가 형식

[5.0.0 이상의 저장소 확장 버전](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) 을 사용 하는 앱은 [Azure SDK for .net](/dotnet/api/overview/azure/storage.queues-readme)의 형식을 사용할 수도 있습니다. 이 버전은 레거시 및 형식에 대 한 지원을 `CloudQueue` `CloudQueueMessage` 다음 형식으로 대체 합니다.

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- 여러 큐 메시지를 쓰기 위한 [QueueClient](/dotnet/api/azure.storage.queues.queueclient)

이러한 유형을 사용 하는 예제는 [확장에 대 한 GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples)를 참조 하세요.

# <a name="java"></a>[Java](#tab/java)

[Queueoutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput) 주석을 사용 하 여 함수에서 큐 메시지를 출력 하는 두 가지 옵션이 있습니다.

- **반환 값**: 함수 자체에 주석을 적용 하면 함수의 반환 값이 큐 메시지로 유지 됩니다.

- **명령형**: 메시지 값을 명시적으로 설정하려면 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 형식의 특정 매개 변수에 주석을 적용합니다. 여기서 `T`는 POJO 또는 네이티브 Java 형식입니다. 이 구성을 사용 하 여 메서드에 값을 전달 하면 `setValue` 값이 큐 메시지로 유지 됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

출력 큐 항목은 `context.bindings.<NAME>` `<NAME>` *function.js에* 정의 된 이름과 일치 하는를 통해 사용할 수 있습니다. 큐 항목 페이로드에 문자열 또는 JSON 직렬화 가능 개체를 사용할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

`Push-OutputBinding`function.js파일의 바인딩 매개 변수에 지정 된 이름과 일치 하는 인수를 전달 하 여 큐 메시지에 대 한 출력을 사용할 수 있습니다 `name` . 

# <a name="python"></a>[Python](#tab/python)

함수에서 큐 메시지를 출력 하는 두 가지 옵션은 다음과 같습니다.

- **반환 값**: *function.json* 의 `name` 속성을 `$return`으로 설정합니다. 이 구성을 사용 하면 함수의 반환 값이 큐 저장소 메시지로 유지 됩니다.

- **명령형**: [출력](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) 형식으로 선언된 매개 변수의 [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) 메서드에 값을 전달합니다. 에 전달 된 값은 `set` 큐 저장소 메시지로 유지 됩니다.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 |  참고 |
|---|---|
| 큐 | [큐 오류 코드](/rest/api/storageservices/queue-service-error-codes) |
| Blob, 테이블, 큐 | [스토리지 오류 코드](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, 테이블, 큐 |  [문제 해결](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>다음 단계

- [큐 저장소 데이터 변경 내용으로 함수 실행 (트리거)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
