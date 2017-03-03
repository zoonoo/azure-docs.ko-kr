---
title: "Azure Functions의 트리거 및 바인딩 작업 | Microsoft Docs"
description: "Azure Functions에서 트리거 및 바인딩을 사용하여 코드 실행을 온라인 이벤트 및 클라우드 기반 서비스에 연결하는 방법을 알아봅니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: b7ea1e3a72a9dc6f4f9ca9d2d9d6f8c9b1564972
ms.openlocfilehash: 947f1f5e6d9bebe6708e6d29d3b71efc09573537
ms.lasthandoff: 01/30/2017


---

# <a name="learn-how-to-work-with-triggers-and-bindings-in-azure-functions"></a>Azure Functions의 트리거 및 바인딩 작업 방법 알아보기 
이 항목에서는 Azure Functions의 트리거 및 바인딩을 사용하여 코드를 다양한 트리거 및 Azure 서비스와 기타 클라우드 기반 서비스에 연결하는 방법을 보여 줍니다. 또한 몇 가지 고급 바인딩 기능과 모든 바인딩 형식에서 지원되는 구문을 제공합니다.  

특정 유형의 트리거나 바인딩 작업에 대한 자세한 내용은 다음 참조 항목 중 하나를 참조하세요.

| | | | |  
| --- | --- | --- | --- |  
| [HTTP/webhook](functions-bindings-http-webhook.md) | [타이머](functions-bindings-timer.md) | [모바일 앱](functions-bindings-mobile-apps.md) | [서비스 버스](functions-bindings-service-bus.md)  |  
| [DocumentDB](functions-bindings-documentdb.md) |  [Storage Blob](functions-bindings-storage-blob.md) | [Storage 큐](functions-bindings-storage-queue.md) |  [Storage 테이블](functions-bindings-storage-table.md) |  
| [이벤트 허브](functions-bindings-event-hubs.md) | [알림 허브](functions-bindings-notification-hubs.md) | [Twilio](functions-bindings-twilio.md) |   
| | | | |  

이러한 문서에서는 사용자가 [Azure Functions 개발자 참조](functions-reference.md) 및 [C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) 또는 [Node.js](functions-reference-node.md) 개발자 참조 문서를 읽었다고 가정합니다.

## <a name="overview"></a>개요
트리거는 사용자 지정 코드를 트리거하는 데 사용되는 이벤트 응답입니다. 이를 통해 Azure 플랫폼 또는 온-프레미스 전반에 걸쳐 이벤트에 응답할 수 있습니다. 바인딩은 필요한 트리거에 코드를 연결하는 데 사용되는 필수 메타데이터 또는 관련 입력이나 출력 데이터를 나타냅니다. 각 함수의 *function.json* 파일에는 모든 관련 바인딩이 포함됩니다. 함수가 갖는 입력 및 출력 바인딩 수에는 제한이 없습니다. 그러나, 각 함수에 대해 하나의 트리거 바인딩만 지원됩니다.  

Azure Function 앱과 통합할 수 있는 여러 바인딩에 대한 더 나은 정보를 얻으려면 아래 표를 참조하세요.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

트리거와 바인딩을 전반적으로 더 잘 이해하려면 일부 코드를 실행하여 Azure Storage 큐에 배치된 새 항목을 처리해 볼 수 있습니다. Azure Functions는 이를 지원하는 Azure 큐 트리거를 제공합니다. 큐를 모니터링하려면 다음 정보가 필요합니다.

* 큐가 있는 저장소 계정입니다.
* 큐 이름입니다.
* 코드에서 큐에 배치된 새 항목을 참조하는 데 사용하는 변수 이름입니다.  

