---
title: "Azure Functions Service Bus 트리거 및 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure 서비스 버스 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/01/2017
ms.author: chrande; glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 58b6d5c6ef40891e56ea4811f5e778286bdb8bc3
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-functions-service-bus-bindings"></a>Azure Functions Service Bus 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure Service Bus 바인딩을 구성하고 사용하는 방법을 설명합니다. 

Azure Functions는 Service Bus 큐 및 토픽에 대한 트리거 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="service-bus-trigger"></a>Service Bus 트리거
Service Bus 트리거를 사용하여 Service Bus 큐 또는 토픽의 메시지에 응답합니다. 

함수에 대한 Service Bus 큐 및 토픽 트리거는 function.json의 `bindings` 배열에 있는 다음과 같은 JSON 개체로 정의됩니다.

* *큐* 트리거:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

* *토픽* 트리거:

    ```json
    {
        "name" : "<Name of input parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBusTrigger",
        "direction" : "in"
    }
    ```

다음 사항에 유의하세요.

* `connection`의 경우 Service Bus의 네임스페이스에 대한 연결 문자열을 포함하는 [함수 앱의 앱 설정을 만들고](functions-how-to-use-azure-function-app-settings.md), 다음으로 트리거의 `connection` 속성에서 앱 설정의 이름을 지정합니다. [관리 자격 증명 얻기](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)에 나온 단계에 따라 연결 문자열을 얻습니다.
  연결 문자열은 서비스 버스 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다.
  `connection`을 비워두면 트리거는 기본 Service Bus 연결 문자열이 `AzureWebJobsServiceBus`라는 앱 설정에서 지정한 것으로 가정합니다.
* `accessRights`의 경우 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 `listen`으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다.

## <a name="trigger-behavior"></a>트리거 동작
* **단일 스레딩** - 기본적으로 함수 런타임은 여러 개의 메시지를 동시에 처리합니다. 런타임이 큐 또는 토픽 메시지를 한 번에 하나만 처리하도록 하려면, *host.json*에서 `serviceBus.maxConcurrentCalls`를 1로 설정합니다. 
  *host.json*에 대한 자세한 내용은 [폴더 구조](functions-reference.md#folder-structure) 및 [host.json](https://github .com/Azure/azure-webjobs-sdk-script/wiki/host.json)을 참조하세요.
* **포이즌 메시지 처리** - Service Bus는 Azure Functions 구성 또는 코드로 제어되거나 구성될 수 없는 자체 포이즌 메시지 처리를 수행합니다. 
* **PeekLock 동작** - 함수 런타임은 [`PeekLock` 모드](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode)로 메시지를 받아 함수가 정상적으로 완료된 경우 메시지에서 `Complete`를 호출하고, 함수가 실패한 경우 `Abandon`을 호출합니다. 
  함수가 `PeekLock` 시간 제한보다 오래 실행되는 경우 잠금이 자동으로 갱신됩니다.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>트리거 사용
이 섹션에서는 함수 코드에서 Service Bus 트리거를 사용하는 방법을 보여 줍니다. 

C# 및 F#에서 Service Bus 트리거 메시지를 다음 입력 형식 중 하나로 deserialize할 수 있습니다.

* `string` - 문자열 메시지에 유용
* `byte[]` - 이진 데이터에 유용
* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx) - JSON 직렬화 데이터에 유용합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `CustomType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* `BrokeredMessage` - [BrokeredMessage.GetBody<T>()](https://msdn.microsoft.com/library/hh144211.aspx) 메서드를 사용하는 deserialize된 메시지가 표시됩니다.

Node.js에서 Service Bus 트리거 메시지가 문자열 또는 JSON 개체로 함수에 전달됩니다.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>트리거 샘플
다음과 같은 function.json이 있다고 가정합니다.

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Service Bus 큐 메시지를 처리하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C#에서 트리거 샘플 #

```cs
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F#에서 트리거 샘플 #

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js에서 트리거 샘플

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

<a name="output"></a>

