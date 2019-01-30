---
title: Azure Functions SignalR Service 바인딩
description: Azure Functions에서 SignalR Service 바인딩을 사용하는 방법을 배웁니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
editor: ''
tags: ''
keywords: Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: cshoe
ms.openlocfilehash: 7a7063b9177774c5207746283dc7cd25e3dd5793
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721889"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

이 문서에서는 Azure Functions에서 SignalR Service 바인딩을 사용하여 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/)에 연결된 클라이언트에 실시간 메시지를 인증하고 전송하는 방법을 설명합니다. Azure Functions는 SignalR Service에 대한 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

SignalR Service 바인딩은 [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet 패키지 버전 1.0.0-preview1-*에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub 리포지토리에 있습니다.

> [!NOTE]
> Azure SignalR Service는 일반적으로 사용할 수 있습니다. 그러나 Azure Functions의 SignalR Service 바인딩은 현재 미리 보기로 제공됩니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>SignalR 연결 정보 입력 바인딩

클라이언트를 Azure SignalR Service에 연결할 수 있기 전에 서비스 엔드포인트 URL 및 유효한 액세스 토큰을 검색해야 합니다. *SignalRConnectionInfo* 입력 바인딩은 서비스에 연결하는 데 사용되는 SignalR Service 엔드포인트 URL 및 유효한 토큰을 생성합니다. 토큰은 시간 제한적이고 연결에 대해 특정 사용자를 인증하는 데 사용될 수 있으므로 토큰을 캐시하거나 클라이언트 간에 공유해서는 안 됩니다. 이 바인딩을 사용하는 HTTP 트리거는 연결 정보를 검색하기 위해 클라이언트에서 사용할 수 있습니다.

언어 관련 예제를 참조하세요.

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>2.x C# 입력 예제

다음 예제에서는 입력 바인딩을 사용하여 SignalR 연결 정보를 획득하고 HTTP를 통해 해당 정보를 반환하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. [App Service 인증](../app-service/overview-authentication-authorization.md)을 사용하여 함수 앱에 인증을 쉽게 추가할 수 있습니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다. [바인딩 식](functions-triggers-bindings.md#binding-expressions-and-patterns)을 사용하여 바인딩의 `UserId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>2.x JavaScript 입력 예제

다음 예에서는 *function.json* 파일의 SignalR 연결 정보 입력 바인딩 및 바인딩을 사용하여 연결 정보를 반환하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. [App Service 인증](../app-service/overview-authentication-authorization.md)을 사용하여 함수 앱에 인증을 쉽게 추가할 수 있습니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다. [바인딩 식](functions-triggers-bindings.md#binding-expressions-and-patterns)을 사용하여 바인딩의 `userId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다. 

예제 function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>SignalR 출력 바인딩

Azure SignalR Service를 사용하여 하나 이상의 메시지를 보내려면 *SignalR* 출력 바인딩을 사용합니다. 연결된 모든 클라이언트에 메시지를 브로드캐스트할 수 있거나, 지정된 사용자에게 인증된 연결된 클라이언트에만 브로드캐스트할 수 있습니다.

언어 관련 예제를 참조하세요.

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>2.x C# 출력 예제

#### <a name="broadcast-to-all-clients"></a>모든 클라이언트에 브로드캐스트

다음 예제에서는 출력 바인딩을 사용하여 모든 연결된 클라이언트에 메시지를 전송하는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다. `Target`은 각 클라이언트에서 호출될 메서드의 이름입니다. `Arguments` 속성은 클라이언트 메서드에 전달될 0개 이상의 개체 배열입니다.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>사용자에게 보내기

SignalR 메시지의 `UserId` 속성을 설정하여 사용자에게 인증된 연결에만 메시지를 보낼 수 있습니다.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>2.x JavaScript 출력 예제

#### <a name="broadcast-to-all-clients"></a>모든 클라이언트에 브로드캐스트

다음 예제에서는 *function.json* 파일의 SignalR 출력 바인딩 및 Azure SignalR Service를 통해 메시지를 보내기 위해 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 출력 바인딩을 하나 이상의 SignalR 메시지 배열로 설정합니다. SignalR 메시지는 각 클라이언트에서 호출할 메서드의 이름을 지정하는 `target` 속성 및 클라이언트 메서드에 인수로 전달하기 위한 개체의 배열인 `arguments` 속성으로 구성됩니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>사용자에게 보내기

SignalR 메시지의 `userId` 속성을 설정하여 사용자에게 인증된 연결에만 메시지를 보낼 수 있습니다.

*function.json*은 동일하게 유지됩니다. JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>구성

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

다음 표에서는 *function.json* 파일 및 `SignalRConnectionInfo` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type**|| `signalRConnectionInfo`로 설정해야 합니다.|
|**direction**|| `in`로 설정해야 합니다.|
|**name**|| 연결 정보 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 연결 정보가 생성되는 SignalR 허브의 이름으로 설정되어야 합니다.|
|**userId**|**UserId**| 선택 사항: 액세스 키 토큰에서 설정할 사용자 식별자 클레임의 값입니다. |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

### <a name="signalr"></a>SignalR

다음 표에서는 *function.json* 파일 및 `SignalR` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type**|| `signalR`로 설정해야 합니다.|
|**direction**|| `out`로 설정해야 합니다.|
|**name**|| 연결 정보 개체에 대한 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 연결 정보가 생성되는 SignalR 허브의 이름으로 설정되어야 합니다.|
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)

