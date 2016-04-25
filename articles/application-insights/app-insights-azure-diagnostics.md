<properties
    pageTitle="Application Insights에 Azure 진단 로그 보내기"
    description="Application Insights 포털로 전송되는 Azure 클라우드 서비스 진단 로그에 대한 세부 정보를 구성합니다."
    services="application-insights"
    documentationCenter=".net"
    authors="sbtron"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="11/17/2015"
    ms.author="awills"/>

# Application Insights에 대해 Azure 진단 로깅 구성

Microsoft Azure에서 클라우드 서비스 프로젝트나 가상 컴퓨터를 설정하는 경우 [Azure는 진단 로그를 생성할 수 있습니다](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md/). 이것을 Application Insights로 보내도록 설정하여 Application Insights SDK에 의해 앱 내부에서 전송되는 진단 및 사용량 원격 분석 자료와 함께 분석할 수 있습니다. Azure 로그는 시작, 중지, 충돌은 물론 성능 카운터와 같은 앱에 대한 관리의 이벤트를 포함합니다. 로그는 앱의 호출을 System.Diagnostics.Trace에 포함합니다.

이 문서는 진단 캡처에 대한 구성을 자세히 설명합니다.

Visual Studio에 Azure SDK 2.8이 설치되어 있어야 합니다.

## Application Insights 리소스 가져오기

최상의 환경을 위해 [Application Insights SDK를 클라우드 서비스 앱의 각 역할에 추가](app-insights-cloudservices.md)하거나 [VM에서 실행할 앱](app-insights-get-started.md)에 추가합니다. 그 후 진단하고 표시할 진단 데이터를 동일한 Application Insights 리소스에 보냅니다.

또는 SDK를 사용하지 않으려면(예를 들어, 앱이 이미 라이브 상태인 경우), Azure 포털에서 [새로운 Application Insights 리소스를 만듭니다](app-insights-create-new-resource.md). **Azure 진단**을 응용 프로그램 종류로 선택합니다.


## Azure 진단을 Application Insights에 보내기

앱 프로젝트를 업데이트할 수 있는 경우에는 Visual Studio에서 각 역할을 선택하고 해당 속성을 선택한 다음 구성 탭에서 **진단 데이터를 Application Insights로 보내기**를 선택합니다.

앱이 이미 라이브 상태이면 Visual Studio의 서버 탐색기 또는 클라우드 서비스 탐색기를 사용하여 앱의 속성을 엽니다. **진단 데이터를 Application Insights로 보내기**를 선택합니다.

매번 생성한 Application Insights 리소스에 대한 세부 정보를 묻는 메시지가 표시됩니다.

[클라우드 서비스 앱에 대해 Application Insights를 설정하는 방법에 대해 자세히 알아봅니다](app-insights-cloudservices.md).

## Azure 진단 어댑터 구성

Application Insights에 보내는 로그의 일부를 선택하려는 경우에만 계속 읽으십시오. 기본적으로 Microsoft Azure 이벤트, 성능 카운터, 앱에서 System.Diagnostics.Trace로 추적 호출을 비롯한 모든 것이 전송됩니다.

Azure 진단은 데이터를 Azure 저장소 테이블에 저장합니다. 그러나 또한 Azure 진단 확장 1.5 이상을 사용하는 경우 구성에서 "싱크" 및 "채널"을 구성하여 모든 데이터 또는 데이터의 하위 집합을 Application Insights로 파이프할 수 있습니다.

### Application Insights를 싱크로 구성

