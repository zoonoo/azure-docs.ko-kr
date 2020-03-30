---
title: Azure 응용 프로그램 인사이트 원격 분석 채널 | 마이크로 소프트 문서
description: .NET 및 .NET 코어에 대한 Azure 응용 프로그램 인사이트 SDK에서 원격 분석 채널을 사용자 지정하는 방법.
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275699"
---
# <a name="telemetry-channels-in-application-insights"></a>애플리케이션 인사이트 원격 분석 채널

원격 분석 채널은 [Azure 응용 프로그램 인사이트 SDK의 필수적인 부분입니다.](../../azure-monitor/app/app-insights-overview.md) 응용 프로그램 인사이트 서비스에 원격 분석의 버퍼링 및 전송을 관리합니다. SDK의 .NET 및 .NET Core 버전에는 두 개의 기본 제공 `InMemoryChannel` `ServerTelemetryChannel`원격 분석 채널이 있습니다. 이 문서에서는 채널 동작을 사용자 지정하는 방법을 포함하여 각 채널에 대해 자세히 설명합니다.

## <a name="what-are-telemetry-channels"></a>원격 분석 채널이란 무엇입니까?

원격 분석 채널은 원격 분석 항목을 버퍼링하고 쿼리 및 분석을 위해 저장되는 Application Insights 서비스로 전송합니다. 원격 분석 채널은 인터페이스를 구현하는 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) 모든 클래스입니다.

모든 `Send(ITelemetry item)` 원격 분석 초기화자 및 원격 분석 프로세서가 호출된 후 원격 분석 채널의 메서드가 호출됩니다. 따라서 원격 분석 프로세서에서 삭제한 항목은 채널에 도달하지 않습니다. `Send()`일반적으로 백 엔드로 즉시 항목을 보내지 않습니다. 일반적으로 메모리에서 버퍼링하고 일괄 처리하여 효율적인 전송을 위해 보냅니다.

[라이브 메트릭 스트림에는](live-stream.md) 원격 분석의 실시간 스트리밍을 지원하는 사용자 지정 채널도 있습니다. 이 채널은 일반 원격 분석 채널과 독립적이며 이 문서는 해당 채널에 적용되지 않습니다.

## <a name="built-in-telemetry-channels"></a>기본 제공 원격 분석 채널

애플리케이션 인사이트 .NET 및 .NET 코어 SDK는 두 개의 기본 제공 채널을 제공합니다.

* `InMemoryChannel`: 항목이 전송될 때까지 메모리에 있는 항목을 버퍼링하는 경량 채널입니다. 항목은 메모리에서 버퍼링되고 30초마다 한 번씩 플러시되거나 500개의 항목이 버퍼링될 때마다 플러시됩니다. 이 채널은 실패 후 원격 분석을 다시 시도하지 않으므로 최소한의 안정성 보장을 제공합니다. 또한 이 채널은 항목을 디스크에 보관하지 않으므로 보내지 않은 항목은 응용 프로그램 종료 시 영구적으로 손실됩니다(정상적으로 사용 여부). 이 채널은 `Flush()` 메모리 내 원격 분석 항목을 동기적으로 강제로 플러시하는 데 사용할 수 있는 메서드를 구현합니다. 이 채널은 동기 플러시가 이상적인 단기 실행 애플리케이션에 적합합니다.

    이 채널은 더 큰 Microsoft.ApplicationInsights NuGet 패키지의 일부이며 다른 채널이 구성되지 않은 경우 SDK가 사용하는 기본 채널입니다.