Azure 함수에 대한 이 정보가 큐 트리거 바인딩에 포함됩니다. 다음은 큐 트리거 바인딩이 포함된 *function.json*의 예입니다. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ],
  "disabled": false
}
```

코드는 새 큐 항목의 처리 방법에 따라 서로 다른 유형의 출력을 보낼 수 있습니다. 예를 들어 Azure Storage 테이블에 새 레코드를 작성할 수 있습니다.  이를 위해 Azure Storage 테이블에 대한 출력 바인딩을 작성합니다. 다음은 큐 트리거에서 사용할 수 있는 저장소 테이블 출력 바인딩이 포함된 *function.json*의 예입니다. 

```json
{
  "bindings": [
    {
      "name": "myNewUserQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "queue-newusers",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "name": "myNewUserTableBinding",
      "tableName": "newUserTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

다음 C# 함수는 큐에 배치되는 새 항목에 응답하고 Azure Storage 테이블에 새 사용자 항목을 작성합니다.

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;

public static async Task Run(string myNewUserQueueItem, IAsyncCollector<Person> myNewUserTableBinding, 
                                TraceWriter log)
{
    // In this example the queue item is a JSON string representing an order that contains the name, 
    // address and mobile number of the new customer.
    dynamic order = JsonConvert.DeserializeObject(myNewUserQueueItem);

    await myNewUserTableBinding.AddAsync(
        new Person() { 
            PartitionKey = "Test", 
            RowKey = Guid.NewGuid().ToString(), 
            Name = order.name,
            Address = order.address,
            MobileNumber = order.mobileNumber }
        );
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string Address { get; set; }
    public string MobileNumber { get; set; }
}
```

지원되는 Azure Storage 유형의 추가 코드 예제 및 보다 구체적인 정보는 [Azure Storage에 대한 Azure Functions 트리거 및 바인딩](functions-bindings-storage.md)을 참조하세요.

Azure Portal에서 고급 바인딩 기능을 사용하려면 함수의 **통합** 탭에서 **고급 편집기** 옵션을 클릭합니다. 고급 편집기를 사용하면 포털에서 직접 *function.json*을 편집할 수 있습니다.

## <a name="random-guids"></a>임의 GUID
Azure Functions는 바인딩과 함께 임의 GUID를 생성하는 구문을 제공합니다. 다음 바인딩 구문은 Storage 컨테이너에서 고유한 이름의 새 BLOB에 대한 출력을 작성합니다. 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```


## <a name="returning-a-single-output"></a>단일 출력 반환
함수 코드가 단일 출력을 반환하는 경우 `$return` 출력 바인딩을 사용하여 코드에 보다 자연스러운 함수 서명을 유지할 수 있습니다. 이는 반환 값을 지원하는 언어(C#, Node.js, F#)에서만 사용할 수 있습니다. 바인딩은 큐 트리거에서 사용되는 다음 blob 출력 바인딩과 유사합니다.

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "name": "input",
      "direction": "in",
      "queueName": "test-input-node"
    },
    {
      "type": "blob",
      "name": "$return",
      "direction": "out",
      "path": "test-output-node/{id}"
    }
  ]
}
```

다음 C# 코드는 함수 서명에서 `out` 매개 변수를 사용하지 않고 출력을 보다 자연스럽게 반환합니다.

```cs
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

비동기 예제:

```cs
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```


다음과 같이 동일한 이 방법이 Node.js로 설명되어 있습니다.

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

다음은 F# 예입니다.

```fsharp
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

`$return`과 함께 단일 출력을 지정하여 여러 출력 매개 변수에서 사용할 수 있습니다.

## <a name="resolving-app-settings"></a>앱 설정 해결
앱 설정을 사용하여 중요한 정보를 런타임 환경의 일부로 저장하는 것이 좋습니다. 중요한 정보를 앱의 구성 파일에서 제외함으로써 공용 리포지토리가 앱 파일을 저장하는 데 사용되는 경우 노출을 제한합니다.  

앱 설정 이름을 백분율 기호로 묶으면(`%your app setting%`) Azure Functions 런타임이 값에 대한 앱 설정을 확인합니다. 다음 [Twilio 바인딩](functions-bindings-twilio.md)은 바인딩의 `from` 필드에 대해 `TWILIO_ACCT_PHONE`이라는 앱 설정을 사용합니다. 

```json
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received Node.js",
  "direction": "out"
},
```



## <a name="parameter-binding"></a>매개 변수 바인딩
출력 바인딩 속성에 대한 정적 구성 설정 대신, 트리거의 입력 바인딩에 속한 데이터에 동적으로 바인딩되도록 설정을 구성할 수 있습니다. Azure Storage 큐를 사용하여 새 주문을 처리하는 시나리오를 가정해 보겠습니다. 각 새 큐 항목은 최소한 다음 속성을 포함하는 JSON 문자열입니다.

```json
{
  "name" : "Customer Name",
  "address" : "Customer's Address",
  "mobileNumber" : "Customer's mobile number in the format - +1XXXYYYZZZZ."
}
```

Twilio 계정을 사용하여 고객에게 주문을 받았다는 업데이트로 SMS 문자 메시지를 보낼 수 있습니다.  다음과 같이 입력의 일부인 `name` 및 `mobileNumber`에 동적으로 바인딩되도록 Twilio 출력 바인딩의 `body` 및 `to` 필드를 구성할 수 있습니다.

```json
{
  "name": "myNewOrderItem",
  "type": "queueTrigger",
  "direction": "in",
  "queueName": "queue-newOrders",
  "connection": "orders_STORAGE"
},
{
  "type": "twilioSms",
  "name": "$return",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "{mobileNumber}",
  "from": "%TWILIO_ACCT_PHONE%",
  "body": "Thank you {name}, your order was received",
  "direction": "out"
},
```

이제 함수 코드는 다음과 같이 출력 매개 변수만 초기화해야 합니다. 실행하는 동안 출력 속성이 필요한 입력 데이터에 바인딩됩니다.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using System.Threading.Tasks;
using Newtonsoft.Json;
using Twilio;

public static async Task<SMSMessage> Run(string myNewOrderItem, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myNewOrderItem}");

    dynamic order = JsonConvert.DeserializeObject(myNewOrderItem);    

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // The following isn't needed since we use parameter binding for this
    //string msg = "Hello " + order.name + ", thank you for your order.";
    //smsText.Body = msg;
    //smsText.To = order.mobileNumber;

    return smsText;
}
```

