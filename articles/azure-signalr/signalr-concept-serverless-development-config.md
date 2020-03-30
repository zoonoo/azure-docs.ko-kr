---
title: Azure 함수 앱 구성 & 개발 - Azure SignalR
description: Azure Function및 Azure SignalR 서비스를 사용하여 서버없는 실시간 응용 프로그램을 개발하고 구성하는 방법에 대한 자세한 내용
author: anthonychu
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: antchu
ms.openlocfilehash: e1157a695d34c75b237391427b37365421366ef8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523173"
---
# <a name="azure-functions-development-and-configuration-with-azure-signalr-service"></a>Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성

Azure Functions 응용 프로그램은 [Azure SignalR 서비스 바인딩을](../azure-functions/functions-bindings-signalr-service.md) 활용하여 실시간 기능을 추가할 수 있습니다. 클라이언트 응용 프로그램은 여러 언어로 제공되는 클라이언트 SDK를 사용하여 Azure SignalR 서비스에 연결하고 실시간 메시지를 수신합니다.

이 문서에서는 SignalR 서비스와 통합된 Azure Function 앱을 개발하고 구성하기 위한 개념을 설명합니다.

## <a name="signalr-service-configuration"></a>시그널R 서비스 구성

Azure SignalR 서비스는 다양한 모드로 구성할 수 있습니다. Azure 함수와 함께 사용할 경우 서비스는 *서버리스* 모드에서 구성해야 합니다.

Azure 포털에서 SignalR 서비스 리소스의 *설정* 페이지를 찾습니다. 서비스 *모드를* *서버리스로 설정합니다.*

![SignalR Service 모드](media/signalr-concept-azure-functions/signalr-service-mode.png)

## <a name="azure-functions-development"></a>Azure Functions 개발

Azure SignalR Service 및 Azure Functions로 구축된 서버리스 실시간 애플리케이션에는 일반적으로 다음과 같은 두 가지 Azure Functions가 필요합니다.

* 클라이언트가 유효한 SignalR Service 액세스 토큰 및 서비스 엔드포인트 URL을 얻기 위해 호출하는 "협상" 함수
* 메시지를 보내거나 그룹 멤버 자격을 관리하는 하나 이상의 함수

### <a name="negotiate-function"></a>협상 함수

클라이언트 응용 프로그램에 Azure SignalR 서비스에 연결하려면 유효한 액세스 토큰이 필요합니다. 액세스 토큰은 익명이거나 지정된 사용자 ID로 인증될 수 있습니다. 서버리스 SignalR 서비스 응용 프로그램은 SignalR 서비스 엔드포인트 URL과 같은 토큰 및 기타 연결 정보를 얻기 위해 "협상"이라는 HTTP 끝점이 필요합니다.

HTTP 트리거Azure 함수 및 *SignalRConnectionInfo* 입력 바인딩을 사용하여 연결 정보 개체를 생성합니다. 함수에 종료되는 HTTP 경로가 `/negotiate`있어야 합니다.

협상 함수를 만드는 방법에 대한 자세한 내용은 [ *SignalRConnectionInfo* 입력 바인딩 참조](../azure-functions/functions-bindings-signalr-service-input.md)를 참조하십시오.