* `ServerTelemetryChannel`: 재시도 정책과 로컬 디스크에 데이터를 저장할 수 있는 기능이 있는 고급 채널입니다. 일시적인 오류가 발생하는 경우 이 채널은 원격 분석을 다시 시도합니다. 또한 이 채널은 로컬 디스크 저장소를 사용하여 네트워크 중단 또는 높은 원격 분석 볼륨 동안 디스크에 있는 항목을 유지합니다. 이러한 재시도 메커니즘과 로컬 디스크 저장소로 인해 이 채널은 보다 안정적인 것으로 간주되며 모든 프로덕션 시나리오에 권장됩니다. 이 채널은 공식 설명서에 따라 구성된 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 및 [ASP.NET 핵심](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 응용 프로그램의 기본값입니다. 이 채널은 장기 실행 프로세스의 서버 시나리오에 최적화되어 있습니다. 이 [`Flush()`](#which-channel-should-i-use) 채널에서 구현하는 메서드는 동기가 아닙니다.

    이 채널은 Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet 패키지로 제공되며 Microsoft.ApplicationInsights.Web 또는 Microsoft.ApplicationInsights.AspNetCore NuGet을 사용할 때 자동으로 획득됩니다. 패키지.

## <a name="configure-a-telemetry-channel"></a>원격 분석 채널 구성

활성 원격 분석 구성으로 설정하여 원격 분석 채널을 구성합니다. ASP.NET 응용 프로그램의 경우 구성에는 원격 분석 `TelemetryConfiguration.Active`채널 인스턴스를 `ApplicationInsights.config`을 로 설정하거나 을 수정하는 작업이 포함됩니다. ASP.NET 핵심 응용 프로그램의 경우 구성에는 종속성 주입 컨테이너에 채널을 추가해야 합니다.

다음 섹션에서는 다양한 응용 프로그램 `StorageFolder` 유형에서 채널에 대한 설정을 구성하는 예제를 보여 주며, 이 에 대한 예제를 보여 준다. `StorageFolder`은 구성 가능한 설정 중 하나일 뿐입니다. 구성 설정의 전체 목록은 이 [문서의 후반부에서 설정 섹션을](telemetry-channels.md#configurable-settings-in-channels) 참조하십시오.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대한 ApplicationInsights.config를 사용하여 구성

[ApplicationInsights.config의](configuration-with-applicationinsights-config.md) `ServerTelemetryChannel` 다음 섹션은 사용자 지정 `StorageFolder` 위치로 설정된 채널을 보여 주며,

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대한 코드 구성

다음 코드는 사용자 지정 위치로 `StorageFolder` 설정된 'ServerTelemetryChannel' 인스턴스를 설정합니다. 응용 프로그램의 시작 부분에 이 코드를 `Application_Start()` 추가합니다(일반적으로 Global.aspx.cs 메서드).

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET 핵심 응용 프로그램에 대한 코드 구성

다음과 `ConfigureServices` 같이 클래스의 메서드를 `Startup.cs` 수정합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> ASP.NET 코어 응용 프로그램에는 채널을 사용하여 `TelemetryConfiguration.Active` 채널을 구성하는 것이 좋습니다.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET 코어 콘솔 응용 프로그램에 대한 코드 구성

콘솔 앱의 경우 .NET 및 .NET Core 모두에서 코드는 동일합니다.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>서버 텔레메일채널의 운영 세부 정보

`ServerTelemetryChannel`도착한 항목을 메모리 내 버퍼에 저장합니다. 항목은 30초마다 한 번씩 또는 `Transmission` 500개의 항목이 버퍼링된 경우 직렬화, 압축 및 인스턴스에 저장됩니다. 단일 `Transmission` 인스턴스에는 최대 500개의 항목이 포함되며 응용 프로그램 인사이트 서비스에 대한 단일 HTTPS 호출을 통해 전송되는 원격 분석 일괄 처리를 나타냅니다.

기본적으로 최대 10개의 `Transmission` 인스턴스를 병렬로 보낼 수 있습니다. 원격 분석이 더 빠른 속도로 도착하거나 네트워크 또는 Application Insights 백 `Transmission` 엔드가 느린 경우 인스턴스가 메모리에 저장됩니다. 이 메모리 `Transmission` 내 버퍼의 기본 용량은 5MB입니다. 메모리 내 용량을 초과하면 `Transmission` 인스턴스는 최대 50MB까지 로컬 디스크에 저장됩니다. `Transmission`인스턴스는 네트워크 문제가 있는 경우에도 로컬 디스크에 저장됩니다. 로컬 디스크에 저장된 항목만 응용 프로그램 충돌을 견딜 수 있습니다. 응용 프로그램이 다시 시작될 때마다 전송됩니다.

## <a name="configurable-settings-in-channels"></a>채널에서 구성 가능한 설정

각 채널에 대한 구성 가능한 설정의 전체 목록은 다음을 참조하십시오.

* [인메모리채널](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [서버텔레메트리채널](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

다음은 가장 일반적으로 사용되는 `ServerTelemetryChannel`설정입니다.

1. `MaxTransmissionBufferCapacity`: 채널에서 메모리의 전송을 버퍼링하는 데 사용하는 최대 메모리 양(바이트)입니다. 이 용량에 도달하면 새 항목이 로컬 디스크에 직접 저장됩니다. 기본값은 5MB입니다. 값을 높게 설정하면 디스크 사용량이 줄어들지만 응용 프로그램이 충돌하면 메모리에 있는 항목이 손실됩니다.

1. `MaxTransmissionSenderCapacity`: 동시에 응용 `Transmission` 프로그램 인사이트로 전송되는 최대 인스턴스 수입니다. 기본값은 10입니다. 이 설정은 더 높은 숫자로 구성할 수 있으며, 방대한 양의 원격 분석이 생성될 때 권장됩니다. 대용량은 일반적으로 부하 테스트 중에 발생하거나 샘플링이 꺼져 있을 때 발생합니다.

1. `StorageFolder`: 필요에 따라 항목을 디스크에 저장하는 데 채널에서 사용하는 폴더입니다. Windows에서 다른 경로를 명시적으로 지정하지 않으면 %LOCALAPPDATA% 또는 %TEMP%가 사용됩니다. Windows 이외의 환경에서는 유효한 위치 또는 원격 분석이 로컬 디스크에 저장되지 않음을 지정해야 합니다.

## <a name="which-channel-should-i-use"></a>어떤 채널을 사용해야 하나요?

`ServerTelemetryChannel`장기 실행 응용 프로그램과 관련된 대부분의 프로덕션 시나리오에 권장됩니다. 구현된 `Flush()` `ServerTelemetryChannel` 메서드는 동기가 아니며 메모리 또는 디스크에서 보류 중인 모든 항목을 보내는 것을 보장하지 않습니다. 응용 프로그램이 종료될 시나리오에서 이 채널을 사용하는 경우 호출 `Flush()`후 약간의 지연을 발생시키는 것이 좋습니다. 필요한 정확한 지연 량은 예측할 수 없습니다. 메모리에 있는 항목 또는 `Transmission` 인스턴스 수, 디스크에 있는 항목 수, 백 엔드로 전송되는 항목 수, 채널이 지수 백오프 시나리오 중간에 있는지 여부 등의 요인에 따라 달라집니다.

동기 플러시를 수행해야 하는 경우 을 사용하는 `InMemoryChannel`것이 좋습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>응용 프로그램 인사이트 채널이 원격 분석 전송을 보장합니까? 그렇지 않은 경우 원격 분석을 잃을 수 있는 시나리오는 무엇입니까?

짧은 대답은 기본 제공 채널 중 어느 것도 백 엔드에 대한 원격 분석 배달의 트랜잭션 유형 보증을 제공하지 않는다는 것입니다. `ServerTelemetryChannel`안정적인 배달을 `InMemoryChannel` 위해 에 비해 더 고급이지만 원격 분석을 보내는 데 가장 노력적인 시도만 합니다. 다음과 같은 일반적인 시나리오를 포함하여 여러 상황에서 원격 분석이 손실될 수 있습니다.

1. 메모리에 있는 항목은 응용 프로그램이 충돌할 때 손실됩니다.

1. 네트워크 문제의 연장된 기간 동안 원격 분석이 손실됩니다. 원격 분석은 네트워크 가동 중단 중 또는 Application Insights 백 엔드에서 문제가 발생할 때 로컬 디스크에 저장됩니다. 단, 48시간 이상 된 품목은 폐기됩니다.

1. Windows에서 원격 분석을 저장하기 위한 기본 디스크 위치는 %LOCALAPPDATA% 또는 %TEMP%입니다. 이러한 위치는 일반적으로 컴퓨터에 로컬입니다. 응용 프로그램이 한 위치에서 다른 위치로 물리적으로 마이그레이션되는 경우 원래 위치에 저장된 모든 원격 분석이 손실됩니다.

1. Windows의 웹 앱에서 기본 디스크 저장소 위치는 D:\local\LocalAppData입니다. 이 위치는 유지되지 않습니다. 앱 재시작, 확장 기 및 기타 작업에서 소멸되어 저장되는 원격 분석이 손실됩니다. 기본값을 재정의하고 D:\home과 같은 지속가능한 위치에 저장소를 지정할 수 있습니다. 그러나 이러한 지속 된 위치는 원격 저장소에 의해 제공 되므로 느릴 수 있습니다.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel은 Windows 이외의 시스템에서 작동합니까?

패키지 및 네임스페이스 이름에는 "WindowsServer"가 포함되어 있지만 이 채널은 Windows 이외의 시스템에서 지원되지만 다음 예외는 없습니다. Windows 이외의 시스템에서는 채널이 기본적으로 로컬 저장소 폴더를 만들지 않습니다. 로컬 저장소 폴더를 만들고 채널을 구성하여 사용해야 합니다. 로컬 저장소를 구성한 후 채널은 모든 시스템에서 동일한 방식으로 작동합니다.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK에서 임시 로컬 스토리지를 작성하나요? 데이터가 저장소에서 암호화되어 있습니까?

SDK는 네트워크 문제 또는 제한 중에 원격 분석 항목을 로컬 저장소에 저장합니다. 이 데이터는 로컬로 암호화되지 않습니다.

Windows 시스템의 경우 SDK는 %TEMP% 또는 %LOCALAPPDATA% 디렉터리에서 임시 로컬 폴더를 자동으로 만들고 관리자와 현재 사용자에 대한 액세스만 제한합니다.

Windows 이외의 시스템의 경우 SDK에서 로컬 저장소를 자동으로 만들지 않으므로 기본적으로 로컬에 데이터가 저장되지 않습니다. 저장소 디렉터리를 직접 만들고 사용할 채널을 구성할 수 있습니다. 이 경우 디렉터리의 보안을 보장할 책임이 있습니다.
[데이터 보호 및 개인 정보 보호에](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)대해 자세히 알아보기.

## <a name="open-source-sdk"></a>오픈 소스 SDK
애플리케이션 인사이트를 위한 모든 SDK와 마찬가지로 채널은 오픈 소스입니다. [공식 GitHub 리포지토리에서](https://github.com/Microsoft/ApplicationInsights-dotnet)코드를 읽고 기여하거나 문제를 보고합니다.

## <a name="next-steps"></a>다음 단계

* [샘플링](../../azure-monitor/app/sampling.md)
* [SDK 문제 해결](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
