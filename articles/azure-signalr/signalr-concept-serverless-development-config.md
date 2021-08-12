---
title: Azure Functions 앱 개발 및 구성 - Azure SignalR
description: Azure Functions 및 Azure SignalR Service를 사용하여 서버리스 실시간 애플리케이션을 개발하고 구성하는 방법에 관한 세부 정보
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 3d69b72012819e3d9099e447b9048fe07aea86d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97858708"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성

Azure Functions 애플리케이션은 [Azure SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md)을 이용하여 실시간 기능을 추가할 수 있습니다. 클라이언트 애플리케이션은 여러 언어로 제공되는 클라이언트 SDK를 사용하여 Azure SignalR Service에 연결하고 실시간 메시지를 수신합니다.

이 문서에서는 SignalR Service와 통합된 Azure 함수 앱을 개발하고 구성하는 개념을 설명합니다.

## <a name="signalr-service-configuration"></a>SignalR Service 구성

Azure SignalR Service는 다양한 모드에서 구성할 수 있습니다. Azure Functions와 함께 사용하는 경우 서비스는 ‘서버리스’ 모드로 구성해야 합니다.

Azure Portal에서 SignalR Service 리소스의 ‘설정’ 페이지를 찾습니다. ‘서비스 모드’를 ‘서버리스’로 설정합니다. 

