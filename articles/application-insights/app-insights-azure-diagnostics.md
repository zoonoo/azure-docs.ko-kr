---
title: "Application Insights에 Azure 진단 로그 보내기 | Microsoft Docs"
description: "Application Insights 포털로 전송되는 Azure 클라우드 서비스 진단 로그에 대한 세부 정보를 구성합니다."
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge
ms.assetid: a67dd20a-fc5d-4391-ba63-bfe164fb62f7
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2843ff25c118afe5d5421b322b529e9a9f2d5036


---
# <a name="configure-azure-diagnostic-logging-to-application-insights"></a>Application Insights에 대해 Azure 진단 로깅 구성
Microsoft Azure에서 클라우드 서비스 프로젝트나 가상 컴퓨터를 설정하는 경우 [Azure는 진단 로그를 생성할 수 있습니다](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md). 이것을 Application Insights로 보내도록 설정하여 Application Insights SDK에 의해 앱 내부에서 전송되는 진단 및 사용량 원격 분석 자료와 함께 분석할 수 있습니다. Azure 로그는 시작, 중지, 충돌은 물론 성능 카운터와 같은 앱에 대한 관리의 이벤트를 포함합니다. 로그는 앱의 호출을 System.Diagnostics.Trace에 포함합니다.

이 문서는 진단 캡처에 대한 구성을 자세히 설명합니다.

Visual Studio에 Azure SDK 2.8 이상이 설치되어 있어야 합니다.

## <a name="get-an-application-insights-resource"></a>Application Insights 리소스 가져오기
최상의 환경을 위해 [Application Insights SDK를 클라우드 서비스 앱의 각 역할에 추가](app-insights-cloudservices.md)하거나 [VM에서 실행할 앱](app-insights-overview.md)에 추가합니다. 그 후 진단하고 표시할 진단 데이터를 동일한 Application Insights 리소스에 보냅니다.

또는 SDK를 사용하지 않으려면(예를 들어, 앱이 이미 라이브 상태인 경우), Azure 포털에서 [새로운 Application Insights 리소스를 만듭니다](app-insights-create-new-resource.md) . **Azure 진단** 을 응용 프로그램 종류로 선택합니다.

## <a name="send-azure-diagnostics-to-application-insights"></a>Azure 진단을 Application Insights에 보내기
앱 프로젝트를 업데이트할 수 있는 경우에는 Visual Studio에서 각 역할을 선택하고 해당 속성을 선택한 다음 구성 탭에서 **진단 데이터를 Application Insights로 보내기**를 선택합니다.

또는 게시 명령을 사용하여 앱을 업로드하는 경우 진단 페이지에서 Application Insights 옵션을 선택할 수 있습니다.

앱이 이미 라이브 상태이면 Visual Studio의 서버 탐색기 또는 클라우드 서비스 탐색기를 사용하여 앱의 속성을 엽니다. **진단 데이터를 Application Insights로 보내기**를 선택합니다.

매번 생성한 Application Insights 리소스에 대한 세부 정보를 묻는 메시지가 표시됩니다.

개별 역할에 대한 데이터를 전송하여 리소스를 구분하는 것이 좋습니다. [대시보드](app-insights-dashboards.md)를 만들어 메트릭 차트를 포털에 나란히 표시할 수 있습니다.

[클라우드 서비스 앱에 대해 Application Insights를 설정하는 방법에 대해 자세히 알아봅니다](app-insights-cloudservices.md).

## <a name="configuring-the-azure-diagnostics-adapter"></a>Azure 진단 어댑터 구성
Application Insights에 보내는 로그의 일부를 선택하려는 경우에만 계속 읽으십시오. 기본적으로 Microsoft Azure 이벤트, 성능 카운터, 앱에서 System.Diagnostics.Trace로 추적 호출을 비롯한 모든 것이 전송됩니다.

역할 속성 편집기 또는 게시 마법사에서 진단 옵션을 변경하면 실제로 다음 두 파일 집합의 내용이 변경됩니다.