Node.js:

```javascript
module.exports = function (context, myNewOrderItem) {    
    context.log('Node.js queue trigger function processed work item', myNewOrderItem);    

    // No need to set the properties of the text, we use parameters in the binding. We do need to 
    // initialize the object.
    var smsText = {};    

    context.done(null, smsText);
}
```

## <a name="advanced-binding-at-runtime-imperative-binding"></a>런타임에서 고급 바인딩(명령적 바인딩)

*function.json*을 사용하는 표준 입력 및 출력 바인딩 패턴은 [*선언적*](https://en.wikipedia.org/wiki/Declarative_programming) 바인딩이라고 하며, 여기서 바인딩은 JSON 선언에 의해 정의됩니다. 하지만 사용자는 [명령적](https://en.wikipedia.org/wiki/Imperative_programming) 바인딩을 사용할 수 있습니다. 이 패턴을 사용하면 함수 코드에서 지원되는 입력 및 출력 바인딩을 얼마든지 즉시 바인딩할 수 있습니다.
경로 또는 다른 입력 바인딩 계산이 함수에서 설계 시간이 아닌 런타임에서 실행되어야 하는 경우 명령적 바인딩이 필요할 수 있습니다. 

다음과 같이 명령적 바인딩을 정의합니다.

- 원하는 명령적 바인딩에 대한 *function.json*에 항목을 포함하지 **마세요**.
- 입력 매개 변수 [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) 또는 [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)에 전달합니다. 
- 다음 C# 패턴을 사용하여 데이터 바인딩을 수행합니다.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

여기서 `BindingTypeAttribute`는 바인딩을 정의하는 .NET 특성이며, `T`는 해당 바인딩 형식에서 지원되는 입력 또는 출력 형식입니다. 또한 `T`는 `out` 매개 변수 형식(예: `out JObject`)일 수 없습니다. 예를 들어, Mobile Apps 테이블 출력 바인딩은 [6가지 출력 형식](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)을 지원하지만 `T`에는 [ICollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) 또는 [IAsyncCollector<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)만 사용할 수 있습니다.
    
다음 예제 코드에서는 런타임에서 정의된 Blob경로를 사용하는 [Storage Blob 출력 바인딩](functions-bindings-storage-blob.md#storage-blob-output-binding)을 만든 다음, Blob에 문자열을 씁니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)는 [Storage Blob](functions-bindings-storage-blob.md) 입력 또는 출력 바인딩을 정의하며, [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)는 지원되는 출력 바인딩 형식입니다.
코드는 저장소 계정 연결 문자열(즉 `AzureWebJobsStorage`)에 대한 기본 앱 설정을 가져옵니다. [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)를 추가하고 `BindAsync<T>()`에 특성 배열을 전달하여 사용할 사용자 지정 앱 설정을 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {    
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

다음 표에는 각 바인딩 형식에 사용하는 해당 .NET 특성과 참조가 나와 있습니다.

> [!div class="mx-codeBreakAll"]
| 바인딩 | 특성 | 추가 참조 |
|------|------|------|
| DocumentDB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.DocumentDB/DocumentDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.DocumentDB"` |
| Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
| Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
| Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
| Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
| Storage 큐 | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage Blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Storage 테이블 | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
| Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |



## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [기능 테스트](functions-test-a-function.md)
* [기능 크기 조정](functions-scale.md)