![SignalR Service 모드](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 개발

Azure SignalR Service 및 Azure Functions로 구축된 서버리스 실시간 애플리케이션에는 일반적으로 다음과 같은 두 가지 Azure Functions가 필요합니다.

* 클라이언트가 유효한 SignalR Service 액세스 토큰 및 서비스 엔드포인트 URL을 얻기 위해 호출하는 "협상" 함수
* SignalR Service에서 메시지를 처리하고 메시지를 보내거나 그룹 멤버 자격을 관리하는 하나 이상의 함수

### <a name="negotiate-function"></a>negotiate 함수

클라이언트 애플리케이션에는 Azure SignalR Service에 연결하기 위해 유효한 액세스 토큰이 필요합니다. 액세스 토큰은 익명이거나 지정된 사용자 ID에 인증될 수 있습니다. 서버리스 SignalR Service 애플리케이션에는 토큰 및 기타 연결 정보(예: SignalR Service 엔드포인트 URL)를 얻기 위해 “negotiate”라는 HTTP 엔드포인트가 필요합니다.

HTTP 트리거 Azure 함수 및 *SignalRConnectionInfo* 입력 바인딩을 사용하여 연결 정보 개체를 생성합니다. 함수는 `/negotiate`로 끝나는 HTTP 경로를 포함해야 합니다.

C#의 [클래스 기반 모델](#class-based-model)을 사용하면 *SignalRConnectionInfo* 입력 바인딩이 필요하지 않으며 사용자 지정 클레임을 훨씬 더 쉽게 추가할 수 있습니다. [클래스 기반 모델에서 환경 협상](#negotiate-experience-in-class-based-model)을 참조하세요.

negotiate 함수를 만드는 방법에 관한 자세한 내용은 [*SignalRConnectionInfo* 입력 바인딩 참조](../azure-functions/functions-bindings-signalr-service-input.md)를 참조하세요.

인증된 토큰을 만드는 방법에 관한 자세한 내용은 [App Service 인증 사용](#using-app-service-authentication)을 참조하세요.

### <a name="handle-messages-sent-from-signalr-service"></a>SignalR Service에서 보낸 메시지 처리

‘SignalR 트리거’ 바인딩을 사용하여 SignalR Service에서 보낸 메시지를 처리합니다. 클라이언트가 메시지를 보내거나 클라이언트가 연결되거나 연결이 끊어지면 알림을 받을 수 있습니다.

자세한 내용은 [‘SignalR 트리거’ 바인딩 참조](../azure-functions/functions-bindings-signalr-service-trigger.md)를 참조하세요.

또한 클라이언트의 메시지가 있을 때 서비스가 함수를 트리거하도록 함수 엔드포인트를 업스트림으로 구성해야 합니다. 업스트림을 구성하는 방법에 관한 자세한 내용은 이 [문서](concept-upstream.md)를 참조하세요.

### <a name="sending-messages-and-managing-group-membership"></a>메시지 보내기 및 그룹 멤버 자격 관리

*SignalR* 출력 바인딩을 사용하여 Azure SignalR Service에 연결된 클라이언트에 메시지를 보냅니다. 모든 클라이언트에 메시지를 브로드캐스트할 수 있거나, 특정 사용자 ID로 인증되거나 특정 그룹에 추가된 클라이언트의 하위 집합에 메시지를 보낼 수 있습니다.

사용자를 하나 이상의 그룹에 추가할 수 있습니다. *SignalR* 출력 바인딩을 사용하여 그룹에서 사용자를 추가하거나 제거할 수도 있습니다.

자세한 내용은 [*SignalR* 출력 바인딩 참조](../azure-functions/functions-bindings-signalr-service-output.md)를 참조하세요.

### <a name="signalr-hubs"></a>SignalR 허브

SignalR에는 “허브”라는 개념이 있습니다. 각 클라이언트 연결과 Azure Functions 보낸 각 메시지의 범위는 특정 허브로 지정됩니다. 허브를 사용하여 연결과 메시지를 논리적 네임스페이스로 구분할 수 있습니다.

## <a name="class-based-model"></a>클래스 기반 모델

클래스 기반 모델은 C# 전용입니다. 클래스 기반 모델에서는 일관된 SignalR 서버 쪽 프로그래밍 환경을 사용할 수 있습니다. 제공되는 기능은 다음과 같습니다.

* 더 적은 구성 작업: 클래스 이름은 `HubName`으로 사용되고, 메서드 이름은 `Event`로 사용되고, `Category`는 메서드 이름에 따라 자동으로 결정됩니다.
* 자동 매개 변수 바인딩: `ParameterNames` 및 `[SignalRParameter]` 특성이 둘 다 필요하지 않습니다. 매개 변수는 Azure 함수 메서드의 인수에 순서대로 자동 바인딩됩니다.
* 편리한 출력 및 환경 협상.

다음 코드는 이러한 기능을 보여 줍니다.

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("negotiate")]
    public SignalRConnectionInfo Negotiate([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req)
    {
        return Negotiate(req.Headers["x-ms-signalr-user-id"], GetClaims(req.Headers["Authorization"]));
    }

    [FunctionName(nameof(OnConnected))]
    public async Task OnConnected([SignalRTrigger]InvocationContext invocationContext, ILogger logger)
    {
        await Clients.All.SendAsync(NewConnectionTarget, new NewConnection(invocationContext.ConnectionId));
        logger.LogInformation($"{invocationContext.ConnectionId} has connected");
    }

    [FunctionName(nameof(Broadcast))]
    public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        await Clients.All.SendAsync(NewMessageTarget, new NewMessage(invocationContext, message));
        logger.LogInformation($"{invocationContext.ConnectionId} broadcast {message}");
    }

    [FunctionName(nameof(OnDisconnected))]
    public void OnDisconnected([SignalRTrigger]InvocationContext invocationContext)
    {
    }
}
```

클래스 기반 모델을 사용하려는 모든 함수는 **ServerlessHub** 에서 상속되는 클래스의 메서드여야 합니다. 샘플의 클래스 이름 `SignalRTestHub`는 허브 이름입니다.

### <a name="define-hub-method"></a>허브 메서드 정의

모든 허브 메서드는 `[SignalRTrigger]` 특성으로 데코레이트된 `InvocationContext`의 인수를 사용하고 매개 변수가 없는 생성자를 사용 **해야 합니다**. 그런 다음, **메서드 이름** 이 매개 변수 **event** 로 처리됩니다.

기본적으로 메서드 이름을 제외한 `category=messages`는 다음 이름 중 하나입니다.

* **OnConnected**: `category=connections, event=connected`로 처리됨
* **OnDisconnected**: `category=connections, event=disconnected`로 처리됨

### <a name="parameter-binding-experience"></a>매개 변수 바인딩 환경

클래스 기반 모델에서는 다음 상황 중 하나인 경우를 제외하고 기본적으로 모든 인수가 `[SignalRParameter]`로 표시되므로 `[SignalRParameter]`가 필요하지 않습니다.

* 인수가 바인딩 특성으로 데코레이트됩니다.
* 인수 형식이 `ILogger` 또는 `CancellationToken`입니다.
* 인수가 `[SignalRIgnore]` 특성으로 데코레이트됩니다.

### <a name="negotiate-experience-in-class-based-model"></a>클래스 기반 모델에서 환경 협상

SignalR 입력 바인딩 `[SignalR]`을 사용하는 것보다는 클래스 기반 모델의 협상이 더 유연할 수 있습니다. 기본 클래스 `ServerlessHub`에 메서드가 있습니다.

```cs
SignalRConnectionInfo Negotiate(string userId = null, IList<Claim> claims = null, TimeSpan? lifeTime = null)
```

이 기능 사용자는 함수 실행 중에 `userId`또는 `claims`를 사용자 지정합니다.

## <a name="use-signalrfilterattribute"></a>`SignalRFilterAttribute` 사용

사용자는 추상 클래스 `SignalRFilterAttribute`를 상속하고 구현할 수 있습니다. `FilterAsync`에서 예외가 throw되면 `403 Forbidden`이 클라이언트에 다시 전송됩니다.

다음 샘플에서는 `admin`만 `broadcast`를 호출하도록 허용하는 고객 필터를 구현하는 방법을 보여 줍니다.

```cs
[AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
internal class FunctionAuthorizeAttribute: SignalRFilterAttribute
{
    private const string AdminKey = "admin";

    public override Task FilterAsync(InvocationContext invocationContext, CancellationToken cancellationToken)
    {
        if (invocationContext.Claims.TryGetValue(AdminKey, out var value) &&
            bool.TryParse(value, out var isAdmin) &&
            isAdmin)
        {
            return Task.CompletedTask;
        }

        throw new Exception($"{invocationContext.ConnectionId} doesn't have admin role");
    }
}
```

특성을 이용하여 함수에 권한을 부여합니다.

```cs
[FunctionAuthorize]
[FunctionName(nameof(Broadcast))]
public async Task Broadcast([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
{
}
```

## <a name="client-development"></a>클라이언트 개발

SignalR 클라이언트 애플리케이션은 여러 언어 중 하나의 SignalR 클라이언트 SDK를 이용하여 손쉽게 Azure SignalR Service에 연결하고 해당 서비스의 메시지를 수신할 수 있습니다.

### <a name="configuring-a-client-connection"></a>클라이언트 연결 구성

SignalR Service에 연결하려면 클라이언트가 다음 단계로 구성된 성공적인 연결 협상을 완료해야 합니다.

1. 위에 설명된 *negotiate* HTTP 엔드포인트에 요청하여 유효한 연결 정보를 얻습니다.
1. 서비스 엔드포인트 URL 및 *negotiate* 엔드포인트에서 가져온 액세스 토큰을 사용하여 SignalR Service에 연결

SignalR 클라이언트 SDK에는 협상 핸드셰이크를 수행하는 데 필요한 논리가 포함되어 있습니다. negotiate 엔드포인트의 URL에서 `negotiate` 세그먼트를 뺀 값을 SDK의 `HubConnectionBuilder`에 전달합니다. 다음은 JavaScript의 예제입니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

규칙에 따라 SDK는 자동으로 `/negotiate`를 URL에 추가하고 이를 사용하여 협상을 시작합니다.

> [!NOTE]
> 브라우저에서 JavaScript/TypeScript SDK를 사용하는 경우 함수 앱에서 [CORS(원본 간 리소스 공유)를 사용하도록 설정](#enabling-cors)해야 합니다.

SignalR 클라이언트 SDK를 사용하는 방법에 관한 자세한 내용은 해당 언어의 설명서를 참조하세요.

* [.NET Standard](/aspnet/core/signalr/dotnet-client)
* [JavaScript](/aspnet/core/signalr/javascript-client)
* [Java](/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>클라이언트에서 서비스로 메시지 보내기

SignalR 리소스에 대해 [업스트림](concept-upstream.md)을 구성한 경우 SignalR 클라이언트를 사용하여 클라이언트에서 Azure Functions로 메시지를 보낼 수 있습니다. 다음은 JavaScript의 예제입니다.

```javascript
connection.send('method1', 'arg1', 'arg2');
```

## <a name="azure-functions-configuration"></a>Azure Functions 구성

Azure SignalR Service와 통합되는 Azure 함수 앱은 [지속적인 배포](../azure-functions/functions-continuous-deployment.md), [zip 배포](../azure-functions/deployment-zip-push.md) 및 [패키지에서 실행](../azure-functions/run-functions-from-deployment-package.md)과 같은 기술을 사용하여 일반적인 Azure 함수 앱처럼 배포할 수 있습니다.

그러나 SignalR Service 바인딩을 사용하는 앱에 대한 몇 가지 특별한 고려 사항이 있습니다. 클라이언트가 브라우저에서 실행되는 경우 CORS를 사용하도록 설정해야 합니다. 또한 앱에 인증이 필요한 경우 negotiate 엔드포인트를 App Service 인증과 통합할 수 있습니다.

### <a name="enabling-cors"></a>CORS 사용

JavaScript/TypeScript 클라이언트는 negotiate 함수에 대한 HTTP 요청을 만들어 연결 협상을 시작합니다. 클라이언트 애플리케이션이 Azure 함수 앱과 다른 도메인에서 호스트되는 경우 함수 앱에서 CORS(원본 간 리소스 공유)를 사용하도록 설정해야 합니다. 그러지 않으면 브라우저에서 요청을 차단합니다.

#### <a name="localhost"></a>Localhost

로컬 컴퓨터에서 함수 앱을 실행하는 경우 *local.settings.json* 에 `Host` 섹션을 추가하여 CORS를 사용하도록 설정할 수 있습니다. `Host` 섹션에서 다음 두 개의 속성을 추가합니다.

* `CORS` - 클라이언트 애플리케이션의 원본인 기준 URL을 입력합니다.
* `CORSCredentials` - `true`로 설정하여 “withCredentials” 요청을 허용합니다.

예:

```json
{
  "IsEncrypted": false,
  "Values": {
    // values
  },
  "Host": {
    "CORS": "http://localhost:8080",
    "CORSCredentials": true
  }
}
```

#### <a name="cloud---azure-functions-cors"></a>클라우드 - Azure Functions CORS

Azure 함수 앱에서 CORS를 사용하려면 Azure Portal에서 함수 앱의 ‘플랫폼 기능’ 탭 아래 CORS 구성 화면으로 이동합니다.

> [!NOTE]
> CORS 구성은 Azure Functions Linux 사용 플랜에서 아직 사용할 수 없습니다. [Azure API Management](#cloud---azure-api-management)를 사용하여 CORS를 사용하도록 설정합니다.

SignalR 클라이언트가 negotiate 함수를 호출하려면 Access-Control-Allow-Credentials를 사용하여 CORS를 사용하도록 설정해야 합니다. 확인란을 선택하여 사용하도록 설정합니다.

‘허용된 원본’ 섹션에서 웹 애플리케이션의 원본 기준 URL을 사용하여 항목을 추가합니다.

![CORS 구성](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>클라우드 - Azure API Management

Azure API Management는 기존 백 엔드 서비스에 기능을 추가하는 API 게이트웨이를 제공합니다. API 게이트웨이를 사용하여 함수 앱에 CORS를 추가할 수 있습니다. 작업 단위 요금 가격 책정 및 월간 무료 제공이 있는 소비 계층을 제공합니다.

[Azure 함수 앱을 가져오는](../api-management/import-function-app-as-api.md) 방법에 관한 정보는 API Management 설명서를 참조하세요. 가져온 후에는 인바운드 정책을 추가하여 Access-Control-Allow-Credentials 지원을 통해 CORS를 사용하도록 설정할 수 있습니다.

```xml
<cors allow-credentials="true">
  <allowed-origins>
    <origin>https://azure-samples.github.io</origin>
  </allowed-origins>
  <allowed-methods>
    <method>GET</method>
    <method>POST</method>
  </allowed-methods>
  <allowed-headers>
    <header>*</header>
  </allowed-headers>
  <expose-headers>
    <header>*</header>
  </expose-headers>
</cors>
```

API Management URL을 사용하도록 SignalR 클라이언트를 구성합니다.

### <a name="using-app-service-authentication"></a>App Service 인증 사용

Azure Functions에는 Facebook, Twitter, Microsoft 계정, Google, Azure Active Directory 등 인기 있는 공급자를 지원하는 기본 제공 인증이 있습니다. 이 기능은 *SignalRConnectionInfo* 바인딩과 통합하여 사용자 ID에 인증된 Azure SignalR Service에 대한 연결을 만들 수 있습니다. 애플리케이션은 해당 사용자 ID를 대상으로 하는 *SignalR* 출력 바인딩을 사용하여 메시지를 보낼 수 있습니다.

Azure Portal에 있는 함수 앱의 ‘플랫폼 기능’ 탭에서 ‘인증/권한 부여’ 설정 창을 엽니다.  [App Service 인증](../app-service/overview-authentication-authorization.md) 설명서에 따라 선택한 ID 공급자를 사용하여 인증을 구성합니다.

구성된 후 인증된 HTTP 요청에는 각각 인증된 ID의 사용자 이름과 사용자 ID가 들어 있는 `x-ms-client-principal-name` 및 `x-ms-client-principal-id` 헤더가 포함됩니다.

*SignalRConnectionInfo* 바인딩 구성에서 이 헤더를 사용하여 인증된 연결을 만들 수 있습니다. 다음은 `x-ms-client-principal-id` 헤더를 사용하는 예제 C# negotiate 함수입니다.

```csharp
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

그런 다음, SignalR 메시지의 `UserId` 속성을 설정하여 해당 사용자에게 메시지를 보낼 수 있습니다.

```csharp
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

다른 언어에 관한 정보는 Azure Functions 참조의 [Azure SignalR Service 바인딩](../azure-functions/functions-bindings-signalr-service.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Functions를 사용하여 서버리스 SignalR Service 애플리케이션을 개발하고 구성하는 방법을 알아보았습니다. [SignalR Service 개요 페이지](index.yml)의 빠른 시작 또는 자습서 중 하나를 사용하여 애플리케이션을 직접 만들어 봅니다.
