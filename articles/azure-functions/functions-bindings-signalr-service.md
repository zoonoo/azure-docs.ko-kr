---
title: Azure Functions SignalR Service 바인딩
description: Azure Functions에서 SignalR Service 바인딩을 사용하는 방법을 배웁니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
editor: ''
tags: ''
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 62d9319ae292c9f4ae22f8fcd83bdd8799dc6617
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480268"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

이 문서에서는 Azure Functions에서 SignalR Service 바인딩을 사용하여 [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/)에 연결된 클라이언트에 실시간 메시지를 인증하고 전송하는 방법을 설명합니다. Azure Functions는 SignalR Service에 대한 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

SignalR 서비스 바인딩은에서 제공 되는 [Microsoft.Azure.WebJobs.Extensions.SignalRService](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) NuGet 패키지를 버전 1. *. 이 패키지에 대한 소스 코드는 [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]


### <a name="java-annotations"></a>Java 주석

SignalR Service 주석을 Java 함수에서를 사용 하려면 종속성을 추가 해야 합니다 *azure-함수-java-라이브러리-signalr* 을 pom.xml에 아티팩트 (버전 1.0 이상).

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

> [!NOTE]
> Java에서 SignalR Service 바인딩을 사용하려면 Azure Functions Core Tools 버전 2.4.419 이상(호스트 버전 2.0.12332)을 사용하고 있는지 확인합니다.

## <a name="using-signalr-service-with-azure-functions"></a>Azure Functions를 사용 하 여 SignalR Service를 사용 하 여

구성 및 SignalR Service 및 Azure Functions를 함께 사용 하는 방법에 대 한 자세한 내용은 참조 [Azure Functions 개발 및 Azure SignalR Service를 사용 하 여 구성을](../azure-signalr/signalr-concept-serverless-development-config.md)합니다.

## <a name="signalr-connection-info-input-binding"></a>SignalR 연결 정보 입력 바인딩

클라이언트를 Azure SignalR Service에 연결할 수 있기 전에 서비스 엔드포인트 URL 및 유효한 액세스 토큰을 검색해야 합니다. *SignalRConnectionInfo* 입력 바인딩은 서비스에 연결하는 데 사용되는 SignalR Service 엔드포인트 URL 및 유효한 토큰을 생성합니다. 토큰은 시간 제한적이고 연결에 대해 특정 사용자를 인증하는 데 사용될 수 있으므로 토큰을 캐시하거나 클라이언트 간에 공유해서는 안 됩니다. 이 바인딩을 사용하는 HTTP 트리거는 연결 정보를 검색하기 위해 클라이언트에서 사용할 수 있습니다.

언어 관련 예제를 참조하세요.

