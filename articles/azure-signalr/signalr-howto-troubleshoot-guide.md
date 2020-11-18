---
title: Azure SignalR Service에 대한 문제 해결 가이드
description: 일반적인 문제를 해결 하는 방법 알아보기
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 11ea348a80bc226b6a96bea1e7c023ee9c06b13a
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684120"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Azure SignalR Service의 일반적인 문제에 대 한 문제 해결 가이드

이 지침은 지난 몇 년간 고객이 충족 하 고 해결 한 일반적인 문제에 따라 유용한 문제 해결 가이드를 제공 하는 것입니다.

## <a name="access-token-too-long"></a>액세스 토큰이 너무 깁니다.

### <a name="possible-errors"></a>가능한 오류:

* 클라이언트 쪽 `ERR_CONNECTION_`
* 414 URI가 너무 김
* 413 페이로드가 너무 큼
* 액세스 토큰은 4K 보다 길면 안 됩니다. 413 요청 엔터티가 너무 큼

### <a name="root-cause"></a>근본 원인:

HTTP/2의 경우 단일 헤더의 최대 길이는 **4 K** 이므로 브라우저를 사용 하 여 Azure 서비스에 액세스 하는 경우 `ERR_CONNECTION_` 에는이 제한에 대 한 오류가 발생 합니다.

HTTP/1.1 또는 c # 클라이언트의 경우 최대 URI 길이는 **12 k** 이 고 최대 헤더 길이는 **16k** 입니다.

SDK 버전 **1.0.6** 이상에서는 생성 된 `/negotiate` `413 Payload Too Large` 액세스 토큰이 **4 K** 보다 큰 경우이 throw 됩니다.

### <a name="solution"></a>해결 방법:

기본적으로에서 클레임 `context.User.Claims` 은 **asrs**(z) **S** ignal **R** **s** ervice **A**)로 JWT 액세스 토큰을 생성할 때 포함 되므로 클라이언트가에 연결할 때 클레임은 유지 되 고 **asrs** 에서로 전달 될 수 있습니다 `Hub` `Hub` .

일부 경우에는 `context.User.Claims` 앱 서버에 대 한 많은 정보를 저장 하는 데 사용 되며, 대부분은에서 사용 되지 않고 `Hub` 다른 구성 요소에서 사용 됩니다.

생성 된 액세스 토큰은 네트워크를 통해 전달 되 고 WebSocket/SSE 연결의 경우 액세스 토큰은 쿼리 문자열을 통해 전달 됩니다. 모범 사례에 따라 허브에 필요할 때 **Asrs** 를 통해 클라이언트에서 앱 서버로 **필요한** 클레임을 전달 하는 것이 좋습니다.

`ClaimsProvider`액세스 토큰 내에서 **asrs** 로 전달 되는 클레임을 사용자 지정할 수 있습니다.

ASP.NET Core:
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

ASP.NET의 경우:
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

## <a name="tls-12-required"></a>TLS 1.2 필요

### <a name="possible-errors"></a>가능한 오류:

