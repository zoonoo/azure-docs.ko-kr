---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 1957fa4310a22a162ee2a621d1e0349e253badb3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456570"
---
## <a name="trigger"></a>트리거

함수 트리거를 사용 하 여 이벤트 허브 이벤트 스트림에 전송 된 이벤트에 응답 합니다. 트리거를 설정 하려면 기본 이벤트 허브에 읽기 권한이 있어야 합니다. 함수가 트리거될 때 함수에 전달 된 메시지를 문자열로 형식화 됩니다.

## <a name="trigger---scaling"></a>트리거 - 크기 조정

이벤트가 트리거 함수의 각 인스턴스는 단일 지원 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 인스턴스. 트리거 (Event Hubs에서 제공)를 통해 하나만 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 인스턴스는 지정 된 파티션에 임대를 가져올 수 있습니다.

예를 들어, 다음과 같은 Event Hub를 고려합니다.

* 10 개의 파티션
* 각 파티션에 100 개의 메시지를 사용 하 여 모든 파티션에 균등 하 게 분산 하는 1,000 개의 이벤트

함수를 처음 사용하는 경우 함수 인스턴스가 하나만 있습니다. 첫 번째 함수 인스턴스를 호출 해 보겠습니다. `Function_0`합니다. 합니다 `Function_0` 함수는 단일 인스턴스의 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 10 개의 모든 파티션에 대해 임대를 보유 하는 합니다. 이 인스턴스는 파티션 0-9에서 이벤트를 읽습니다. 이 지점부터 다음 중 하나가 발생합니다.

* **새 함수 인스턴스 필요 하지 않은**: `Function_0` 함수 크기 조정 논리를 적용 하기 전에 모든 1,000 개의 이벤트를 처리할 수 있습니다. 모든 1,000 메시지를 처리 하는 예제의 경우 `Function_0`합니다.

* **추가 함수 인스턴스가 추가됨**: 경우 함수는 결정 논리를 크기 조정 `Function_0` 처리할 수, 새 함수 앱 인스턴스를 보다 더 많은 메시지가 있음 (`Function_1`) 만들어집니다. 이 새 함수 관련된 인스턴스의 역시 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor)합니다. 새 호스트 인스턴스 메시지를 읽으려는 시도 검색 하는 기본 Event Hubs, 부하 분산에 걸친 파티션은 해당 호스트 인스턴스. 예를 들어, 파티션 0-4는 `Function_0`에, 파티션 5-9는 `Function_1`에 할당할 수 있습니다.

* **N 이상 함수 인스턴스 추가됨**: 경우 함수 확인 크기 조정 논리 둘 다 `Function_0` 하 고 `Function_1` 자세한 메시지를 처리할 수 있는, 새로운 보다 `Functions_N` 함수 앱 인스턴스가 생성 됩니다.  앱은 지점에 생성 됩니다 여기서 `N` 이벤트 허브 파티션 수보다 큽니다. 이 예에서 Event Hubs는 다시 파티션을 부하 분산합니다(이 경우, 인스턴스 `Function_0`...`Functions_9`로).

눈금이 때 함수 `N` 인스턴스는 이벤트 허브 파티션 수보다 큰 숫자입니다. 이렇게 되도록 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 인스턴스가 다른 인스턴스에서 사용 가능 해지면 이러한 파티션에 대 한 잠금을 가져올 수 있습니다. 만 함수 인스턴스가 실행 될 때 사용 하는 리소스에 대 한 요금이 청구 됩니다. 즉,이 과도 한 프로 비전에 대 한 청구 되지 됩니다.

