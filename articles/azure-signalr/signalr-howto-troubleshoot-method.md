---
title: Azure SignalR Service에 대 한 문제 해결 방법
description: 연결 및 메시지 배달 문제를 해결 하는 방법을 알아봅니다.
author: YanJin
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 4b0b85b08c3f813440d556c61ba5e290ac200049
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686916"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>연결 및 메시지 배달 문제를 해결 하는 방법

이 지침에서는 자체 진단을 수행 하 여 근본 원인을 직접 찾거나 문제의 범위를 좁히는 데 도움이 되는 여러 가지 방법을 소개 합니다. 자체 진단 결과는 추가 조사를 위해 microsoft에 보고할 때에도 유용 합니다.

먼저, Azure SignalR Service ( **Asrs** 라고도 함)가로 구성 된 [servicemode](https://docs.microsoft.com/azure/azure-signalr/concept-service-mode) 가 Azure Portal에서 확인 해야 합니다.

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* `Default`모드의 경우 [기본 모드 문제 해결](#default_mode_tsg) 을 참조 하세요.

* 모드의 경우 `Serverless` 서버 리스 [모드 문제 해결](#serverless_mode_tsg) 을 참조 하세요.

* `Classic`모드의 경우 [클래식 모드 문제 해결](#classic_mode_tsg) 을 참조 하세요.

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>기본 모드 문제 해결

**Asrs** 가 *기본* 모드인 경우 *클라이언트*, *서버* 및 *서비스* 의 **세** 가지 역할이 있습니다.

* *클라이언트*: *클라이언트* 는 **asrs** 에 연결 된 클라이언트를 나타냅니다. 클라이언트와 **Asrs** 를 연결 하는 영구 연결을이 지침의 *클라이언트 연결* 이라고 합니다.

* *서버*: *서버* 는 클라이언트 협상을 수행 하 고 SignalR 논리를 호스트 하는 서버를 나타냅니다 `Hub` . 그리고 *서버* 와 **asrs** 간의 영구 연결을이 지침에서 *서버 연결* 이라고 합니다.

* *Service*: *service* 는이 지침에서 **asrs** 의 약식 이름입니다.

전체 아키텍처 및 워크플로의 자세한 소개는 [Azure SignalR Service의 내부](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) 정보를 참조 하세요.

문제의 범위를 좁히는 데 도움이 되는 여러 가지 방법이 있습니다. 

* 문제가 즉시 발생 하거나 재현 가능 하 게 되 면 바로 앞으로 진행 되는 트래픽을 확인 하는 것입니다. 

* 문제가 재현 하기 어려운 경우 추적 및 로그가 도움이 될 수 있습니다.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>트래픽을 확인 하 고 문제의 범위를 좁히는 방법

진행 중인 트래픽을 캡처하는 것은 문제의 범위를 좁히는 가장 간단한 방법입니다. 아래에 설명 된 옵션을 사용 하 여 [네트워크 추적](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#network-traces) 을 캡처할 수 있습니다.

* [Fiddler를 사용 하 여 네트워크 추적 수집](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#network-traces)

* [Tcpdump를 사용 하 여 네트워크 추적 수집](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [브라우저에서 네트워크 추적 수집](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>클라이언트 요청

SignalR 영구 연결의 경우 먼저 호스트 된 `/negotiate` 앱 서버에 연결한 다음 Azure SignalR 서비스로 리디렉션되고 Azure SignalR service에 대 한 실제 영구 연결을 설정 합니다. 자세한 단계는 [Azure SignalR Service의 내부](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) 정보를 참조 하세요.

직접 클라이언트 쪽 네트워크 추적을 사용 하 여 어떤 상태 코드와 어떤 응답이 무엇이 실패 하는지 확인 하 고 [문제 해결 가이드](https://docs.microsoft.com/azure/azure-signalr/signalr-howto-troubleshoot-guide)내에서 솔루션을 찾습니다.

#### <a name="server-requests"></a>서버 요청

SignalR *서버* 는 *서버* 와 *서비스* 간의 *서버 연결* 을 유지 관리 합니다. 앱 서버를 시작 하면 Azure SignalR service에 대 한 **WebSocket** 연결이 시작 됩니다. 모든 클라이언트 트래픽을은 Azure SignalR service를 통해 이러한 *서버 연결* 로 라우팅되고 다음으로 발송 됩니다 `Hub` . *서버 연결이* 떨어지면이 *서버 연결* 로 라우팅되는 클라이언트에 영향을 줍니다. Microsoft Azure SignalR SDK에는 영향을 최소화 하기 위해 최대 1 분 지연 시간 동안 *서버 연결* 을 다시 연결 하는 논리가 "항상 재시도" 됩니다.

Azure SignalR Service의 네트워크 불안정 또는 정기 유지 관리 또는 호스트 된 앱 서버 업데이트/유지 관리 때문에 *서버 연결* 을 삭제할 수 있습니다. 클라이언트 쪽에 연결 끊기/다시 연결 메커니즘이 있으면 클라이언트 쪽에서 연결 끊기-다시 연결에 따른 영향을 최소화 합니다.

서버 쪽 네트워크 추적을 보고 *서비스* 에서 *서버 연결이* 삭제 되거나 거부 되는 이유 및 오류 정보를 확인 하 고 [문제 해결 가이드](https://docs.microsoft.com/azure/azure-signalr/signalr-howto-troubleshoot-guide)내에서 근본 원인을 찾으십시오.


### <a name="how-to-add-logs"></a>로그를 추가 하는 방법

로그는 문제를 진단 하 고 실행 상태를 모니터링 하는 데 유용할 수 있습니다.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>클라이언트 쪽 로그를 사용 하도록 설정 하는 방법

클라이언트 쪽 로깅 환경은 자체 호스팅 SignalR를 사용 하는 경우와 동일 합니다.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>클라이언트 쪽 로깅 사용 `ASP.NET Core SignalR`

* [JavaScript 클라이언트 로깅](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET 클라이언트 로깅](https://docs.microsoft.com/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>클라이언트 쪽 로깅 사용 `ASP.NET SignalR`

* [.NET 클라이언트](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Windows Phone 8 클라이언트에서 추적 사용](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [JavaScript 클라이언트에서 추적 사용](https://docs.microsoft.com/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>서버 쪽 로그를 사용 하도록 설정 하는 방법

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>서버 쪽 로깅을 사용 하도록 설정 `ASP.NET Core SignalR`

에 대 한 서버 쪽 로깅은 `ASP.NET Core SignalR` `ILogger` 프레임 워크에서 제공 된 기반 [로깅과](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&tabs=aspnetcore2x) 통합 `ASP.NET Core` 됩니다. 다음과 같이 샘플 사용을 사용 하 여 서버 쪽 로깅을 사용 하도록 설정할 수 있습니다 `ConfigureLogging` .

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR에 대 한로 거 범주는 항상로 시작 `Microsoft.Azure.SignalR` 합니다. Azure SignalR에서 자세한 로그를 사용 하도록 설정 하려면 `Information` 아래와 같이 파일 **의appsettings.js** 에서 이전 접두사를 수준으로 구성 합니다.

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

비정상적인 경고/오류 로그가 기록 되었는지 확인 합니다. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>서버 쪽 추적 사용 `ASP.NET SignalR`

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

비정상적인 경고/오류 로그가 기록 되었는지 확인 합니다. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Azure SignalR service 내에서 로그를 사용 하도록 설정 하는 방법

Azure SignalR service에 대 한 [진단 로그를 사용 하도록 설정할](https://docs.microsoft.com/azure/azure-signalr/signalr-tutorial-diagnostic-logs) 수도 있습니다. 이러한 로그는 azure SignalR 서비스에 연결 된 모든 연결에 대 한 자세한 정보를 제공 합니다.

<a name="serverless_mode_tsg"></a>

## <a name="serverless-mode-troubleshooting"></a>서버를 사용 하지 않는 모드 문제 해결

**Asrs** 가 *서버* 리스 모드에 있으면 **ASP.NET Core SignalR** 만 `Serverless` 모드를 지원 하 고 **ASP.NET SignalR** 는이 모드를 지원 **하지** 않습니다.

모드에서 연결 문제를 진단 하기 위해 `Serverless` 가장 직접적인 방법은 [클라이언트 쪽 트래픽을 보는](#view_traffic_client)것입니다. [클라이언트 쪽 로그](#add_logs_client) 와 [서비스 측 로그](#add_logs_server) 를 사용 하는 것도 도움이 될 수 있습니다.

<a name="classic_mode_tsg"></a>

## <a name="classic-mode-troubleshooting"></a>클래식 모드 문제 해결

`Classic` 모드는 사용 되지 않으며 사용 하지 않는 것이 좋습니다. 이 모드에서 Azure SignalR service는 연결 된 *서버 연결* 을 사용 하 여 현재 서비스가 `default` 모드 또는 모드 인지 확인 `serverless` 합니다. 이로 인해 네트워크 불안정성으로 인 한 모든 연결 된 *서버 연결이* 갑자기 삭제 되는 경우 Azure SignalR가 이제 모드로 전환 된 것으로 간주 하 `serverless` 고이 기간 동안 연결 된 클라이언트는 호스트 된 앱 서버로 라우팅되지 않습니다. [서비스 쪽 로그](#add_logs_server) 를 사용 하도록 설정 하 고, 응용 프로그램 서버를 호스트 하는 것 처럼 기록 된 클라이언트가 있는지 확인 `ServerlessModeEntered` 합니다. 그러나 일부 클라이언트는 앱 서버 쪽에 도달 하지 않습니다. 이 경우 [클라이언트 연결을 중단](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) 하 고 클라이언트를 다시 시작 하는 것이 도움이 될 수 있습니다.

문제 해결 `classic` 모드 연결 및 메시지 배달 문제는 [기본 모드 문제 해결](#default_mode_tsg)과 유사 합니다.

## <a name="service-health"></a>서비스 상태

상태 api에서 서비스 상태를 확인할 수 있습니다.

* 요청: 가져오기 `https://{instance_name}.service.signalr.net/api/v1/health`

* 응답 상태 코드:
  * 200: 정상입니다.
  * 503: 서비스가 비정상 상태입니다. 다음을 수행할 수 있습니다.
    * 자동으로 몇 분 정도 기다립니다.
    * Ip 주소가 포털의 ip와 동일한 지 확인 합니다.
    * 또는 인스턴스를 다시 시작 합니다.
    * 위의 모든 옵션이 작동 하지 않는 경우 Azure Portal에서 새로운 지원 요청을 추가 하 여 문의해 주세요.

[재해 복구](https://docs.microsoft.com/azure/azure-signalr/signalr-concept-disaster-recovery)에 대 한 자세한 정보.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 연결 및 메시지 배달 문제를 해결 하는 방법에 대해 알아보았습니다. 일반적인 문제를 처리 하는 방법에 대해서도 알아볼 수 있습니다. 

> [!div class="nextstepaction"]
> [문제 해결 가이드](./signalr-howto-troubleshoot-guide.md)