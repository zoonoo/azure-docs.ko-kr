---
title: "Azure Functions SendGrid 바인딩 | Microsoft Docs"
description: "Azure Functions SendGrid 바인딩 참조"
services: functions
documentationcenter: na
author: rachelappel
manager: cfowler
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/26/2017
ms.author: rachelap
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: bcdbb6aee49d230a4cb0ba08d836facacb64de7f
ms.contentlocale: ko-kr
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions SendGrid 바인딩

이 문서에서는 Azure Functions에서 SendGrid 바인딩을 구성하고 사용하는 방법에 대해 설명합니다. SendGrid에서 Azure Functions를 사용하여 프로그래밍 방식으로 사용자 지정된 전자 메일을 보낼 수 있습니다.

이 문서는 Azure Functions 개발자를 위한 참조 정보입니다. Azure Functions를 처음 접하는 경우 다음 리소스부터 시작합니다.

[첫 번째 Azure Function 만들기](functions-create-first-azure-function.md) 
[C#](functions-reference-csharp.md), [F#](functions-reference-fsharp.md) 또는 [Node](functions-reference-node.md) 개발자 참조

## <a name="functionjson-for-sendgrid-bindings"></a>SendGrid 바인딩의 function.json

Azure Functions는 SendGrid에 대해 출력 바인딩을 제공합니다. SendGrid 출력 바인딩을 사용하면 프로그래밍 방식으로 전자 메일을 만들고 보낼 수 있습니다. 

SendGrid 바인딩에서 지원하는 속성은 다음과 같습니다.

|속성  |설명  |
|---------|---------|
|**name**| 필수 - 요청 또는 요청 본문의 함수 코드에 사용되는 변수 이름입니다. 반환 값이 하나만 있는 경우 이 값은 ```$return```입니다. |
|**type**| 필수 - `sendGrid`로 설정해야 합니다.|
|**direction**| 필수 - `out`으로 설정해야 합니다.|
|**apiKey**| 필수 - 함수 앱의 앱 설정에 저장된 API 키의 이름으로 설정해야 합니다. |
|**to**| 수신자의 전자 메일 주소입니다. |
|**from**| 발신자의 전자 메일 주소입니다. |
|**subject**| 메일의 제목입니다. |
|**text**| 전자 메일 내용입니다. |

**function.json**의 예제는 다음과 같습니다.

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Azure Functions는 사용자의 소스 제어 리포지토리를 확인하지 않도록 사용자 연결 정보 및 API 키를 앱 설정으로 저장합니다. 이 작업은 사용자의 중요한 정보를 보호합니다.
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>SendGrid 출력 바인딩의 C# 예제

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>SendGrid 출력 바인딩의 Node 예제

```javascript
module.exports = function (context, input) {    
    var message = {
         "personalizations": [ { "to": [ { "email": "sample@sample.com" } ] } ],
        from: { email: "sender@contoso.com" },        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>다음 단계
Azure Functions에 대한 다른 바인딩 및 트리거에 대한 정보는 다음을 참조하세요. 
- [Azure Functions 트리거 및 바인딩 개발자 참조](functions-triggers-bindings.md)

- [Azure Functions에 대한 모범 사례](functions-best-practices.md) Azure Functions를 만들 때 사용할 몇 가지 모범 사례를 나열합니다.

- [Azure Functions 개발자 참조](functions-reference.md) 함수를 코딩하고 트리거 및 바인딩을 정의하기 위한 프로그래머 참조입니다.

