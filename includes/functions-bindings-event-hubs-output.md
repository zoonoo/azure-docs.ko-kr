---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1e25656b58fe675cfbe87fef75af4fcb174b7f55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589741"
---
Event Hubs 출력 바인딩을 사용하여 이벤트 스트림에 이벤트를 씁니다. 이벤트를 쓰려면 이벤트 허브에 대한 보내기 사용 권한이 있어야 합니다.

출력 바인딩을 구현 하려고 하기 전에 필요한 패키지 참조가 준비 되어 있는지 확인 합니다.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 메서드 반환 값을 출력으로 사용하여 이벤트 허브로 메시지를 쓰는 [C# 함수](../articles/azure-functions/functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

다음 예제에서는 `IAsyncCollector` 인터페이스를 사용 하 여 메시지 일괄 처리를 보내는 방법을 보여 줍니다. 이 시나리오는 하나의 이벤트 허브에서 들어오는 메시지를 처리 하 고 그 결과를 다른 이벤트 허브로 보내는 경우에 일반적입니다.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C#스크립트도](#tab/csharp-script)

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](../articles/azure-functions/functions-reference-csharp.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 함수 2.x 이상에 대 한 것이 고 두 번째 예제는 함수 1.x에 대 한 것입니다. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

단일 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

여러 메시지를 만드는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](../articles/azure-functions/functions-reference-node.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 함수 2.x 이상에 대 한 것이 고 두 번째 예제는 함수 1.x에 대 한 것입니다. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

단일 메시지를 보내는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

여러 메시지를 보내는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](../articles/azure-functions/functions-reference-python.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

단일 메시지를 보내는 Python 코드는 다음과 같습니다.

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

다음 예에서는 현재 시간을 포함 하는 메시지를 이벤트 허브에 기록 하는 Java 함수를 보여 줍니다.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에 게시되는 매개 변수에 대한 `@EventHubOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 POJO 또는 원시 Java 형식입니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)에서 [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) 특성을 사용합니다.

특성의 생성자는 이벤트 허브의 이름 및 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. `EventHub` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#example)를 참조하세요.

# <a name="c-script"></a>[C#스크립트도](#tab/csharp-script)

스크립트에서 C# 특성을 지원 하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원 하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서 특성을 지원 하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)에서 값이 이벤트 허브에 게시 되는 매개 변수에 대해 [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 주석을 사용 합니다. 매개 변수는 `OutputBinding<T>` 형식 이어야 합니다. 여기서 `T`는 POJO 또는 네이티브 Java 형식입니다.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `EventHub` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | "eventHub"로 설정해야 합니다. |
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 이벤트를 나타내는 함수 코드에서 사용되는 변수 이름입니다. |
|**path** |**EventHubName** | Functions 1.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**eventHubName** |**EventHubName** | 함수 2.x 이상 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**연결** |**연결** | 이벤트 허브의 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름입니다. 이벤트 허브 자체가 아닌 **네임스페이스**에 대한 *연결 정보* 단추를 클릭하여 이 연결 문자열을 복사합니다. 이 연결 문자열에는 이벤트 스트림으로 메시지를 보내기 위해 보내기 사용 권한이 있어야 합니다.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

`out string paramName`와 같은 메서드 매개 변수를 사용 하 여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 `name`function.json*의*  속성에 지정된 값입니다. 여러 메시지를 쓰려면 `ICollector<string>` 대신 `IAsyncCollector<string>` 또는 `out string`를 사용할 수 있습니다.

# <a name="c-script"></a>[C#스크립트도](#tab/csharp-script)

`out string paramName`와 같은 메서드 매개 변수를 사용 하 여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 `name`function.json*의*  속성에 지정된 값입니다. 여러 메시지를 쓰려면 `ICollector<string>` 대신 `IAsyncCollector<string>` 또는 `out string`를 사용할 수 있습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>`를 사용 하 여 출력 이벤트에 액세스 합니다. 여기서 `<name>`는 *함수. json*의 `name` 속성에 지정 된 값입니다.

# <a name="python"></a>[Python](#tab/python)

함수에서 이벤트 허브 메시지를 출력 하는 두 가지 옵션은 다음과 같습니다.

- **반환 값**: *함수 json* 의 `name` 속성을 `$return`로 설정 합니다. 이 구성을 사용 하 여 함수의 반환 값은 이벤트 허브 메시지로 유지 됩니다.

- **명령적**: [Out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) 형식으로 선언 된 매개 변수의 [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) 메서드에 값을 전달 합니다. `set` 전달 되는 값은 이벤트 허브 메시지로 유지 됩니다.

# <a name="java"></a>[Java](#tab/java)

[EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 주석을 사용 하 여 함수에서 이벤트 허브 메시지를 출력 하는 두 가지 옵션이 있습니다.

- **반환 값**: 함수 자체에 주석을 적용 하면 함수의 반환 값이 이벤트 허브 메시지로 유지 됩니다.

- **명령적**: 메시지 값을 명시적으로 설정 하려면 [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)형식의 특정 매개 변수에 주석을 적용 합니다. 여기서 `T`은 Pojo 또는 네이티브 Java 유형입니다. 이 구성을 사용 하 여 `setValue` 메서드에 값을 전달 하면 값이 이벤트 허브 메시지로 유지 됩니다.

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| 이벤트 허브 | [운영 가이드](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x 이상에서이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명 합니다. 아래의 예제 호스트 json 파일에는이 바인딩에 대 한 버전 2.x + 설정만 포함 되어 있습니다. 2\.x 이상 버전의 전역 구성 설정에 대 한 자세한 내용은 [Azure Functions에 대 한 호스트 json 참조](../articles/azure-functions/functions-host-json.md)를 참조 하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](../articles/azure-functions/functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|속성  |기본값 | Description |
|---------|---------|---------|
|`maxBatchSize`|10|수신 루프 당 받은 최대 이벤트 수입니다.|
|`prefetchCount`|300|기본 `EventProcessorHost`에서 사용 하는 기본 사전 인출 수입니다.|
|`batchCheckpointFrequency`|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.|