인증된 토큰을 만드는 방법에 대한 자세한 내용은 [앱 서비스 인증 사용을](#using-app-service-authentication)참조하십시오.

### <a name="sending-messages-and-managing-group-membership"></a>메시지 보내기 및 그룹 구성원 자격 관리

*SignalR* 출력 바인딩을 사용하여 Azure SignalR 서비스에 연결된 클라이언트에 메시지를 보냅니다. 모든 클라이언트에 메시지를 브로드캐스트하거나 특정 사용자 ID로 인증되었거나 특정 그룹에 추가된 클라이언트의 하위 집합으로 메시지를 보낼 수 있습니다.

사용자는 하나 이상의 그룹에 추가할 수 있습니다. *SignalR* 출력 바인딩을 사용하여 그룹을 받는/부터 사용자를 추가하거나 제거할 수도 있습니다.

자세한 내용은 [ *SignalR* 출력 바인딩 참조](../azure-functions/functions-bindings-signalr-service-output.md)를 참조하십시오.

### <a name="signalr-hubs"></a>시그널R 허브

SignalR에는 "허브"라는 개념이 있습니다. Azure Functions에서 보낸 각 클라이언트 연결 및 각 메시지는 특정 허브로 범위가 조정됩니다. 허브를 사용하여 연결및 메시지를 논리적 네임스페이스로 구분할 수 있습니다.

## <a name="client-development"></a>클라이언트 개발

SignalR 클라이언트 응용 프로그램은 여러 언어 중 하나에서 SignalR 클라이언트 SDK를 활용하여 Azure SignalR 서비스에서 메시지를 쉽게 연결하고 받을 수 있습니다.

### <a name="configuring-a-client-connection"></a>클라이언트 연결 구성

SignalR 서비스에 연결하려면 클라이언트가 다음 단계로 구성된 성공적인 연결 협상을 완료해야 합니다.

1. 유효한 연결 정보를 얻기 위해 위에서 설명한 HTTP 끝점에 대한 *요청*
1. *협상* 끝점에서 얻은 서비스 엔드포인트 URL 및 액세스 토큰을 사용하여 SignalR 서비스에 연결

SignalR 클라이언트 SDK에는 협상 핸드셰이크를 수행하는 데 필요한 논리가 이미 포함되어 있습니다. 세그먼트를 뺀 `negotiate` 협상 끝점의 URL을 SDK의 에 `HubConnectionBuilder`전달합니다. 다음은 자바스크립트의 예입니다.

```javascript
const connection = new signalR.HubConnectionBuilder()
  .withUrl('https://my-signalr-function-app.azurewebsites.net/api')
  .build()
```

규칙에 따라 SDK는 자동으로 `/negotiate` URL을 적용하고 이를 사용하여 협상을 시작합니다.

> [!NOTE]
> 브라우저에서 JavaScript/TypeScript SDK를 사용하는 경우 함수 앱에서 [CORS(원본 간 리소스 공유)를 사용하도록 설정해야](#enabling-cors) 합니다.

SignalR 클라이언트 SDK를 사용하는 방법에 대한 자세한 내용은 해당 언어에 대한 설명서를 참조하십시오.

* [.NET Standard](https://docs.microsoft.com/aspnet/core/signalr/dotnet-client)
* [자바 스크립트](https://docs.microsoft.com/aspnet/core/signalr/javascript-client)
* [Java](https://docs.microsoft.com/aspnet/core/signalr/java-client)

### <a name="sending-messages-from-a-client-to-the-service"></a>클라이언트에서 서비스로 메시지 보내기

SignalR SDK를 사용하면 클라이언트 응용 프로그램이 SignalR 허브에서 백 엔드 논리를 호출할 수 있지만 Azure Functions를 사용하여 SignalR 서비스를 사용할 때 이 기능은 아직 지원되지 않습니다. HTTP 요청을 사용하여 Azure 함수를 호출합니다.

## <a name="azure-functions-configuration"></a>Azure 함수 구성

Azure SignalR 서비스와 통합되는 Azure Function 앱은 [연속 배포,](../azure-functions/functions-continuous-deployment.md) [zip 배포](../azure-functions/deployment-zip-push.md)및 [패키지에서 실행과](../azure-functions/run-functions-from-deployment-package.md)같은 기술을 사용하여 일반적인 Azure Function 앱처럼 배포할 수 있습니다.

그러나 SignalR 서비스 바인딩을 사용 하는 애플 리 케이 션에 대 한 몇 가지 특별 한 고려 사항이 있습니다. 클라이언트가 브라우저에서 실행되는 경우 CORS를 사용하도록 설정해야 합니다. 앱에 인증이 필요한 경우 협상 끝점을 앱 서비스 인증과 통합할 수 있습니다.

### <a name="enabling-cors"></a>CORS 사용

JavaScript/TypeScript 클라이언트는 연결 협상을 시작하기 위해 협상 함수에 HTTP 요청을 합니다. 클라이언트 응용 프로그램이 Azure Function 앱이 아닌 다른 도메인에서 호스팅되는 경우 CORS(원본 간 리소스 공유)를 Function 앱에서 사용하도록 설정해야 하거나 브라우저에서 요청을 차단합니다.

#### <a name="localhost"></a>Localhost

로컬 컴퓨터에서 Function 앱을 실행할 때 `Host` *local.settings.json에* 섹션을 추가하여 CORS를 활성화할 수 있습니다. 섹션에서 `Host` 두 개의 속성을 추가합니다.

* `CORS`- 클라이언트 응용 프로그램의 원본인 기본 URL을 입력합니다.
* `CORSCredentials`- "자격 `true` 증명"요청을 허용하도록 설정

예제:

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

#### <a name="cloud---azure-functions-cors"></a>클라우드 - Azure 함수 CORS

Azure Function 앱에서 CORS를 사용하려면 Azure 포털에서 Function 앱의 *플랫폼 기능* 탭 아래의 CORS 구성 화면으로 이동합니다.

> [!NOTE]
> CORS 구성은 Azure Functions Linux 소비 계획에서 아직 사용할 수 없습니다. [Azure API 관리를](#cloud---azure-api-management) 사용하여 CORS를 사용하도록 설정합니다.

SignalR 클라이언트가 협상 함수를 호출하려면 액세스 제어 허용 자격 증명이 있는 CORS를 사용하도록 설정해야 합니다. 확인란을 선택하여 활성화합니다.

*허용된 원본* 섹션에서 웹 응용 프로그램의 원본 기본 URL이 있는 항목을 추가합니다.

![CORS 구성](media/signalr-concept-serverless-development-config/cors-settings.png)

#### <a name="cloud---azure-api-management"></a>클라우드 - Azure API 관리

Azure API Management는 기존 백 엔드 서비스에 기능을 추가하는 API 게이트웨이를 제공합니다. 함수 앱에 CORS를 추가하는 데 사용할 수 있습니다. 그것은 행동 당 지불 가격 및 월별 무료 보조금 소비 계층을 제공합니다.

[Azure Function 앱을 가져오는](../api-management/import-function-app-as-api.md)방법에 대한 자세한 내용은 API 관리 설명서를 참조하십시오. 가져온 후에는 인바운드 정책을 추가하여 액세스 제어 허용 자격 증명 지원을 사용하여 CORS를 활성화할 수 있습니다.

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

API 관리 URL을 사용하도록 SignalR 클라이언트를 구성합니다.

### <a name="using-app-service-authentication"></a>앱 서비스 인증 사용

Azure Functions에는 Facebook, Twitter, Microsoft 계정, Google 및 Azure Active Directory와 같은 인기 있는 공급자를 지원하는 기본 제공 인증이 있습니다. 이 기능은 *SignalRConnectionInfo* 바인딩과 통합하여 사용자 ID에 인증된 Azure SignalR 서비스에 대한 연결을 만들 수 있습니다. 응용 프로그램은 해당 사용자 ID를 대상으로 하는 *SignalR* 출력 바인딩을 사용하여 메시지를 보낼 수 있습니다.

Azure 포털에서 함수 앱의 *플랫폼 기능* 탭에서 *인증/권한 부여* 설정 창을 엽니다. 앱 서비스 [인증](../app-service/overview-authentication-authorization.md) 설명서를 따라 선택한 ID 공급자를 사용하여 인증을 구성합니다.

일단 구성되면 인증된 HTTP 요청에는 `x-ms-client-principal-id` 인증된 ID의 사용자 이름과 사용자 ID가 각각 포함된 헤더와 헤더가 포함됩니다. `x-ms-client-principal-name`

*SignalRConnectionInfo* 바인딩 구성에서 이러한 헤더를 사용하여 인증된 연결을 만들 수 있습니다. 다음은 헤더를 사용하는 C# 협상 `x-ms-client-principal-id` 함수의 예입니다.

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

그런 다음 SignalR 메시지의 `UserId` 속성을 설정하여 해당 사용자에게 메시지를 보낼 수 있습니다.

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

다른 언어에 대한 자세한 내용은 Azure Functions 참조에 대한 [Azure SignalR 서비스 바인딩을](../azure-functions/functions-bindings-signalr-service.md) 참조하십시오.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Functions를 사용하여 서버리스 SignalR 서비스 응용 프로그램을 개발하고 구성하는 방법을 배웠습니다. [SignalR 서비스 개요 페이지에서](index.yml)빠른 시작 또는 자습서 중 하나를 사용하여 응용 프로그램을 직접 만들어 보십시오.