## <a name="service-bus-output-binding"></a>Service Bus 출력 바인딩
함수에 대한 Service Bus 큐 및 토픽 출력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

* *큐* 출력:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "queueName" : "<Name of the queue>",
        "connection" : "<Name of app setting that has your queue's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```
* *토픽* 출력:

    ```json
    {
        "name" : "<Name of output parameter in function signature>",
        "topicName" : "<Name of the topic>",
        "subscriptionName" : "<Name of the subscription>",
        "connection" : "<Name of app setting that has your topic's connection string - see below>",
        "accessRights" : "<Access rights for the connection string - see below>",
        "type" : "serviceBus",
        "direction" : "out"
    }
    ```

다음 사항에 유의하세요.

* `connection`의 경우 Service Bus의 네임스페이스에 대한 연결 문자열을 포함하는 [함수 앱의 앱 설정을 만들고](functions-how-to-use-azure-function-app-settings.md), 다음으로 출력 바인딩의 `connection` 속성에서 앱 설정의 이름을 지정합니다. [관리 자격 증명 얻기](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#obtain-the-management-credentials)에 나온 단계에 따라 연결 문자열을 얻습니다.
  연결 문자열은 서비스 버스 네임스페이스에 대한 것이어야 하며, 특정 큐 또는 항목으로 제한되지 않습니다.
  `connection`을 비워두면 출력 바인딩은 기본 Service Bus 연결 문자열이 `AzureWebJobsServiceBus`라는 앱 설정에서 지정한 것으로 가정합니다.
* `accessRights`의 경우 사용 가능한 값은 `manage` 및 `listen`입니다. 기본값은 `manage`이며, `connection`에 **관리** 권한이 있음을 의미합니다. **관리** 권한이 없는 연결 문자열을 사용하는 경우 `accessRights`을 `listen`으로 설정합니다. 그렇지 않으면 함수 런타임은 관리 권한이 필요한 작업 시도를 실패할 수 있습니다.

<a name="outputusage"></a>

## <a name="output-usage"></a>출력 사용
C# 및 F#에서 Azure Functions은 다음 형식으로부터 Service Bus 큐 메시지를 만들 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx) - 매개 변수 정의는 `out T paramName`(C#)과 같습니다.
  함수는 JSON 메시지에 개체를 deserialize합니다. 함수가 종료될 때 출력 값이 null이면 함수는 null 개체와 메시지를 만듭니다.
* `string` - 매개 변수 정의는 `out string paraName`(C#)과 같습니다. 함수가 종료될 때 매개 변수 값이 null이 아닌 경우 함수는 메시지를 만듭니다.
* `byte[]` - 매개 변수 정의는 `out byte[] paraName`(C#)과 같습니다. 함수가 종료될 때 매개 변수 값이 null이 아닌 경우 함수는 메시지를 만듭니다.
* `BrokeredMessage` 매개 변수 정의는 `out BrokeredMessage paraName`(C#)과 같습니다. 함수가 종료될 때 매개 변수 값이 null이 아닌 경우 함수는 메시지를 만듭니다.

C# 함수에서 여러 메시지를 만들려면, `ICollector<T>` 또는 `IAsyncCollector<T>`를 사용할 수 있습니다. 메시지는 `Add` 메서드를 호출할 때 생성됩니다.

Node.js에서 문자열, 바이트 배열 또는 Javascript 개체(JSON으로 deserialize됨)를 `context.binding.<paramName>`에 할당할 수 있습니다.

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
Service Bus 큐 출력을 정의하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Service Bus 큐에 메시지를 전송하는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C#에서 출력 샘플 #

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

또는 여러 메시지 만들려면 다음을 수행합니다.

```cs
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F#에서 출력 샘플 #

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js에서 출력 샘플

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

또는 여러 메시지 만들려면 다음을 수행합니다.

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = [];
    context.bindings.outputSbQueueMsg.push("1 " + message);
    context.bindings.outputSbQueueMsg.push("2 " + message);
    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


