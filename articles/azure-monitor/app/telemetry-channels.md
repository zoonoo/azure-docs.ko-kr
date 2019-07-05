---
title: Azure Application Insights에서 원격 분석 채널 | Microsoft Docs
description: .NET 및.NET Core 용 Azure Application Insights Sdk에서 원격 분석 채널을 사용자 지정 하는 방법입니다.
services: application-insights
documentationcenter: .net
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: af00641123354831c7bf174a743ded2886343579
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561356"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights에서 원격 분석 채널

원격 분석 채널의 핵심은는 [Azure Application Insights Sdk](../../azure-monitor/app/app-insights-overview.md)합니다. Application Insights 서비스로 원격 분석의 버퍼링 및 전송을 관리 합니다. Sdk의.NET 및.NET Core 버전에는 두 개의 기본 제공 원격 분석 채널: `InMemoryChannel` 고 `ServerTelemetryChannel`입니다. 이 문서에서는 각 채널이 채널 동작을 사용자 지정 하는 방법 등을 자세히 설명 합니다.

## <a name="what-are-telemetry-channels"></a>원격 분석 채널 이란 무엇 인가요?

원격 분석 채널은 원격 분석 항목을 버퍼링 하 고 Application Insights 서비스에 보내는 쿼리 및 분석 하는 저장 하는 일을 담당 합니다. 원격 분석 채널은 구현 하는 클래스를 [ `Microsoft.ApplicationInsights.ITelemetryChannel` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) 인터페이스입니다.

`Send(ITelemetry item)` 모든 원격 분석 이니셜라이저 후 원격 분석 채널의 메서드가 호출 되 고 원격 분석 프로세서 이라고 합니다. 따라서 원격 분석 프로세서에 의해 삭제 된 항목 채널에 도달 하지 않습니다. `Send()` 일반적으로 보내지 않습니다 항목 백 엔드에 즉시 합니다. 일반적으로 메모리에 버퍼링 하 고 효율적인 전송을 위해 일괄로 보냅니다.

[라이브 메트릭 Stream](live-stream.md) 원격 분석의 라이브 스트리밍과 구동 하는 사용자 지정 채널도 있습니다. 이 채널에는 일반 원격 분석 채널의 독립적 이며이 문서에 적용 되지 않습니다.

## <a name="built-in-telemetry-channels"></a>기본 제공 원격 분석 채널

Application Insights.NET 및.NET Core Sdk를 두 개의 기본 제공 채널을 사용 하 여 제공 합니다.

* `InMemoryChannel`: 전송 되는 때까지 항목을 메모리에 버퍼링 하는 간단한 채널입니다. 항목은 메모리에 버퍼링 및 30 초 마다 한 번, 플러시 또는 때마다 500 개 항목은 버퍼링 됩니다. 이 채널은 원격 분석 전송 실패 후 다시 시도 하지 않습니다 것 때문에 최소한의 안정성 보장을 제공 합니다. 이 채널도 하지 항목을 디스크에 유지 되므로 보내지 않은 항목이 응용 프로그램 종료 시 영구적으로 손실 (정상적인 여부). 이 채널에서 구현 된 `Flush()` 강제 플러시 모든 메모리 내 원격 분석 항목 동기적으로 하는 메서드. 이 채널은 동기 플러시 적합 단기 실행 응용 프로그램에 적합 합니다.

    이 채널 더 큰 Microsoft.ApplicationInsights NuGet 패키지의 일부 이며 구성 된 아무 경우 SDK를 사용 하는 기본 채널입니다.

