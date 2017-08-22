---
title: "Azure Functions Twilio 바인딩 | Microsoft Docs"
description: "Azure Functions와 함께 Twilio 바인딩을 사용하는 방법을 배웁니다."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 2fd12dd32ed3c8479c7460cbc0a1cac3330ff4f4
ms.openlocfilehash: 9355aae6e3fbf70aae08cc829d7addd2decc44fd
ms.contentlocale: ko-kr
ms.lasthandoff: 03/01/2017

---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Twilio 출력 바인딩을 사용하여 Azure Functions에서 SMS 메시지 전송
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions와 함께 Twilio 바인딩을 구성하고 사용하는 방법을 설명합니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions는 함수에서 몇 줄의 코드와 [Twilio](https://www.twilio.com/) 계정으로 SMS 텍스트 메시지를 보낼 수 있도록 Twilio 출력 바인딩을 지원합니다. 

## <a name="functionjson-for-the-twilio-output-binding"></a>Twilio 출력 바인딩에 대한 function.json
function.json 파일은 다음 속성을 제공합니다.

* `name` : Twilio SMS 텍스트 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
* `type` : *"twilioSms"*로 설정해야 합니다.
* `accountSid` : 이 값은 Twilio 계정 Sid가 포함된 앱 설정의 이름으로 설정해야 합니다.
* `authToken` : 이 값은 Twilio 인증 토큰이 포함된 앱 설정의 이름으로 설정해야 합니다.
* `to` : 이 값은 SMS 텍스트 메시지가 전송된 전화 번호로 설정됩니다.
* `from` : 이 값은 SMS 텍스트 메시지를 보낸 전화 번호로 설정됩니다.
* `direction` : *"out"*으로 설정해야 합니다.
* `body` : 이 값은 함수에 대한 코드에서 동적으로 설정할 필요가 없는 경우에 SMS 텍스트 메시지를 하드 코딩하는 데 사용할 수 있습니다. 

예제 function.json:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Twilio 출력 바인딩이 포함된 C# 큐 트리거 예제
#### <a name="synchronous"></a>동기
Azure Storage 큐 트리거에 대한 이 동기 예제 코드는 out 매개 변수를 사용하여 제품을 주문한 고객에게 문자 메시지를 보냅니다.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>비동기
Azure Storage 큐 트리거에 대한 이 비동기 예제 코드는 제품을 주문한 고객에게 문자 메시지를 보냅니다.

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Twilio 출력 바인딩이 포함된 Node.js 큐 트리거 예제
이 Node.js 예제는 제품을 주문한 고객에게 문자 메시지를 보냅니다.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


