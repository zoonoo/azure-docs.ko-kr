---
title: "Azure Functions Event Hubs 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure Event Hubs 바인딩을 사용하는 방법을 이해합니다."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/20/2017
ms.author: wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 19021bef8b7156b3049f43b0275c0ed0c6b22514
ms.contentlocale: ko-kr
ms.lasthandoff: 07/08/2017


---
# <a name="azure-functions-event-hubs-bindings"></a>Azure Functions Event Hubs 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에 [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) 바인딩을 구성하고 코딩하는 방법에 대해 설명합니다.
Azure Functions는 이벤트 허브에 대한 트리거 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Event Hubs를 처음 사용하는 경우 [Event Hubs 개요](../event-hubs/event-hubs-what-is-event-hubs.md)를 참조하세요.

<a name="trigger"></a>

## <a name="event-hub-trigger"></a>Event Hubs 트리거
Event Hubs 트리거를 사용하여 이벤트 허브 이벤트 스트림으로 보낸 이벤트에 응답합니다. 트리거를 설정하려면 이벤트 허브에 대한 읽기 권한이 있어야 합니다.

Event Hubs 함수 트리거는 function.json의 `bindings` 배열에 있는 다음 JSON 개체를 사용합니다.

```json
{
    "type": "eventHubTrigger",
    "name": "<Name of trigger parameter in function signature>",
    "direction": "in",
    "path": "<Name of the event hub>",
    "consumerGroup": "Consumer group to use - see below",
    "connection": "<Name of app setting with connection string - see below>"
}
```

`consumerGroup`은 선택적 속성으로, 허브의 이벤트를 구독하는 데 사용되는 [소비자 그룹](../event-hubs/event-hubs-features.md#event-consumers)을 설정합니다. 생략한 경우 `$Default` 소비자 그룹이 사용됩니다.  
`connection`은 이벤트 허브의 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름이어야 합니다.
이벤트 허브 자체가 아닌 *네임스페이스*에 대한 **연결 정보** 단추를 클릭하여 이 연결 문자열을 복사합니다. 트리거를 활성화하려면 이 연결 문자열은 적어도 읽기 권한이 있어야 합니다.

host.json 파일에 [추가 설정](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)을 제공하여 Event Hubs 트리거를 더 세밀하게 튜닝할 수 있습니다.  

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>트리거 사용
Event Hubs 트리거 함수를 트리거하는 경우 트리거되는 메시지가 함수에 문자열로 전달됩니다.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>트리거 샘플
function.json의 `bindings` 배열에 다음과 같은 Event Hubs 트리거가 있다고 가정합니다.

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionString"
}
```

이벤트 허브 트리거의 메시지 본문을 기록하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C#에서 트리거 샘플 #

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

또한 해당 이벤트를 이벤트 메타데이터에 액세스할 수 있도록 하는 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 개체로도 수신할 수 있습니다.

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage, TraceWriter log)
{
    log.Info($"{Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
}
```

이벤트를 일괄로 수신하려면 메서드 서명을 `string[]` 또는 `EventData[]`로 변경합니다.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F#에서 트리거 샘플 #

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js에서 트리거 샘플

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
    context.done();
};
```

<a name="output"></a>

## <a name="event-hubs-output-binding"></a>Event Hubs 출력 바인딩
Event Hubs 출력 바인딩을 사용하여 이벤트 허브 이벤트 스트림에 이벤트를 씁니다. 이벤트를 쓰려면 이벤트 허브에 대한 보내기 사용 권한이 있어야 합니다.

출력 바인딩은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "type": "eventHub",
    "name": "<Name of output parameter in function signature>",
    "path": "<Name of event hub>",
    "connection": "<Name of app setting with connection string - see below>"
    "direction": "out"
}
```

`connection`은 이벤트 허브의 네임스페이스에 대한 연결 문자열을 포함하는 앱 설정의 이름이어야 합니다.
이벤트 허브 자체가 아닌 *네임스페이스*에 대한 **연결 정보** 단추를 클릭하여 이 연결 문자열을 복사합니다. 이 연결 문자열에는 이벤트 스트림으로 메시지를 보내기 위해 보내기 사용 권한이 있어야 합니다.

## <a name="output-usage"></a>출력 사용
이 섹션에서는 함수 코드에서 Event Hubs 출력 바인딩을 사용하는 방법을 보여 줍니다.

다음 매개 변수 형식의 구성된 이벤트 허브로 메시지를 출력할 수 있습니다.

* `out string`
* `ICollector<string>`(여러 메시지 출력)
* `IAsyncCollector<string>`(비동기 버전의 `ICollector<T>`)

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
function.json의 `bindings` 배열에 다음과 같은 Event Hubs 출력 바인딩이 있다고 가정합니다.

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSend",
    "direction": "out"
}
```

짝수 스트림에 이벤트를 작성하는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C#에서 출력 샘플 #

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

또는 여러 메시지 만들려면 다음을 수행합니다.

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F#에서 출력 샘플 #

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

<a name="outnodejs"></a>

### <a name="output-sample-for-nodejs"></a>Node.js에서 출력 샘플

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

또는 여러 메시지를 전송하려면 다음을 수행합니다.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