모든 함수 실행이 오류 없이 완료되면 연결된 저장소 계정에 검사점이 추가됩니다. 검사점에 성공 하면 모든 1000 개의 메시지 다시 검색 되지 않습니다.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [C#](#trigger---c-example)
* [C# 스크립트(.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제에서는 이벤트 허브 트리거의 메시지 본문을 기록하는 [C# 함수](../articles/azure-functions/functions-dotnet-class-library.md)를 보여줍니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

함수 코드에서 [이벤트 메타데이터](#trigger---event-metadata)에 대한 액세스를 얻으려면 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체에 바인딩합니다(`Microsoft.Azure.EventHubs`에 using 문이 필요함). 메서드 시그니처의 바인딩 식을 사용하여 동일한 속성에 액세스할 수도 있습니다.  다음 예제에서는 동일한 데이터를 가져오는 두 가지 방법을 모두 보여줍니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

일괄 처리에서 이벤트를 수신하려면 `string` 또는 `EventData` 배열을 만듭니다.  

> [!NOTE]
> 일괄로 이벤트를 수신하면 `DateTime enqueuedTimeUtc`를 사용하여 위 예제와 같이 메서드 매개 변수에 바인딩할 수 없으며 각 `EventData` 개체에서 이러한 매개 변수를 수신해야 합니다.  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](../articles/azure-functions/functions-reference-csharp.md)를 보여줍니다. 함수는 이벤트 허브 트리거의 메시지 본문을 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

#### <a name="version-2x"></a>버전 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

함수 코드에서 [이벤트 메타데이터](#trigger---event-metadata)에 대한 액세스를 얻으려면 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체에 바인딩합니다(`Microsoft.Azure.EventHubs`에 using 문이 필요함). 메서드 시그니처의 바인딩 식을 사용하여 동일한 속성에 액세스할 수도 있습니다.  다음 예제에서는 동일한 데이터를 가져오는 두 가지 방법을 모두 보여줍니다.

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

일괄 처리에서 이벤트를 수신하려면 `string` 또는 `EventData` 배열을 만듭니다.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>트리거 - F# 예제

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [F# 함수](../articles/azure-functions/functions-reference-fsharp.md)를 보여줍니다. 함수는 이벤트 허브 트리거의 메시지 본문을 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 

#### <a name="version-2x"></a>버전 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

F# 코드는 다음과 같습니다.

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](../articles/azure-functions/functions-reference-node.md)를 보여줍니다. 함수는 [이벤트 메타데이터](#trigger---event-metadata)를 읽고 메시지를 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

#### <a name="version-2x"></a>버전 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

일괄 처리에서 이벤트를 수신하려면 다음 예제에 표시된 대로 *function.json* 파일에서 `cardinality`를 `many`로 설정합니다.

#### <a name="version-2x"></a>버전 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>버전 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---python-example"></a>트리거 - Python 예제

다음 예제에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [Python 함수](../articles/azure-functions/functions-reference-python.md)를 보여줍니다. 함수는 [이벤트 메타데이터](#trigger---event-metadata)를 읽고 메시지를 기록합니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>트리거 - Java 예제

다음 예제에서는 *function.json* 파일의 Event Hub 트리거 바인딩 및 바인딩을 사용하는 [Java 함수](../articles/azure-functions/functions-reference-java.md)를 보여줍니다. 함수는 Event Hub 트리거의 메시지 본문을 기록합니다.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 [Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에서 제공되는 매개 변수에 대한 `EventHubTrigger` 주석을 사용합니다. 이러한 주석을 사용하는 매개 변수로 인해 이벤트 도착 시 함수가 실행될 수 있습니다.  옵션 <T>를 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

## <a name="trigger---attributes"></a>트리거 - 특성

[C# 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)에서 [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 특성을 사용합니다.

특성의 생성자는 이벤트 허브의 이름, 소비자 그룹의 이름 및 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [트리거 구성 섹션](#trigger---configuration)을 참조하세요. `EventHubTriggerAttribute` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

전체 예제는 [트리거 - C# 예제](#trigger---c-example)를 참조하세요.

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `EventHubTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | `eventHubTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 이벤트 항목을 나타내는 변수의 이름입니다. |
|**path** |**EventHubName** | Functions 1.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**eventHubName** |**EventHubName** | Functions 2.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**consumerGroup** |**ConsumerGroup** | 설정 하는 선택적 속성을 [소비자 그룹](../articles/event-hubs/event-hubs-features.md)#event 소비자) 허브에서 이벤트를 구독 하는 데 사용 합니다. 생략한 경우 `$Default` 소비자 그룹이 사용됩니다. |
|**cardinality** | 해당 없음 | JavaScript의 경우 `many`로 설정하여 일괄 처리할 수 있도록 합니다.  생략되거나 `one`로 설정한 경우 단일 메시지가 함수에 전달됩니다. |
|**연결** |**연결** | 이벤트 허브의 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름입니다. 클릭 하 여이 연결 문자열을 복사 합니다 **연결 정보** 단추를 [네임 스페이스](../articles/event-hubs/event-hubs-create.md)#create-에서---event hubs), 이벤트 허브 자체가 아닌 합니다. 트리거를 활성화하려면 이 연결 문자열은 적어도 읽기 권한이 있어야 합니다.|
|**path**|**EventHubName**|이벤트 허브의 이름입니다. 앱 설정 `%eventHubName%`을 통해 참조할 수 있습니다.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>트리거 - 이벤트 메타데이터

Event Hubs 트리거는 몇 가지 [메타데이터 속성](../articles/azure-functions/./functions-bindings-expressions-patterns.md)을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) 클래스의 속성은 다음과 같습니다.

|자산|Type|설명|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` 인스턴스|
|`EnqueuedTimeUtc`|`DateTime`|큐에 대기된 시간(UTC)입니다.|
|`Offset`|`string`|Event Hub 파티션 스트림을 기준으로 데이터의 오프셋 오프셋은 Event Hubs 스트림 내의 이벤트에 대한 표식 또는 식별자입니다. 식별자는 Event Hubs 스트림의 파티션 내에서 고유합니다.|
|`PartitionKey`|`string`|이벤트 데이터를 전송해야 하는 파티션|
|`Properties`|`IDictionary<String,Object>`|이벤트 데이터의 사용자 속성|
|`SequenceNumber`|`Int64`|이벤트의 논리적 시퀀스 번호|
|`SystemProperties`|`IDictionary<String,Object>`|이벤트 데이터를 비롯한 시스템 속성|

이 아티클의 앞부분에서 이러한 속성을 사용하는 [코드 예제](#trigger---example)를 참조하세요.

## <a name="trigger---hostjson-properties"></a>트리거 - host.json 속성

[host.json](../articles/azure-functions/functions-host-json.md#eventhub) 파일에는 Event Hubs 트리거 동작을 제어하는 설정이 포함됩니다.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>출력

Event Hubs 출력 바인딩을 사용하여 이벤트 스트림에 이벤트를 씁니다. 이벤트를 쓰려면 이벤트 허브에 대한 보내기 사용 권한이 있어야 합니다.

필요한 패키지 참조가 작동하는지 확인합니다. Functions 1.x 또는 Functions 2.x

## <a name="output---example"></a>출력 - 예제

언어 관련 예제를 참조하세요.

* [C#](#output---c-example)
* [C# 스크립트(.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>출력 - C# 예제

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

### <a name="output---c-script-example"></a>출력 - C# 스크립트 예제

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](../articles/azure-functions/functions-reference-csharp.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 Functions 2.x이고 두 번째 예제는 Functions 1.x입니다. 

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

### <a name="output---f-example"></a>출력 - F# 예제

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [F# 함수](../articles/azure-functions/functions-reference-fsharp.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 Functions 2.x이고 두 번째 예제는 Functions 1.x입니다. 

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

F# 코드는 다음과 같습니다.

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>출력 - JavaScript 예제

다음 예에서는 *function.json* 파일의 이벤트 허브 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](../articles/azure-functions/functions-reference-node.md)를 보여줍니다. 함수는 이벤트 허브로 메시지를 씁니다.

다음 예제에서는 *function.json* 파일에 있는 Event Hubs 데이터 바인딩을 표시합니다. 첫 번째 예제는 Functions 2.x이고 두 번째 예제는 Functions 1.x입니다. 

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

### <a name="output---python-example"></a>출력 - Python 예제

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
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>출력 - Java 예제

다음 예제에서는 Event Hub에 현재 시간을 포함하는 메시지를 작성하는 Java 함수를 보여줍니다.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Event Hub에 게시되는 매개 변수에 대한 `@EventHubOutput` 주석을 사용합니다.  매개 변수 형식은 `OutputBinding<T>`이어야 합니다. 여기서 T는 POJO 또는 원시 Java 형식입니다.

## <a name="output---attributes"></a>출력 - 특성

[C# 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)에서 [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 특성을 사용합니다.

특성의 생성자는 이벤트 허브의 이름 및 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#output---configuration)을 참조하세요. `EventHub` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

전체 예제는 [출력 - C# 예제](#output---c-example)를 참조하세요.

## <a name="output---configuration"></a>출력 - 구성

다음 표에서는 *function.json* 파일 및 `EventHub` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | "eventHub"로 설정해야 합니다. |
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 이벤트를 나타내는 함수 코드에서 사용되는 변수 이름입니다. |
|**path** |**EventHubName** | Functions 1.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**eventHubName** |**EventHubName** | Functions 2.x에만 해당합니다. 이벤트 허브의 이름입니다. 이벤트 허브 이름이 연결 문자열에 있는 경우 해당 값은 런타임 시 이 속성을 재정의합니다. |
|**연결** |**연결** | 이벤트 허브의 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름입니다. 이벤트 허브 자체가 아닌 *네임스페이스*에 대한 **연결 정보** 단추를 클릭하여 이 연결 문자열을 복사합니다. 이 연결 문자열에는 이벤트 스트림으로 메시지를 보내기 위해 보내기 사용 권한이 있어야 합니다.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>출력 - 사용

C# 및 C# 스크립트에서는 `out string paramName`과 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 여러 메시지를 쓰려면 `out string` 대신 `ICollector<string>` 또는 `IAsyncCollector<string>`를 사용할 수 있습니다.

JavaScript에서 `context.bindings.<name>`를 사용하여 출력 이벤트에 액세스합니다. `<name>`은 *function.json*의 `name` 속성에 지정된 값입니다.

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참조 |
|---|---|
| 이벤트 허브 | [운영 가이드](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x에서 이 바인딩에 사용할 수 있는 글로벌 구성 설정을 설명합니다. 아래 예제 host.json 파일에는 이 바인딩에 대한 버전 2.x 설정만 포함되어 있습니다. 버전 2.x의 글로벌 구성 설정에 대한 자세한 내용은 [Azure Functions 버전 2.x에 대한 host.json 참조](../articles/azure-functions/functions-host-json.md)를 참조하세요.

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

|자산  |Default | 설명 |
|---------|---------|---------|
|maxBatchSize|64|수신 루프 당 받은 최대 이벤트 수입니다.|
|prefetchCount|해당 없음|기본 EventProcessorHost에서 사용할 기본 PrefetchCount입니다.|
|batchCheckpointFrequency|1|EventHub 커서 검사점을 만들기 전에 처리할 이벤트 일괄 처리 수입니다.|