* ASP.NET "서버를 사용할 수 없음" 오류 [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET "연결이 활성 상태가 아닙니다. 데이터를 서비스로 보낼 수 없습니다." 오류 [#324](https://github.com/Azure/azure-signalr/issues/324)
* "Https://에 대 한 HTTP 요청을 만드는 동안 오류가 발생 했습니다 <API endpoint> . 이 오류는 HTTPS 사례에서 서버 인증서가 HTTP.SYS를 사용 하 여 제대로 구성 되지 않았기 때문일 수 있습니다. 클라이언트와 서버 간의 보안 바인딩이 일치 하지 않기 때문에이 오류가 발생할 수도 있습니다. "

### <a name="root-cause"></a>근본 원인:

Azure 서비스는 보안 문제에 대 한 TLS 1.2만 지원 합니다. .NET framework에서는 TLS 1.2가 기본 프로토콜이 아닐 수 있습니다. 따라서 ASRS에 대 한 서버 연결을 설정할 수 없습니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. 이 오류가 로컬로 재현 될 수 있는 경우 *내 코드만* 를 선택 취소 하 고 모든 CLR 예외를 throw 하 고 앱 서버를 로컬로 디버그 하 여 예외를 throw 하는 내용을 확인 합니다.
    * 선택 취소 *내 코드만*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="선택 취소 내 코드만":::

    * CLR 예외 Throw

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="CLR 예외 Throw":::

    * 앱 서버 쪽 코드를 디버그할 때 throw 되는 예외를 참조 하세요.
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="예외 throw":::

2. ASP.NET에서 다음 코드를 추가 하 여 `Startup.cs` 자세한 추적을 사용 하도록 설정 하 고 로그에서 오류를 확인할 수도 있습니다.
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>해결 방법:

시작에 다음 코드를 추가 합니다.
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

## <a name="400-bad-request-returned-for-client-requests"></a>400 클라이언트 요청에 대해 잘못 된 요청이 반환 됨

### <a name="root-cause"></a>근본 원인

클라이언트 요청에 여러 `hub` 쿼리 문자열이 있는지 확인 합니다. `hub` 는 유지 되는 쿼리 매개 변수이 고, 서비스에서 쿼리에서 둘 이상의를 검색 하면 400가 throw 됩니다 `hub` .

## <a name="401-unauthorized-returned-for-client-requests"></a>401 권한이 없음 클라이언트 요청에 대해 반환됨

### <a name="root-cause"></a>근본 원인

현재 JWT 토큰 수명의 기본값은 1 시간입니다.

ASP.NET Core SignalR의 경우 WebSocket 전송 유형을 사용 하는 경우 정상입니다.

ASP.NET Core SignalR의 다른 전송 유형, SSE 및 긴 폴링의 경우 기본적으로 1 시간 동안 연결이 지속 될 수 있습니다.

ASP.NET SignalR의 경우 클라이언트는 `/ping` 시간에서 서비스에 KeepAlive 요청을 전송 합니다 `/ping` .이 실패 하면 클라이언트는 연결을 **중단** 하 고 다시 연결 하지 않습니다. 즉, ASP.NET SignalR의 경우 기본 토큰 수명은 모든 전송 형식에 대해 **최대** 1 시간 동안 연결이 지속 되도록 합니다.

### <a name="solution"></a>솔루션

보안 문제를 해결 하기 위해 TTL을 확장 하는 것은 권장 되지 않습니다. 이러한 401이 발생 하면 연결을 다시 시작 하기 위해 클라이언트에서 다시 연결 논리를 추가 하는 것이 좋습니다. 클라이언트는 연결을 다시 시작할 때 JWT 토큰을 다시 가져오고 갱신 된 토큰을 가져오기 위해 app server와 협상 합니다.

클라이언트 연결을 다시 시작 하는 방법은 [여기](#restart_connection) 를 확인 하세요.

## <a name="404-returned-for-client-requests"></a>404 클라이언트 요청에 대해 반환됨

SignalR 영구 연결의 경우 먼저 `/negotiate` Azure SignalR service로 이동한 다음 Azure SignalR service에 대 한 실제 연결을 설정 합니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

* 클라이언트에서 서비스로 요청을 가져오는 [보내는 요청을 보는 방법](#view_request)
* 404이 발생할 때 요청의 URL을 확인 합니다. URL이 웹 앱을 대상으로 하 고와 유사한 경우 `{your_web_app}/hubs/{hubName}` 클라이언트가 `SkipNegotiation` 인지 확인 `true` 합니다. Azure SignalR를 사용 하는 경우 클라이언트는 먼저 앱 서버와 협상 될 때 리디렉션 URL을 수신 합니다. Azure SignalR를 사용 하는 경우 클라이언트에서 협상 **을 건너뛰지 않아야 합니다.**
* 가 호출 된 후 connect 요청이 **5** 초 이상 처리 되는 경우 다른 404이 발생할 수 있습니다 `/negotiate` . 클라이언트 요청의 타임 스탬프를 확인 하 고 서비스 요청에 저속 응답이 있는 경우 문제를 엽니다.

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>404 ASP.NET SignalR의 다시 연결 요청에 대해 반환 됨

ASP.NET SignalR의 경우 [클라이언트 연결이 떨어지면](#client_connection_drop) `connectionId` 연결을 중지 하기 전에 3 번에 대해 동일한를 사용 하 여 다시 연결 합니다. `/reconnect``/reconnect`영구 연결을 성공적으로 다시 설정할 수 있는 네트워크 간헐적 문제로 인해 연결이 끊어진 경우에 도움이 될 수 있습니다. 다른 상황에서는 예를 들어 라우트된 서버 연결이 끊어졌거나 SignalR Service에 인스턴스 다시 시작/장애 조치/배포와 같은 일부 내부 오류가 있기 때문에 클라이언트 연결이 끊어졌거나, 연결이 더 이상 존재 하지 않으므로이 `/reconnect` 반환 `404` 됩니다. 이는 및에 대 한 예상 동작이 `/reconnect` 며, 다시 시도 하면 연결이 중지 됩니다. 연결이 중지 될 때 [연결 다시 시작](#restart_connection) 논리를 제안 하는 것이 좋습니다.

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (너무 많은 요청)이 클라이언트 요청에 대해 반환 됨

429는 **동시** 연결 수가 제한을 초과 하는 경우를 반환 합니다.

**무료** 인스턴스의 경우에는 **동시** 연결 수 제한이 **표준** 인스턴스의 경우 20이 고 **단위당** **동시** 연결 수 제한은 1 K입니다. 즉, Unit100에서 100-K 동시 연결을 허용 합니다.

연결에는 클라이언트 연결과 서버 연결이 모두 포함 됩니다. 연결 수를 계산 하는 방법은 [여기](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-messages-and-connections#how-connections-are-counted) 를 참조 하세요.

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500 협상 시 오류 발생: Azure SignalR 서비스가 아직 연결 되지 않았습니다. 나중에 다시 시도 하세요.

### <a name="root-cause"></a>근본 원인

이 오류는 Azure SignalR Service에 연결 된 서버 연결이 없을 때 보고 됩니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

서버 쪽 추적을 사용 하도록 설정 하 여 서버에서 Azure SignalR Service에 연결 하려고 할 때 오류 세부 정보를 확인 합니다.

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>ASP.NET Core SignalR에 대 한 서버 쪽 로깅 사용

ASP.NET Core SignalR에 대 한 서버 쪽 로깅은 `ILogger` ASP.NET Core framework에 제공 된 기반 [로깅과](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) 통합 됩니다. 다음과 같이 샘플 사용을 사용 하 여 서버 쪽 로깅을 사용 하도록 설정할 수 있습니다 `ConfigureLogging` .
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Azure SignalR에 대 한로 거 범주는 항상로 시작 `Microsoft.Azure.SignalR` 합니다. Azure SignalR에서 자세한 로그를 사용 하도록 설정 하려면 `Debug` 아래와 같이 파일 **의appsettings.js** 에서 이전 접두사를 수준으로 구성 합니다.
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>ASP.NET SignalR에 대 한 서버 쪽 추적 사용

SDK 버전 >=를 사용 하 `1.0.0` 는 경우 `web.config` ([세부 정보](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))에 다음을 추가 하 여 추적을 사용 하도록 설정할 수 있습니다.
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

## <a name="client-connection-drops"></a>클라이언트 연결 삭제

클라이언트가 Azure SignalR에 연결 된 경우 클라이언트와 Azure SignalR 간의 영구 연결이 다른 이유로 인해 삭제 될 수 있습니다. 이 섹션에서는 이러한 연결을 삭제할 수 있는 몇 가지 가능성에 대해 설명 하 고 근본 원인을 식별 하는 방법에 대 한 지침을 제공 합니다.

### <a name="possible-errors-seen-from-the-client-side"></a>클라이언트 쪽에서 볼 수 있는 오류

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>근본 원인:

클라이언트 연결은 다양 한 상황에서 삭제 될 수 있습니다.
* `Hub`에서 들어오는 요청과 함께 예외를 throw 하는 경우
* 클라이언트에서 라우팅되는 서버 연결의 경우 [서버 연결 삭제](#server_connection_drop)에 대 한 자세한 내용은 아래 섹션을 참조 하세요.
* 클라이언트와 SignalR 서비스 간에 네트워크 연결 문제가 발생 하는 경우
* SignalR Service에 인스턴스 다시 시작, 장애 조치 (failover), 배포 등의 일부 내부 오류가 있는 경우

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. 앱 서버 쪽 로그를 열어 비정상적인 작업이 발생 하는지 확인 합니다.
2. 앱 서버 쪽 이벤트 로그를 확인 하 여 앱 서버를 다시 시작 했는지 확인 합니다.
3. 시간 프레임을 제공 하는 문제를 만들고 리소스 이름을 전자 메일로 보내 주세요.


## <a name="client-connection-increases-constantly"></a>클라이언트 연결이 지속적으로 늘어납니다.

클라이언트 연결의 잘못 된 사용으로 인해 발생할 수 있습니다. 누군가가 SignalR client를 중지/삭제 하는 것을 잊은 경우 연결은 계속 열려 있습니다.

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>Azure Portal 리소스 메뉴의 모니터링 섹션에 있는 SignalR의 메트릭에 표시 되는 가능한 오류

클라이언트 연결은 Azure SignalR의 메트릭에 오랜 시간 동안 지속적으로 증가 합니다.

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="클라이언트 연결이 지속적으로 늘어납니다.":::

### <a name="root-cause"></a>근본 원인:

SignalR 클라이언트 연결 `DisposeAsync` 을 호출할 수 없습니다. 연결이 계속 열려 있습니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. SignalR **클라이언트가 닫히지** 않았는지 확인 합니다.

### <a name="solution"></a>솔루션

연결을 닫고 있는지 확인 합니다. `HubConnection.DisposeAsync()`를 사용 하 여 연결을 중지 하려면 수동으로를 호출 합니다.

예를 들어:

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>잘못 된 일반적인 클라이언트 연결 사용

#### <a name="azure-function-example"></a>Azure 함수 예제 

이 문제는 사용자가 함수 클래스에 대 한 정적 멤버로 설정 하는 대신 Azure Function 메서드에서 SignalR 클라이언트 연결을 설정 하는 경우에 종종 발생 합니다. 클라이언트 연결이 한 번만 설정 될 수 있지만 클라이언트 연결 수가 계속 해 서 Azure Portal 리소스 메뉴의 모니터링 섹션에 있는 메트릭에 표시 되는 것을 볼 수 있습니다. 이러한 모든 연결은 Azure Function 또는 Azure SignalR 서비스가 다시 시작 된 후에만 삭제 됩니다. 이는 **각** 요청에 대해 azure function이 **하나의** 클라이언트 연결을 만들기 때문입니다. 함수 메서드에서 클라이언트 연결을 중지 하지 않으면 클라이언트가 Azure SignalR service에 연결 된 상태를 유지 합니다.

#### <a name="solution"></a>솔루션

* Azure 함수에서 SignalR 클라이언트를 사용 하거나 SignalR client를 singleton으로 사용 하는 경우 클라이언트 연결을 닫아야 합니다.
* Azure function에서 SignalR 클라이언트를 사용 하는 대신, 다른 곳에서 SignalR 클라이언트를 만들고 [Azure SignalR Service에 대 한 Azure Functions 바인딩을](https://github.com/Azure/azure-functions-signalrservice-extension) 사용 하 여 클라이언트를 azure SignalR에 [협상할](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) 수 있습니다. 또한 바인딩을 활용 하 여 [메시지를 보낼](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40)수 있습니다. 클라이언트를 협상 하 고 메시지를 보내는 샘플은 [여기](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)에서 찾을 수 있습니다. 자세한 내용은 [여기](https://github.com/Azure/azure-functions-signalrservice-extension)를 참조 하세요.
* Azure function에서 SignalR 클라이언트를 사용 하는 경우 시나리오에 더 나은 아키텍처가 있을 수 있습니다. 서버를 사용 하지 않는 적절 한 아키텍처를 디자인 하는지 확인 합니다. [Azure Functions에서 SignalR 서비스 바인딩을 사용 하 여 실시간 서버 리스 응용 프로그램](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)을 참조할 수 있습니다.

<a name="server_connection_drop"></a>

## <a name="server-connection-drops"></a>서버 연결 삭제

앱 서버를 백그라운드에서 시작 하면 Azure SDK는 원격 Azure SignalR에 대 한 서버 연결 시작을 시작 합니다. [Azure SignalR Service의 내부](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)에 설명 된 대로 azure SignalR는 들어오는 클라이언트 트래픽을를 이러한 서버 연결로 라우팅합니다. 서버 연결을 삭제 하면 해당 연결을 사용 하는 모든 클라이언트 연결도 닫힙니다.

앱 서버와 SignalR 서비스 간의 연결은 영구 연결 이므로 네트워크 연결 문제가 발생할 수 있습니다. 서버 SDK에서 항상 서버 연결에 대 한 전략을 **다시 연결** 합니다. 모범 사례로, 사용자가 서버에 대 한 대량 동시 요청을 방지 하기 위해 임의 지연 시간을 사용 하 여 클라이언트에 연속 다시 연결 논리를 추가 하도록 권장 합니다.

정기적으로 Azure SignalR 서비스에 대 한 새로운 버전 릴리스가 있으며, 경우에 따라 Azure 전체 OS 패치 또는 업그레이드 또는 종속 서비스에서 때때로 중단 되는 경우가 있습니다. 이로 인해 서비스 중단 시간이 단축 될 수 있지만, 클라이언트 쪽에 연결 끊기/다시 연결 메커니즘이 있으면 클라이언트 쪽에서 연결 끊기/다시 연결에 따른 영향이 최소화 됩니다.

이 섹션에서는 서버 연결 삭제에 대 한 몇 가지 가능성을 설명 하 고 근본 원인을 식별 하는 방법에 대 한 몇 가지 지침을 제공 합니다.

### <a name="possible-errors-seen-from-server-side"></a>서버 쪽에서 볼 수 있는 오류:

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>근본 원인:

**Asrs**( **z\s** ignal **R** **s** ervice **)에 의해** 서버 서비스 연결이 닫혔습니다.

### <a name="troubleshooting-guide"></a>문제 해결 가이드

1. 앱 서버 쪽 로그를 열어 비정상적인 작업이 발생 하는지 확인 합니다.
2. 앱 서버 쪽 이벤트 로그를 확인 하 여 앱 서버를 다시 시작 했는지 확인 합니다.
3. 시간 프레임을 제공 하는 문제를 만들고 리소스 이름을 전자 메일로 보내 주세요.

## <a name="tips"></a>팁

<a name="view_request"></a>

* 클라이언트에서 보내는 요청을 보는 방법
예를 들어 ASP.NET Core (예: ASP.NET)를 사용 합니다.
    * 브라우저에서:

        Chrome 사용 예를 들어 **F12** 키를 사용 하 여 콘솔 창을 열고 **네트워크** 탭으로 전환할 수 있습니다. 처음부터 네트워크를 캡처하려면 **F5 키** 를 사용 하 여 페이지를 새로 고쳐야 할 수도 있습니다.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome 네트워크 보기":::

    * C # 클라이언트에서:

        [Fiddler](https://www.telerik.com/fiddler)를 사용 하 여 로컬 웹 트래픽을를 볼 수 있습니다. WebSocket 트래픽을는 Fiddler 4.5부터 지원 됩니다.

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="네트워크 보기 Fiddler" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* 클라이언트 연결을 다시 시작 하는 방법
    
    다음은 *항상 다시 시도* 전략을 사용 하 여 연결 논리를 다시 시작 하는 [샘플 코드](https://github.com/Azure/azure-signalr/tree/dev/samples) 입니다.

    * [ASP.NET Core c # 클라이언트](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [JavaScript 클라이언트 ASP.NET Core](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET c # 클라이언트](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript 클라이언트](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 일반적인 문제를 처리 하는 방법을 배웠습니다. 일반적인 문제 해결 방법에 대해 자세히 알아볼 수도 있습니다. 

> [!div class="nextstepaction"]
> [연결 및 메시지 배달 문제를 해결 하는 방법](./signalr-howto-troubleshoot-method.md)