* `ServerTelemetryChannel`: 고급 사용 하는 채널에 다시 시도 정책 및 로컬 디스크에 데이터를 저장 하는 기능입니다. 이 채널에 일시적인 오류가 발생 하면 보내는 원격 분석을 다시 시도 합니다. 또한이 채널 네트워크 중단 또는 많은 원격 분석 볼륨 중 디스크에서 항목을 유지 하려면 로컬 디스크 storage를 사용 합니다. 이러한 재시도 메커니즘 및 로컬 디스크 저장소로 인해이 채널 안정적 간주 되 고 모든 프로덕션 시나리오에 권장 됩니다. 이 채널에 대 한 기본값인 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 하 고 [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 공식 설명서에 따라 구성 된 응용 프로그램입니다. 이 채널은 장기 실행 프로세스를 사용 하 여 서버 시나리오에 대 한 최적화 됩니다. 합니다 [ `Flush()` ](#which-channel-should-i-use) 이 채널에서 구현 되는 메서드는 동기 없습니다.

    이 채널은 Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel NuGet 패키지로 제공 되며 Microsoft.ApplicationInsights.Web 또는 Microsoft.ApplicationInsights.AspNetCore NuGet을 사용 하는 경우 자동으로 획득 패키지입니다.

## <a name="configure-a-telemetry-channel"></a>원격 분석 채널 구성

현재 원격 분석 구성을 설정 하 여 원격 분석 채널을 구성 합니다. ASP.NET 응용 프로그램에 대 한 구성 포함는 원격 분석 채널 인스턴스를 설정 `TelemetryConfiguration.Active`를 수정 하 여 `ApplicationInsights.config`입니다. ASP.NET Core 응용 프로그램에 대 한 구성 종속성 주입 컨테이너에 채널을 추가 해야 합니다.

다음 섹션에서는 구성의 예제를 보여 줍니다.는 `StorageFolder` 다양 한 응용 프로그램에서에서 채널에 대 한 설정입니다. `StorageFolder` 구성 가능한 설정 중 하나일 뿐입니다. 구성 설정의 전체 목록을 참조 하세요 [의 설정 섹션](telemetry-channels.md#configurable-settings-in-channels) 이 문서의 뒷부분에 나오는.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>ASP.NET 응용 프로그램의 ApplicationInsights.config를 사용 하 여 구성

다음 섹션에서 [ApplicationInsights.config](configuration-with-applicationinsights-config.md) 표시 된 `ServerTelemetryChannel` 사용 하 여 구성 된 채널 `StorageFolder` 사용자 지정 위치로 설정:

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

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대 한 코드에서 구성

다음 코드를 사용 하 여 'ServerTelemetryChannel' 인스턴스를 설정 `StorageFolder` 사용자 지정 위치로 설정 합니다. 일반적으로 응용 프로그램의 시작 부분에 다음이 코드를 추가 `Application_Start()` Global.aspx.cs에서 메서드.

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램에 대 한 코드에서 구성

수정 된 `ConfigureServices` 메서드는 `Startup.cs` 다음과 같이 클래스:

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
> 채널을 사용 하 여 구성 `TelemetryConfiguration.Active` ASP.NET Core 응용 프로그램에 권장 되지 않습니다.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 콘솔 응용 프로그램에 대 한 코드에서 구성

콘솔 앱을 위한 코드는.NET 및.NET Core에 대해 동일.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 한 운영 세부 정보

`ServerTelemetryChannel` 도착 하는 메모리 내 버퍼에 있는 항목을 저장 합니다. 항목 직렬화, 압축 및 저장을 `Transmission` 30 초 마다 한 번, 인스턴스 500 개 항목을 버퍼링 된 또는 합니다. 단일 `Transmission` 인스턴스 최대 500 개 항목을 포함 하 고 Application Insights 서비스에 단일 HTTPS 호출을 통해 전송한 원격 분석의 일괄 처리를 나타냅니다.

기본적으로 10 개 사이로 `Transmission` 인스턴스를 동시에 보낼 수 있습니다. 원격 분석 더 빠른 속도로 도착 하는 경우 end가 느린 경우 네트워크 또는 Application Insights 다시 `Transmission` 인스턴스가 메모리에 저장 됩니다. 이 메모리의 기본 용량 `Transmission` 버퍼가 5MB입니다. 메모리 용량을 초과 하는 경우 `Transmission` 인스턴스 최대 50MB까지 로컬 디스크에 저장 됩니다. `Transmission` 네트워크 문제가 인스턴스가 또한 로컬 디스크에 저장 됩니다. 로컬 디스크에 저장 되는 항목만 응용 프로그램 작동이 중단 시에도 존속 합니다. 응용 프로그램 다시 시작 될 때마다 전송 되는 합니다.

## <a name="configurable-settings-in-channels"></a>채널의 구성 가능한 설정

각 채널에 대 한 구성 가능한 설정의 전체 목록을 참조 하세요.

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

다음은 가장 일반적으로 사용 되는 설정에 대 한 `ServerTelemetryChannel`:

1. `MaxTransmissionBufferCapacity`: 최대 메모리 (바이트)를 메모리에 버퍼 전송 채널에서 사용 하는 양입니다. 이 용량에 도달 하면 새 항목은 로컬 디스크에 직접 저장 됩니다. 기본값은 5MB입니다. 값을 높게 설정 더 적은 디스크 사용량으로 인해 그러나 응용 프로그램이 크래시 되는 경우 메모리에서 항목이 손실 됩니다.

1. `MaxTransmissionSenderCapacity`: 최대 `Transmission` 인스턴스를 동시에 Application Insights로 전송 됩니다. 기본값은 10입니다. 이 설정은 방대한 양의 원격 분석은 생성 된 때 권장 되는 더 높은 숫자를 구성할 수 있습니다. 대용량은 샘플링 꺼진 경우 또는 부하 테스트 중 일반적으로 발생 합니다.

1. `StorageFolder`: 필요에 따라 디스크에 항목을 저장 하는 채널에 의해 사용 되는 폴더입니다. Windows에서 % LOCALAPPDATA % 또는 % TEMP % 명시적으로 지정 된 경로가 다른 경우 사용 됩니다. Windows 이외의 환경에 유효한 위치 또는 원격 분석을 로컬 디스크에 저장할 수 없습니다 지정 해야 합니다.

## <a name="which-channel-should-i-use"></a>채널을 사용 해야 합니까?

`ServerTelemetryChannel` 장기 실행 응용 프로그램을 포함 하는 대부분의 프로덕션 시나리오에 권장 됩니다. 합니다 `Flush()` 메서드를 구현한 `ServerTelemetryChannel` 동기적으로 되지 않습니다도 한다는 보장이 없다는 메모리 또는 디스크에서 모든 보류 중인 항목을 전송 하 고 합니다. 응용 프로그램 종료 하려고 합니다. 여기서는 시나리오에서이 채널을 사용 하면 호출한 후 약간의 지연이 도입 하는 것이 좋습니다 `Flush()`합니다. 예측 가능한 정확한 양의 필요할 수 있는 지연 되지 않습니다. 얼마나 많은 항목 같은 요인에 따라 다르겠지요 또는 `Transmission` 인스턴스는 메모리, 횟수는 디스크, 얼마나 많은 백 엔드로 전송 중이면 및 채널 지 수 백오프 시나리오 중간 인지 합니다.

동기 플러시 작업을 수행 하는 경우 사용 하는 것이 좋습니다 `InMemoryChannel`합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>채널을 Application Insights 원격 분석 배달 반드시? 그렇지 않은 경우 원격 분석 손실 될 수 있는 시나리오는 무엇입니까?

간단 하 게 말하면은 기본 제공 채널은 하나도 보장 트랜잭션 유형 백 엔드로 원격 분석 배달 됩니다. `ServerTelemetryChannel` 더 많은 고급에 비해 `InMemoryChannel` 만 최상의 시도 하는 원격 분석을 보내도록 하기도 하지만 신뢰할 수 있는 배달에 대 한 합니다. 원격 분석 이러한 일반적인 시나리오를 비롯 한 여러 경우 여전히 손실 될 수 있습니다.

1. 응용 프로그램이 충돌할 때 메모리에서 항목이 손실 됩니다.

1. 원격 분석의 네트워크 문제 기간 동안 손실 됩니다. 원격 분석은 네트워크 중단 또는 Application Insights 백 엔드를 사용 하 여 발생 한 문제 중 로컬 디스크에 저장 됩니다. 그러나 24 시간 보다 오래 된 항목 삭제 됩니다.

1. Windows에서 원격 분석을 저장 하기 위한 기본 디스크 위치는 % LOCALAPPDATA % 또는 % TEMP %입니다. 이러한 위치는 일반적으로 컴퓨터에 로컬입니다. 응용 프로그램에서 다른 한 위치에서 물리적으로 마이그레이션합니다, 원래 위치에 저장 된 원격 분석이 손실 됩니다.

1. Windows에서 웹 앱에서 기본 디스크-저장소 위치가 D:\local\LocalAppData 합니다. 이 위치를 유지 하지 않습니다. 앱 다시 시작, 크기 제한 및 기타 이러한 작업에는 여기에 저장 된 원격 분석이 손실에서 초기화 된 것입니다. 기본값을 재정의 하 고 저장소 D:\home 지속형된 위치를 지정할 수 있습니다. 그러나 이러한 지속형된 위치 원격 저장소에서 제공 하는 있으며 따라서 속도가 느려질 수 있습니다.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel은 Windows 이외의 시스템에서 작동 합니까?

해당 패키지 및 네임 스페이스의 이름을 "WindowsServer"를 포함 하지만이 채널이 다음 예외를 사용 하 여, Windows 이외의 시스템에서 지원 됩니다. Windows 이외의 시스템에서 채널을 기본적으로 로컬 저장소 폴더를 만들지 않습니다. 로컬 저장소 폴더를 만들고 사용 하도록 채널을 구성 해야 합니다. 로컬 저장소를 구성한 후 채널 동일한 방식으로 모든 시스템에서 작동 합니다.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK에서 임시 로컬 저장소를 작성하나요? 데이터는 저장소에서 암호화가?

SDK는 네트워크 문제 또는 제한 중 로컬 저장소에 원격 분석 항목을 저장합니다. 이 데이터는 로컬로 암호화 되지 않습니다.

Windows 시스템의 경우 SDK는 자동으로 % TEMP % 또는 % LOCALAPPDATA % 디렉터리에 임시 로컬 폴더를 만듭니다 하 고 관리자 및 현재 사용자 전용 액세스를 제한 합니다.

Windows 이외의 시스템에 대 한 로컬 저장소 SDK에서 자동으로 만들어진 및 이므로 데이터가 기본적으로 로컬로 저장 됩니다. 직접 저장소 디렉터리를 만들 하 고 사용 하도록 채널을 구성할 수 있습니다. 이 경우 라면 디렉터리에 보안을 담당 합니다.
에 대해 자세히 알아보세요 [데이터 보호 및 프라이버시](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)합니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
Application Insights에 대 한 모든 SDK와 같은 채널은 오픈 소스입니다. 읽고 있는 코드 또는 문제 보고를 적용할 [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet)합니다.

## <a name="next-steps"></a>다음 단계

* [샘플링](../../azure-monitor/app/sampling.md)
* [SDK 문제 해결](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
