---
title: Azure Application Insights에서 원격 분석 채널 | Microsoft Docs
description: Azure Application Insights Sdk에서 원격 분석 채널.NET/.NET Core에 대 한 사용자 지정 하는 방법입니다.
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
ms.openlocfilehash: fa99e29e9ec6c2bef7296a50dd381ee5fc60a1cb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255818"
---
# <a name="telemetrychannel-in-application-insights"></a>Application Insights에서 TelemetryChannel

TelemetryChannel는의 필수적인 부분 [Azure Application Insights Sdk](../../azure-monitor/app/app-insights-overview.md)합니다. Application Insights 서비스로 원격 분석의 버퍼링 및 전송을 관리합니다. Sdk의.NET 및.NET Core 버전에는 두 개의 기본 제공 TelemetryChannels- `InMemoryChannel` 고 `ServerTelemetryChannel`입니다. 이 문서에서는 각 채널을 사용자 채널 동작을 지정할 수 있습니다 하는 방법을 포함 하 여 자세히 설명 합니다.

## <a name="what-is-a-telemetrychannel"></a>TelemetryChannel 란?

`TelemetryChannel` 버퍼링 및 쿼리 및 분석에 대 한 저장 된 Application Insights 서비스에 원격 분석 항목을 전송 하는 일을 담당 합니다. 인터페이스를 구현 하는 모든 클래스 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)

합니다 `Send(ITelemetry item)` TelemetryChannel의 메서드는 결국 `TelemetryInitializer`s 및 `TelemetryProcessor`가 호출 됩니다. 즉, 모든 항목에 의해 삭제 `TelemetryProcessor` 채널에 도달 하지 않습니다. `Send()` 일반적으로 보내지 않습니다 항목 즉시 백 엔드. 일반적으로 버퍼링 된 메모리 내 되며 효율적인 전송을 위해 일괄 처리로 전송 됩니다.

[LiveMetrics](live-stream.md) 원격 분석의 라이브 스트리밍과 구동 하는 사용자 지정 채널을 있습니다. 이 채널 일반 원격 분석 채널의 독립적 이며이 문서에서 사용 하는 채널에 적용 되지 않습니다 `LiveMetrics`합니다.

## <a name="built-in-telemetrychannels"></a>기본 제공 TelemetryChannels

Application Insights.NET/.NET Core SDK는 두 개의 기본 제공 채널을 사용 하 여 제공 됩니다.

* **InMemoryChannel** 
 `InMemoryChannel` 은 전송 될 때까지 항목을 메모리에 버퍼링 하는 간단한 채널입니다. 항목은 메모리에 버퍼링 및 30 초 마다 한 번씩 플러시 또는 500 개 항목 버퍼링 있을 때마다 합니다. 이 채널 실패 시 원격 분석을 보내는 다시 시도 하지 않습니다 것 처럼 최소한의 안정성 보장을 제공 합니다. 이 채널 (정상적으로 또는 비공유) 보내지 않은 항목이 응용 프로그램 종료 시 영구적으로 손실 되므로를 디스크에 항목을 유지 하지 않습니다. 한 `Flush()` 강제 플러시 모든 메모리 내 원격 분석 항목 동기적으로를 사용할 수 있는이 채널에서 구현 하는 메서드. 이 동기 플러시 적합 단기 실행 응용 프로그램에 적합 합니다.

    이 채널의 일부로 제공 되는 `Microsoft.ApplicationInsights` nuget 자체 패키지 및 SDK에 아무를 구성할 때 사용 하 여 기본 채널입니다.