역할 속성을 이용하여 “진단 데이터를 Application Insights로 보내기”를 설정하는 경우, Azure SDK(2.8 이상)는 `<SinksConfig>` 요소를 역할의 공용 [Azure 진단 구성 파일](https://msdn.microsoft.com/library/azure/dn782207.aspx)에 추가합니다.

`<SinksConfig>`는 Azure 진단 데이터를 보낼 수 있는 추가적인 싱크를 정의합니다. `SinksConfig`의 예는 다음과 같습니다.

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`ApplicationInsights` 요소는 Azure 진단 데이터가 전송될 Application Insights 리소스를 식별하는 계측 키를 지정합니다. 리소스를 선택하면 `APPINSIGHTS_INSTRUMENTATIONKEY` 서비스 구성을 근거로 자동으로 채워집니다. (수동으로 설정하려면, 리소스의 Essentials 드롭다운에서 키를 가져옵니다.)

`Channels`는 싱크로 보낼 데이터를 정의합니다. 채널은 필터처럼 작동합니다. `loglevel` 특성을 사용하면 채널이 보내는 로그 수준을 지정할 수 있습니다. 사용 가능한 값은 `{Verbose, Information, Warning, Error, Critical}`입니다.

### 데이터를 싱크에 보내기

DiagnosticMonitorConfiguration 노드에 sinks 특성을 추가하여 데이터를 Application Insights 싱크에 보냅니다. sinks 요소를 각 노드에 추가하는 작업은 해당 노드 및 모든 노드에서 지정된 싱크로 전송할 수 있도록 수집하려는 데이터를 지정합니다.

예를 들어, Azure SDK에서 생성되는 기본값은 모든 Azure 진단 데이터를 보냅니다.

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">
```

하지만 오류 로그만 보내려면, 싱크 이름을 채널 이름으로 한정합니다.

```xml

    <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">
```

정의한 싱크 이름과 위에서 정의한 채널 이름을 함께 사용한다는 점에 유의합니다.

Application Insights에 Verbose 응용 프로그램 로그만 보내려면, `Logs` 노드에 sinks 특성을 추가해야 합니다.

```xml

    <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>
```

또한 계층 구조에서 다양한 수준의 구성에 여러 싱크를 포함할 수 있습니다. 이 경우에 계층 구조의 최상위 수준에서 지정된 싱크는 전역 설정이며 개별 요소에 지정된 싱크는 전역 설정에 재정의와 같은 역할을 합니다.

다음은 Application Insights에 모든 오류를 보내는(`DiagnosticMonitorConfiguration` 노드에 구성된) 공용 구성 파일 및 응용 프로그램 로그에 대한 Verbose 수준 로그(`Logs` 노드에 구성된)의 전체 예입니다.

```xml

    <WadCfg>
     <DiagnosticMonitorConfiguration overallQuotaInMB="4096"
       sinks="ApplicationInsights.MyTopDiagData"> <!-- All info below sent to this channel -->
      <DiagnosticInfrastructureLogs />
      <PerformanceCounters>
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="ApplicationInsights.MyLogData/>
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
        <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
      </PerformanceCounters>
      <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Application!*" />
      </WindowsEventLog>
      <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose"
            sinks="ApplicationInsights.MyLogData"/>
       <!-- This specific info sent to this channel -->
     </DiagnosticMonitorConfiguration>

     <SinksConfig>
      <Sink name="ApplicationInsights">
        <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```

![](./media/app-insights-azure-diagnostics/diagnostics-publicconfig.png)

이 기능을 사용할 경우 주의해야 할 몇 가지 제한 사항이 있습니다.

* 채널은 성능 카운터가 아닌 로그 형식으로만 작업할 수 있다는 의미입니다. 성능 카운터 요소를 사용하여 채널을 지정하는 경우 무시됩니다.
* 채널에 대한 로그 수준은 Azure 진단에서 수집되는 로그 수준을 초과할 수 없습니다. 예를 들어 로그 요소에서 응용 프로그램 로그 오류를 수집하고 Application Insight 동기화에 자세한 정보 로그를 보내려고 시도할 수 없습니다. scheduledTransferLogLevelFilter 특성은 항상 싱크에 전송하려는 로그와 같거나 더 많은 로그를 수집해야 합니다.
* Application Insights에 Azure 진단 확장에서 수집된 Blob 데이터를 보낼 수 없습니다. 예를 들어 Directories 노드에 지정된 모든 항목입니다. 크래시 덤프의 경우 실제 크래시 덤프는 Blob 저장소에 보내지고 크래시 덤프가 생성된 알림이 Application Insights에 전송됩니다.

## 관련된 항목

* [Application Insights로 Azure 클라우드 서비스 모니터링](app-insights-cloudservices.md)
* [PowerShell을 사용하여 Azure 진단을 Application Insights에 보내기](app-insights-powershell-azure-diagnostics.md)
* [Azure 진단 구성 파일](https://msdn.microsoft.com/library/azure/dn782207.aspx)

<!---HONumber=AcomDC_0413_2016-->