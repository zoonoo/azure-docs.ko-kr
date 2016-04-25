<properties
   pageTitle="Application Insights에 데이터를 보내도록 Azure 진단 구성 | Microsoft Azure"
   description="Application Insights에 데이터를 보내도록 Azure 진단 공용 구성을 업데이트 합니다."
   services="multiple"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="" />
<tags
   ms.service="application-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />

# Application Insights에 데이터를 보내도록 Azure 진단 구성

Azure 진단은 데이터를 Azure 저장소 테이블에 저장합니다. 그러나 또한 Azure 진단 확장 1.5 이상을 사용하는 경우 구성에서 "싱크" 및 "채널"을 구성하여 모든 데이터 또는 데이터의 하위 집합을 Application Insights로 파이프할 수 있습니다.

이 문서에서는 Azure 진단 확장에 대한 공용 구성을 만드는 방법을 설명하여 데이터를 Application Insights에 보내도록 구성됩니다.

## Application Insights를 싱크로 구성

Azure 진단 확장 1.5는 공용 구성에서 **<SinksConfig>** 요소를 도입합니다. Azure 진단 데이터를 보낼 수 있는 추가 *싱크*를 정의합니다. 이 작업의 일환으로 Azure 진단 데이터를 전송하려는 Application Insights 리소스의 세부 정보를 지정할 수 있습니다 **<SinksConfig>**. **SinksConfig** 예는 다음과 같습니다.

	<SinksConfig>
        <Sink name="ApplicationInsights">
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>
          <Channels>
            <Channel logLevel="Error" name="MyTopDiagData"  />
            <Channel logLevel="Verbose" name="MyLogData"  />
          </Channels>
        </Sink>
      </SinksConfig>

**싱크** 요소의 경우 *이름* 특성은 싱크를 고유하게 명명하는 데 사용할 문자열 값을 지정합니다. **ApplicationInsights** 요소는 Azure 진단 데이터를 보낼 Application Insights 리소스의 계측 키를 지정합니다. 기존 Application Insights 리소스가 없는 경우 리소스 만들기 및 계측 키 가져오기에 대한 자세한 내용은 [새 Application Insights 리소스 만들기](./application-insights/app-insights-create-new-resource.md)를 참조하세요.

Azure SDK 2.8을 사용하여 클라우드 서비스 프로젝트를 개발하는 경우 클라우드 서비스 프로젝트를 패키징할 때 이 계측 키는 **APPINSIGHTS\_INSTRUMENTATIONKEY** 서비스 구성 설정에 기반하여 공용 구성에서 자동으로 채워집니다. [Application Insights를 Azure 진단과 함께 사용하여 클라우드 서비스 문제 해결](./cloud-services/cloud-services-dotnet-diagnostics-applicationinsights.md)을 참조하세요.

**채널** 요소를 사용하면 싱크로 전송 될 데이터에 대한 하나 이상의 **채널** 요소를 정의할 수 있습니다. 채널은 필터처럼 작동하고 채널을 사용하면 싱크를 보내려는 특정 로그 수준을 선택할 수 있습니다. 예를 들어 자세한 정보를 수집하고 저장소에 보낼 수 있지만 오류의 로그 수준을 정의하도록 선택할 수 있고 로그를 해당 채널을 통해 보낼 때 오류 로그는 해당 싱크에 전송됩니다. **채널**의 경우 *이름* 특성은 해당 채널을 고유하게 명명하는 데 사용됩니다. *loglevel* 특성을 사용하면 채널이 허용하는 로그 수준을 지정할 수 있습니다. 정보가 적은 순서대로 사용 가능한 로그 수준은 다음과 같습니다.
 - 자세한 정보 표시
 - 정보
 - Warning
 - 오류
 - 중요

## Application Insights 싱크로 데이터 보내기
Application Insights 싱크를 정의하면 *싱크* 특성을 **DiagnosticMonitorConfiguration** 노드의 요소에 추가하여 데이터를 해당 싱크에 보낼 수 있습니다. *싱크* 요소를 각 노드에 추가하는 작업은 해당 노드 및 모든 노드에서 지정된 싱크를 전송할 수 있도록 수집하려는 데이터를 지정합니다.

예를 들어 Azure 진단에서 수집된 모든 데이터를 전송하려는 경우 *싱크* 특성을 직접 **DiagnosticMonitorConfiguration** 노드에 추가할 수 있습니다. *싱크* 값을 **SinkConfig**에 지정된 싱크 이름에 설정합니다.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights">

오류 로그를 Application Insights 싱크에 보내려는 경우 *싱크* 값이 마침표(".")로 구분하는 채널 이름 다음에 싱크 이름이 오도록 설정할 수 있습니다. 예를 들어 오류 로그를 Application Insights에 보내려면 위의 SinksConfig에 정의된 MyTopDiagdata 채널을 사용합니다.

	<DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="ApplicationInsights.MyTopDiagdata">

자세한 정보 응용 프로그램 로그를 Application Insights에 보내려는 경우 *싱크* 특성을 **로그** 노드에 추가합니다.

	<Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Verbose" sinks="ApplicationInsights.MyLogData"/>

또한 계층 구조에서 다양한 수준의 구성에 여러 싱크를 포함할 수 있습니다. 이 경우에 계층 구조의 최상위 수준에서 지정된 싱크는 전역 설정이며 개별 요소에 지정된 싱크는 전역 설정에 재정의와 같은 역할을 합니다.

Application Insights(**DiagnosticMonitorConfiguration** 노드에 지정됨) 이외에 응용 프로그램 로그에 대한 자세한 정보 수준 로그(**로그** 노드에 지정됨)에 오류를 전송하는 공용 구성 파일의 전체 예제는 다음과 같습니다.

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
                sinks="ApplicationInsights.MyLogData"/> <!-- This specific info sent to this channel -->
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

![진단 공용 구성](./media/azure-diagnostics-configure-applicationinsights/diagnostics-publicconfig.png)

이 기능을 사용할 경우 주의해야 할 몇 가지 제한 사항이 있습니다.

- 채널은 성능 카운터가 아닌 로그 형식으로만 작업할 수 있다는 의미입니다. 성능 카운터 요소를 사용하여 채널을 지정하는 경우 무시됩니다.
- 채널에 대한 로그 수준은 Azure 진단에서 수집되는 로그 수준을 초과할 수 없습니다. 예를 들어 로그 요소에서 응용 프로그램 로그 오류를 수집하고 Application Insight 싱크에 자세한 정보 로그를 보내려고 시도할 수 없습니다. *scheduledTransferLogLevelFilter* 특성은 항상 싱크를 전송하려는 로그와 같거나 더 많은 로그를 수집해야 합니다.
- Application Insights에 Azure 진단 확장에서 수집된 Blob 데이터를 보낼 수 없습니다. 예를 들어 *디렉터리* 노드에 지정된 모든 항목입니다. 크래시 덤프의 경우 실제 크래시 덤프는 Blob 저장소에 보내지고 크래시 덤프가 생성된 알림이 Application Insights에 전송됩니다.


## 다음 단계

- [PowerShell](./cloud-services/cloud-services-diagnostics-powershell.md)을 사용하여 응용 프로그램에 대한 Azure 진단 확장을 사용하도록 설정합니다. 
- [Visual Studio](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)를 사용하여 응용 프로그램에 대한 Azure 진단 확장을 사용하도록 설정합니다.

<!---HONumber=AcomDC_0413_2016-->