* **ServerTelemetryChannel** 
 `ServerTelemetryChannel` 다시 시도 정책 및 로컬 디스크에 데이터를 저장 하는 기능이 있는 고급 채널이 있습니다. 이 채널에 일시적인 오류가 발생 하면 보내는 원격 분석을 다시 시도 합니다. 또한이 채널 네트워크 중단 또는 많은 원격 분석 볼륨 중 디스크에서 항목을 유지 하려면 로컬 디스크 storage를 사용 합니다. 이러한 재시도 메커니즘 및 로컬 디스크 저장소로 인해이 채널 더 안정적으로 간주 되 고 모든 프로덕션 시나리오에 권장 됩니다. 이 채널에 대 한 기본값인 [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net) 하 고 [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 연결 된 공식 문서에 따라 구성 되어 있는 응용 프로그램. 이 채널은 장기 실행 프로세스의 서버 시나리오를 위해 최적화 됩니다. 합니다 [ `Flush()` ](#which-channel-should-i-use) 이 채널에서 구현 하는 메서드 동기화 되지 않습니다.

    이 채널은 NuGet 패키지로 제공 되 `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`, NuGet 패키지 중 하나를 사용 하는 경우 자동으로 상태가 되 고 `Microsoft.ApplicationInsights.Web` 또는 `Microsoft.ApplicationInsights.AspNetCore`합니다.

## <a name="configuring-telemetrychannel"></a>TelemetryChannel 구성

원하는 설정 하 여 원격 분석 채널을 구성할 수 있습니다 `TelemetryChannel` 은 활성 `TelemetryConfiguration`합니다. Asp.Net 응용 프로그램에 대 한 구성 설정이 포함 됩니다 `TelemetryChannel` 대 `TelemetryConfiguration.Active`, 수정 또는 `ApplicationInsights.config`합니다. ASP.NET Core 응용 프로그램에 대 한 구성 종속성 주입 컨테이너에 원하는 채널을 추가 해야 합니다.

다음 예제는 사용자를 구성 하는 것은 `StorageFolder` 채널에 대 한 합니다. `StorageFolder` 구성 가능한 설정 중 하나일 뿐입니다. 구성 설정의 전체 목록은 설명 [설정 섹션에서](telemetry-channels.md#configurable-settings-in-channel)합니다.

## <a name="configuration-using-applicationinsightsconfig-for-aspnet-applications"></a>ApplicationInsights.Config를 사용 하 여 ASP.NET 응용 프로그램에 대 한 구성

다음 섹션에서 [ApplicationInsights.config](configuration-with-applicationinsights-config.md) 사용 하 여 구성 ServerTelemetryChannel 표시 `StorageFolder` 사용자 지정 위치로 설정 합니다.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!--TelemetryProcessors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

## <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대 한 코드에서 구성

다음 코드를 사용 하 여 ServerTelemetryChannel 설정 `StorageFolder` 사용자 지정 위치로 설정 합니다. 이 코드에 application_start () 메서드를 일반적으로 응용 프로그램의 시작 부분에 추가 해야 `Global.aspx.cs`

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

## <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램 코드에서 구성

수정할 `ConfigureServices` 메서드의 `Startup.cs` 아래와 같이 클래스입니다.

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

> [!NOTE]
> 사용 하 여 채널을 구성 하 해야 `TelemetryConfiguration.Active` ASP.NET Core 응용 프로그램에 권장 되지 않습니다.

## <a name="configuring-telemetrychannel-in-code-for-netnet-core-console-applications"></a>코드로 TelemetryChannel.NET/.NET Core 콘솔 응용 프로그램 구성

콘솔 앱을 위한 코드는.NET 및.NET Core에 대 한 동일 하 고 아래 표시 됩니다.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 한 운영 세부 정보

`ServerTelemetryChannel` 메모리 버퍼에 도착 하는 항목을 버퍼링 합니다. 직렬화, 압축 및 저장 `Transmission` 30 초 또는 때 500 개 항목 마다 버퍼링 되 면 인스턴스. 단일 `Transmission` 인스턴스 최대 500 개 항목을 포함 하 고 Application Insights 서비스에 대 한 단일 https 호출을 통해 전송 되는 원격 분석의 일괄 처리를 나타냅니다. 기본적으로 있을 수 있습니다 최대 10 개 `Transmission`병렬로 전송 되 고 s입니다. 원격 분석 더 빠른 속도로 도착 하는 경우 또는 네트워크/Application Insights 백 엔드는 다음 느린 경우 `Transmission`의메모리에 저장 합니다. 이 메모리 내 전송 버퍼의 기본 용량은 5MB입니다. 메모리 용량을 초과 하 되 면 `Transmission`s 최대 50mb까지 할당에 대 한 로컬 디스크에 저장 됩니다. `Transmission`s도 네트워크 문제가 있는 경우 로컬 디스크에 저장 됩니다. 로컬 디스크에 저장 된 항목에만 응용 프로그램을 다시 시작할 때마다 전송 되는 응용 프로그램 충돌을 시에도 존속 합니다.

## <a name="configurable-settings-in-channel"></a>채널의 구성 가능한 설정

각 채널에 대 한 구성 가능한 설정의 전체 목록은 다음과 같습니다.

[InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

[ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

가장 일반적으로 사용에 대 한 설정을 `ServerTelemetryChannel` 다음과 같습니다.

1. `MaxTransmissionBufferCapacity` -최대 메모리 (바이트)를 메모리에 버퍼 전송 채널에서 사용 하는 양입니다. 이 용량에 도달 하면 새 항목이 로컬 디스크에 직접 저장 됩니다. 기본값은 5MB입니다. 더 적은 디스크 사용량을 잠재 고객을 더 높은 값을 설정 하는 것이 응용 프로그램이 크래시 되는 경우 메모리에서 항목이 손실 될 해야 합니다.

2. `MaxTransmissionSenderCapacity` -최대 `Transmission`의동시에 Application Insights로 전송 됩니다. 기본값은 10 이지만 더 큰 값을 하도록 구성할 수 있습니다. 이 방대한 양의 원격 분석 생성 되 면 일반적으로 수행할 때 부하 테스트 및/또는 샘플링 꺼진 경우 권장 됩니다.

3. `StorageFolder` -채널에서 사용 하 여 필요에 따라 디스크에 항목을 저장할 폴더입니다. Windows에서의 % LocalAppData % 또는 % Temp % 경우 사용 됩니다, 명시적으로 구성 된 항목이 없습니다. 비-Windows 환경에서 사용자 **해야** 로컬 디스크에 저장 하지 않습니다 원격 분석 관계 없이 유효한 위치를 구성 합니다.

## <a name="which-channel-should-i-use"></a>채널을 사용 해야 합니까?

`ServerTelemetryChannel` 장기 실행 응용 프로그램의 대부분의 프로덕션 시나리오에 권장 됩니다. 합니다 `Flush()` 메서드 구현의 `ServerTelemetryChannel` 동기적인 없습니다 및 `Flush()` 메모리/디스크에서 모든 보류 중인 항목을 전송 하는 것을 보장 하지 않습니다. 이 채널은 응용 프로그램 종료 되려고을 호출한 후 약간의 지연이 수행 하는 것이 좋습니다. 여기서 시나리오에서 사용 하는 경우 `Flush()` 이 채널에서. 얼마나 많은 항목 같은 요인에 따라 필요한 지연의 정확한 크기를 예측할 수 없는 또는 `Transmissions` 메모리, 얼마나 많은 개수는로 전송 될의 디스크에 있는 경우 채널 지 수 백오프 시나리오 중입니다. 그런 다음 동기 플러시 작업을 수행 하는 필요한 경우 `InMemoryChannel` 것이 좋습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-applicationinsights-channel-offer-guaranteed-telemetry-delivery-or-what-are-the-scenarios-where-telemetry-can-be-lost"></a>*ApplicationInsights 채널 배달 보장 된 원격 분석 또는 원격 분석이 손실 될 수 있는 시나리오는 무엇을 제공 하나요?*

* 답은 기본 제공 채널은 하나도 백 엔드로 원격 분석 배달에 대 한 트랜잭션 형식 보장을 제공 합니다. 하는 동안 `ServerTelemetryChannel` 는 고급에 비해 `InMemoryChannel` 신뢰할 수 있는 원격 분석 배달 하기가 원격 분석을 보내도록 시도 하는 최상의 노력 하 고 원격 분석을 여러 시나리오로 여전히 손실 될 수 있습니다. 분석이 손실 일반적인 시나리오 중 일부는 다음과 같습니다.

1. 응용 프로그램 작동이 중단 될 때마다 메모리에서 항목이 손실 됩니다.
1. 원격 분석을 가져옵니다 네트워크 중단 또는 Application Insights 백 엔드를 사용 하 여 문제 중 로컬 디스크에 저장 됩니다. 그러나 24 시간 보다 오래 된 항목 삭제 됩니다. 따라서 원격 분석은 네트워크 문제는 확장 된 기간 동안 손실 합니다.
1. Windows에서 원격 분석을 저장 하기 위한 기본 디스크 위치는 % LocalAppData % 또는 % Temp %입니다. 이러한 위치는 일반적으로 컴퓨터에 로컬입니다. 응용 프로그램에서 다른 한 위치에서 물리적으로 마이그레이션합니다,이 위치에 저장 된 원격 분석이 손실 됩니다.
1. Azure 웹 앱 (Windows)에서 기본 디스크 위치는 "D:\local\LocalAppData"입니다. 이 위치는 유지 되지 않습니다 및 앱 다시 시작에서 초기화 된, 확장 아웃 등의 해당 위치에 저장 된 원격 분석은 손실 합니다. 사용자가 저장소 "D:\home"와 같은 지속형된 위치를 재정의할 수 있지만 이러한 지속형된 위치 아래 원격 저장소에서 제공 하는 속도가 느려질 수 있습니다.

### <a name="does-servertelemetrychannel-work-in-non-windows-systems"></a>*ServerTelemetryChannel 비 Windows 시스템에서 작동 하나요?*

* 패키지/네임 스페이스의 windows Server 중 이름이이 채널은 예외가 발생 하 여 비 Windows 시스템에서 지원 됩니다. 비-Windows에서 채널을 기본적으로 로컬 저장소 폴더를 만들지 않습니다. 사용자가 로컬 저장소 폴더를 만들고 사용 하도록 채널을 구성 해야 합니다. 로컬 저장소 구성 되 면 채널 Windows 및 비 Windows 시스템에서 동일한 작동 합니다.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>*SDK는 임시 로컬 저장소를 만드시겠습니까? 데이터는 저장소에서 암호화가?*

* SDK는 네트워크 문제 또는 제한 중 로컬 저장소에 원격 분석 항목을 저장합니다. 이 데이터를 로컬로 암호화 되지 않습니다.
Windows 시스템의 경우 SDK는 자동으로 TEMP 또는 APPDATA 디렉터리에 임시 로컬 폴더를 만듭니다 하 고 관리자 및 현재 사용자 전용 액세스를 제한 합니다.
비-Windows에 대 한 로컬 저장소 SDK에서 자동으로 만들어진 및 따라서 데이터가 기본적으로 로컬로 저장 됩니다. 사용자는 자체 저장소 디렉터리를 만듭니다 하 고 사용 하도록 채널을 구성할 수 있습니다. 이 경우 사용자가이 디렉터리에 보안 설정 되도록 해야 합니다.
에 대해 자세히 알아보세요 [데이터 보호 및 프라이버시](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)합니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
모든 Application Insights Sdk와 같은 채널은 또한 오픈 소스입니다. 읽기 및 코드 기여 또는 문제 보고 [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-dotnet)합니다.

## <a name="next-steps"></a>다음 단계

* [샘플링](../../azure-monitor/app/sampling.md)
* [SDK 문제 해결](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