* [2.x C#](#2x-c-input-examples)
* [2.x JavaScript](#2x-javascript-input-examples)
* [2.x Java](#2x-java-input-examples)

SignalR 클라이언트 SDK에서 사용할 수 있는 "negotiate" 함수를 만들려면이 바인딩을 사용 하는 방법에 대 한 자세한 내용은 참조는 [Azure Functions 개발 및 구성 문서](../azure-signalr/signalr-concept-serverless-development-config.md) SignalR Service 개념 설명서입니다.

### <a name="2x-c-input-examples"></a>2.x C# 예제를 입력 합니다.

다음 예제에서는 입력 바인딩을 사용하여 SignalR 연결 정보를 획득하고 HTTP를 통해 해당 정보를 반환하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req,
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. 사용 하 여 함수 앱에 인증을 쉽게 추가할 수 있습니다 [App Service 인증](../app-service/overview-authentication-authorization.md)합니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다. [바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `UserId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다. 

```cs
[FunctionName("negotiate")]
public static SignalRConnectionInfo Negotiate(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-examples"></a>2.x JavaScript 입력된 예제

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
module.exports = async function (context, req, connectionInfo) {
    context.res.body = connectionInfo;
};
```

#### <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. 사용 하 여 함수 앱에 인증을 쉽게 추가할 수 있습니다 [App Service 인증](../app-service/overview-authentication-authorization.md)합니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다. [바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `userId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다. 

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
module.exports = async function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier
    // claim set to the authenticated user
    context.res.body = connectionInfo;
};
```

### <a name="2x-java-input-examples"></a>2.x Java 입력된 예제

에서는 다음 예제는 [Java 함수](functions-reference-java.md) 입력된 바인딩을 사용 하 여 SignalR 연결 정보를 획득 하 고 HTTP를 통해 반환 합니다.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>인증된 토큰

인증된 클라이언트에서 함수를 트리거하는 경우 생성된 토큰에 사용자 ID 클레임을 추가할 수 있습니다. 사용 하 여 함수 앱에 인증을 쉽게 추가할 수 있습니다 [App Service 인증](../app-service/overview-authentication-authorization.md)합니다.

App Service 인증은 `x-ms-client-principal-id`라는 HTTP 헤더 및 인증된 사용자의 클라이언트 보안 주체 ID 및 이름 각각을 포함하는 `x-ms-client-principal-name`을 설정합니다. [바인딩 식](./functions-bindings-expressions-patterns.md)을 사용하여 바인딩의 `UserId` 속성을 `{headers.x-ms-client-principal-id}` 또는 `{headers.x-ms-client-principal-name}` 헤더 중 하나의 값으로 설정할 수 있습니다.

```java
@FunctionName("negotiate")
public SignalRConnectionInfo negotiate(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
        @SignalRConnectionInfoInput(
            name = "connectionInfo",
            hubName = "chat",
            userId = "{headers.x-ms-client-principal-id}") SignalRConnectionInfo connectionInfo) {
    return connectionInfo;
}
```

## <a name="signalr-output-binding"></a>SignalR 출력 바인딩

Azure SignalR Service를 사용하여 하나 이상의 메시지를 보내려면 *SignalR* 출력 바인딩을 사용합니다. 연결된 모든 클라이언트에 메시지를 브로드캐스트할 수 있거나, 지정된 사용자에게 인증된 연결된 클라이언트에만 브로드캐스트할 수 있습니다.

사용자가 속한 그룹을 관리 하 사용할 수 있습니다.

언어 관련 예제를 참조하세요.

* [2.x C#](#2x-c-send-message-output-examples)
* [2.x JavaScript](#2x-javascript-send-message-output-examples)
* [2.x Java](#2x-java-send-message-output-examples)

### <a name="2x-c-send-message-output-examples"></a>2.x C# 출력 예제 메시지 보내기

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
            // the message will only be sent to this user ID
            UserId = "userId1",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

#### <a name="send-to-a-group"></a>그룹에 보내기

설정 하 여 그룹에 추가 된 연결에만 메시지를 보낼 수는 `GroupName` SignalR 메시지의 속성입니다.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message,
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage
        {
            // the message will be sent to the group with this name
            GroupName = "myGroup",
            Target = "newMessage",
            Arguments = new [] { message }
        });
}
```

### <a name="2x-c-group-management-output-examples"></a>2.x C# 그룹 관리 예제 출력

SignalR Service를 사용 하면 그룹에 추가할 수 있습니다. 메시지는 다음 그룹에 보낼 수 있습니다. 사용할 수는 `SignalRGroupAction` 클래스는 `SignalR` 출력 바인딩 사용자의 그룹 멤버 자격을 관리 합니다.

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예제에서는 그룹에 사용자를 추가합니다.

```csharp
[FunctionName("addToGroup")]
public static Task AddToGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Add
        });
}
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예제에서는 그룹에서 사용자를 제거합니다.

```csharp
[FunctionName("removeFromGroup")]
public static Task RemoveFromGroup(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequest req,
    string userId,
    [SignalR(HubName = "chat")]
        IAsyncCollector<SignalRGroupAction> signalRGroupActions)
{
    return signalRGroupActions.AddAsync(
        new SignalRGroupAction
        {
            UserId = userId,
            GroupName = "myGroup",
            Action = GroupAction.Remove
        });
}
```

### <a name="2x-javascript-send-message-output-examples"></a>2.x JavaScript 송신 메시지 출력 예제

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
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-user"></a>사용자에게 보내기

SignalR 메시지의 `userId` 속성을 설정하여 사용자에게 인증된 연결에만 메시지를 보낼 수 있습니다.

*function.json*은 동일하게 유지됩니다. JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this user ID
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

#### <a name="send-to-a-group"></a>그룹에 보내기

설정 하 여 그룹에 추가 된 연결에만 메시지를 보낼 수는 `groupName` SignalR 메시지의 속성입니다.

*function.json*은 동일하게 유지됩니다. JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to this group
        "groupName": "myGroup",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
};
```

### <a name="2x-javascript-group-management-output-examples"></a>2.x JavaScript 그룹 관리 예제 출력

SignalR Service를 사용 하면 그룹에 추가할 수 있습니다. 메시지는 다음 그룹에 보낼 수 있습니다. 사용할 수는 `SignalR` 출력 바인딩 사용자의 그룹 멤버 자격을 관리 합니다.

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예제에서는 그룹에 사용자를 추가합니다.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "add"
  }];
};
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예제에서는 그룹에서 사용자를 제거합니다.

*function.json*

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "signalR",
      "name": "signalRGroupActions",
      "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
      "hubName": "chat",
      "direction": "out"
    }
  ]
}
```

*index.js*

```javascript
module.exports = async function (context, req) {
  context.bindings.signalRGroupActions = [{
    "userId": req.query.userId,
    "groupName": "myGroup",
    "action": "remove"
  }];
};
```

### <a name="2x-java-send-message-output-examples"></a>2.x Java 송신 메시지 출력 예제

#### <a name="broadcast-to-all-clients"></a>모든 클라이언트에 브로드캐스트

다음 예제는 [Java 함수](functions-reference-java.md) 모든 연결 된 클라이언트에 출력 바인딩을 사용 하 여 메시지를 보내는 합니다. `target`은 각 클라이언트에서 호출될 메서드의 이름입니다. `arguments` 속성은 클라이언트 메서드에 전달될 0개 이상의 개체 배열입니다.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-user"></a>사용자에게 보내기

SignalR 메시지의 `userId` 속성을 설정하여 사용자에게 인증된 연결에만 메시지를 보낼 수 있습니다.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.userId = "userId1";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

#### <a name="send-to-a-group"></a>그룹에 보내기

설정 하 여 그룹에 추가 된 연결에만 메시지를 보낼 수는 `groupName` SignalR 메시지의 속성입니다.

```java
@FunctionName("sendMessage")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRMessage sendMessage(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req) {

    SignalRMessage message = new SignalRMessage();
    message.groupName = "myGroup";
    message.target = "newMessage";
    message.arguments.add(req.getBody());
    return message;
}
```

### <a name="2x-java-group-management-output-examples"></a>2.x Java 그룹 관리 예제 출력

SignalR Service를 사용 하면 그룹에 추가할 수 있습니다. 메시지는 다음 그룹에 보낼 수 있습니다. 사용할 수는 `SignalRGroupAction` 클래스는 `SignalROutput` 출력 바인딩 사용자의 그룹 멤버 자격을 관리 합니다.

#### <a name="add-user-to-a-group"></a>그룹에 사용자 추가

다음 예제에서는 그룹에 사용자를 추가합니다.

```java
@FunctionName("addToGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction addToGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "add";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
```

#### <a name="remove-user-from-a-group"></a>그룹에서 사용자 제거

다음 예제에서는 그룹에서 사용자를 제거합니다.

```java
@FunctionName("removeFromGroup")
@SignalROutput(name = "$return", hubName = "chat")
public SignalRGroupAction removeFromGroup(
        @HttpTrigger(
            name = "req",
            methods = { HttpMethod.POST },
            authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Object> req,
        @BindingName("userId") String userId) {

    SignalRGroupAction groupAction = new SignalRGroupAction();
    groupAction.action = "remove";
    groupAction.userId = userId;
    groupAction.groupName = "myGroup";
    return action;
}
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

> [!div class="nextstepaction"]
> [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](../azure-signalr/signalr-concept-serverless-development-config.md)