* 역할 [진단 구성 파일](https://msdn.microsoft.com/library/azure/dn782207.aspx). 솔루션 탐색기의 `<Your Service>/Roles/*/diagnostics.wadcfgx` 아래에서 찾을 수 있습니다.
* 서비스 구성 파일 `ServiceConfiguration.*.cscfg`.

마법사보다 구체적인 옵션을 적용하려면 이러한 파일을 직접 편집합니다. 자세한 내용을 보려면 계속 읽어보세요. 

## <a name="separate-development-and-production-resources"></a>별도 개발 및 프로덕션 리소스
앱의 개발 및 프로덕션 스탬프에서 여러 Application Insights 리소스로 원격 분석 데이터를 보낼 수 있습니다. 이렇게 하면 개발 원격 분석이 실시간 원격 분석으로 과도하게 실행되는 것을 방지할 수 있습니다. 

1. 각 스탬프에 대한 [새 Application Insights 리소스](app-insights-create-new-resource.md)를 만듭니다. 각 리소스의 필수 패키지 탭에서 계측 키를 가져옵니다.
2. 두 개의 .cscfg 파일을 편집하고 서로 다른 계측 키를 삽입합니다.

## <a name="choose-the-priority-levels-to-send"></a>보낼 우선 순위 수준 선택
각 역할에 대한 진단 구성 파일 `diagnostics.wadcfgx`에서 수준별로 로그 메시지를 필터링할 수 있습니다.

### <a name="define-a-sink"></a>싱크 정의
`<SinksConfig>` 는 Azure 진단 데이터를 보낼 수 있는 추가적인 싱크를 정의합니다.  `SinksConfig` 의 예는 다음과 같습니다.

```xml

    <SinksConfig>
     <Sink name="ApplicationInsights">
      <ApplicationInsights/>
      <Channels>
        <Channel logLevel="Error" name="MyTopDiagData"  />
        <Channel logLevel="Verbose" name="MyLogData"  />
      </Channels>
     </Sink>
    </SinksConfig>

```

`Channels`는 싱크로 보낼 데이터 스트림의 이름을 지정합니다. 채널은 필터처럼 작동합니다. `loglevel` 특성을 사용하면 채널이 보내는 로그 수준을 지정할 수 있습니다. 사용 가능한 값은 `{Verbose, Information, Warning, Error, Critical}`입니다.

### <a name="send-data-to-the-sink"></a>데이터를 싱크에 보내기
DiagnosticMonitorConfiguration 노드 또는 하위 노드에 sinks 특성을 추가하여 데이터를 Application Insights 싱크에 보냅니다. sinks 요소를 각 노드에 추가하는 작업은 해당 노드 및 모든 노드에서 지정된 싱크로 전송할 수 있도록 수집하려는 데이터를 지정합니다.

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
       sinks="ApplicationInsights.MyTopDiagData">
       <!-- All info below sent to this channel -->
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
        <ApplicationInsights/>
        <Channels>
          <Channel logLevel="Error" name="MyTopDiagData"  />
          <Channel logLevel="Verbose" name="MyLogData"  />
        </Channels>
      </Sink>
     </SinksConfig>
    </WadCfg>
```


이 기능을 사용할 경우 주의해야 할 몇 가지 제한 사항이 있습니다.

* 채널은 성능 카운터가 아닌 로그 형식으로만 작업할 수 있다는 의미입니다. 성능 카운터 요소를 사용하여 채널을 지정하는 경우 무시됩니다.
* 채널에 대한 로그 수준은 Azure 진단에서 수집되는 로그 수준을 초과할 수 없습니다. 예를 들어 로그 요소에서 응용 프로그램 로그 오류를 수집하고 Application Insight 동기화에 자세한 정보 로그를 보내려고 시도할 수 없습니다. scheduledTransferLogLevelFilter 특성은 항상 싱크에 전송하려는 로그와 같거나 더 많은 로그를 수집해야 합니다.
* Application Insights에 Azure 진단 확장에서 수집된 Blob 데이터를 보낼 수 없습니다. 예를 들어 Directories 노드에 지정된 모든 항목입니다. 크래시 덤프의 경우 실제 크래시 덤프는 Blob 저장소에 보내지고 크래시 덤프가 생성된 알림이 Application Insights에 전송됩니다.

## <a name="next-steps"></a>다음 단계
* [Application Insights로 Azure 클라우드 서비스 모니터링](app-insights-cloudservices.md)
* [PowerShell을 사용하여 Azure 진단을 Application Insights에 보내기](app-insights-powershell-azure-diagnostics.md)
* [Azure 진단 구성 파일](https://msdn.microsoft.com/library/azure/dn782207.aspx)




<!--HONumber=Nov16_HO3-->


