---
title: "Azure Functions Storage 큐 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: christopheranderson
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
ms.date: 11/02/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 96f253f14395ffaf647645176b81e7dfc4c08935
ms.openlocfilehash: 36cf563a8318acb9371c48ba7d29e24694446e45


---
# <a name="azure-functions-storage-queue-bindings"></a>Azure Functions Storage 큐 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure Storage 큐 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 Azure Storage 큐에 대한 트리거 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="trigger"></a>

## <a name="storage-queue-trigger"></a>Storage 큐 트리거
Azure Storage 큐 트리거를 사용하면 새 메시지에 대한 저장소 큐를 모니터링하고 이에 반응할 수 있습니다. 

함수에 대한 Storage 큐 트리거는 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "queueName": "<Name of queue to poll>",
    "connection":"<Name of app setting - see below>",
    "type": "queueTrigger",
    "direction": "in"
}
```

`connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 이 앱 설정을 수동으로 만들려면 [이 앱 설정을 수동으로 구성]()을 참조하세요.

host.json 파일에 [추가 설정](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)을 입력하여 저장소 큐 트리거를 더욱 세밀하게 조정할 수 있습니다.  

### <a name="handling-poison-queue-messages"></a>포이즌 큐 메시지 처리
큐 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 큐 메시지에 대해 기본적으로 최대 5번(첫 번째 시도 포함) 다시 시도합니다. 5번 모두 실패할 경우 함수는 메시지를 *&lt;originalqueuename>-poison*이라는 Storage 큐에 추가합니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다. 

포이즌 메시지를 수동으로 처리하려는 경우 `dequeueCount`을 확인하여 처리를 위해 메시지를 선택한 횟수를 가져올 수 있습니다([큐 트리거 메타데이터](#meta) 참조).

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>트리거 사용
C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 메시지를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [트리거 바인딩](#trigger)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 Blob 데이터에 액세스합니다.

큐 메시지를 다음 중 원하는 형식으로 역직렬화할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx) - JSON 직렬화 메시지에 유용합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `FooType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열
* 바이트 배열 
* `CloudQueueMessage` (C#) 

<a name="meta"></a>

### <a name="queue-trigger-metadata"></a>큐 트리거 메타데이터
이러한 변수 이름을 사용하여 함수에서 큐 메타데이터를 얻을 수 있습니다.

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger(큐 메시지 텍스트를 문자열로 검색하는 다른 방법)

[트리거 샘플](#triggersample)에서 큐 메타데이터를 사용하는 방법을 참조하세요.

<a name="triggersample"></a>

## <a name="trigger-sample"></a>트리거 샘플
Storage 큐 트리거를 정의하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"",
            "type": "queueTrigger",
            "direction": "in"
        }
    ]
}
```

큐 메타데이터를 검색하고 기록하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C에서 트리거 샘플# #
```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}\n" +
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
    context.log('Node.js queue trigger function processed work item' context.bindings.myQueueItem);
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

<a name="output"></a>

## <a name="storage-queue-output-binding"></a>Storage 큐 출력 바인딩
Azure Storage 큐 출력 바인딩을 사용하면 메시지를 함수의 Storage 큐에 쓸 수 있습니다. 

함수에 대한 Storage 큐 출력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
  "name": "<Name of output parameter in function signature>",
    "queueName": "<Name of queue to write to>",
    "connection":"<Name of app setting - see below>",
  "type": "queue",
  "direction": "out"
}
```

`connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 이 앱 설정을 수동으로 만들려면 [이 앱 설정을 수동으로 구성]()을 참조하세요.

<a name="outputusage"></a>

## <a name="output-usage"></a>출력 사용
C# 함수에서 `out <T> <name>`같은 함수 시그니처의 명명된 `out` 매개 변수를 사용하여 큐 메시지를 작성합니다. 여기서 `T`는 메시지를 직렬화하려는 데이터 형식이며, `paramName`은 [출력 바인딩](#output)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력에 액세스합니다.

코드의 데이터 형식 중 하나를 사용하여 큐 메시지를 출력할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화에 유용합니다.
  사용자 지정 출력 형식을 선언하는 경우(예: `out FooType paramName`), Azure Functions에서 개체를 JSON으로 직렬화하려고 시도합니다. 함수가 종료될 때 출력 매개 변수가 null이면 Functions 런타임은 큐 메시지를 null 개체로 만듭니다.
* 문자열 - (`out string paramName`)은 테스트 메시지에 유용합니다. Functions 런타임은 함수가 종료될 때 문자열 매개 변수가 null이 아닌 경우에만 메시지를 생성합니다.
* 바이트 배열 - (`out byte[]`) 
* `out CloudQueueMessage` - C#에만 해당 

C#에서 `ICollector<T>` 또는 `IAsyncCollector<T>`에 바인딩할 수 있으며, 여기서 `T`는 지원되는 형식 중 하나입니다.

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
[Storage 큐 트리거](functions-bindings-storage-queue.md), Storage Blob 입력 및 Storage Blob 출력을 정의하는 다음과 같은 function.json이 있다고 가정합니다.

큐 트리거를 사용하고 큐 메시지를 쓰는 저장소 큐 출력 바인딩에 대한 *function.json* 예제:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "queueName": "samples-workitems-out",
      "connection": "MyStorageConnection",
      "type": "queue",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

각 입력 큐 메시지에 대한 출력 큐 메시지를 작성하는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C에서 출력 샘플# #

```cs
public static void Run(string myQueueItem, out string myQueue, TraceWriter log)
{
    myQueue = myQueueItem + "(next step)";
}
```

또는 여러 메시지를 전송하려면 다음을 수행합니다.

```cs
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js에서 출력 샘플

```javascript
module.exports = function(context) {
    context.bindings.myQueue = context.bindings.myQueueItem + "(next step)";
    context.done();
};
```

또는 여러 메시지를 전송하려면 다음을 수행합니다.

```javascript
module.exports = function(context) {
    context.bindings.myQueue = [];

    context.bindings.myQueueItem.push("(step 1)");
    context.bindings.myQueueItem.push("(step 2)");
    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]




<!--HONumber=Nov16_HO3-->


