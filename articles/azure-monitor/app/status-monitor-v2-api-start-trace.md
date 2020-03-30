---
title: Azure 응용 프로그램 인사이트 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 시작 추적. 상태 모니터 및 애플리케이션 인사이트 SDK에서 ETW 로그를 수집합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671225"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>애플리케이션 인사이트 에이전트 API: 시작-애플리케이션 인사이트모니터링트레이스

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>설명

코드없는 첨부 런타임에서 [ETW 이벤트를](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) 수집합니다. 이 cmdlet은 [PerfView를](https://github.com/microsoft/perfview)실행하는 대안입니다.

수집된 이벤트는 실시간으로 콘솔에 인쇄되어 ETL 파일에 저장됩니다. 추가 조사를 위해 [PerfView에서](https://github.com/microsoft/perfview) 출력 ETL 파일을 열 수 있습니다.

이 cmdlet은 시간 초과 기간(기본값 5분)에 도달하거나`Ctrl + C`수동으로 중지될 때까지 실행됩니다( ).

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.

## <a name="examples"></a>예

### <a name="how-to-collect-events"></a>이벤트 수집 방법

일반적으로 응용 프로그램이 계측되지 않는 이유를 조사하기 위해 이벤트를 수집하도록 요청합니다.

코드리스 첨부 런타임은 IIS가 시작되고 응용 프로그램이 시작될 때 ETW 이벤트를 내보올 것입니다.

이러한 이벤트를 수집하려면 다음 단계를 수행하십시오.
1. 관리자 권한이 있는 cmd 콘솔에서 `iisreset /stop` IIS 및 모든 웹 앱을 끄려면 실행합니다.
2. 이 cmdlet 실행
3. 관리자 권한이 있는 cmd 콘솔에서 `iisreset /start` IIS 시작을 실행합니다.
4. 앱을 찾아보십시오.
5. 앱 로드가 완료되면 수동으로 중지하거나`Ctrl + C`시간 설정을 기다릴 수 있습니다.

### <a name="what-events-to-collect"></a>수집할 이벤트

이벤트를 수집할 때 세 가지 옵션이 있습니다.
1. 스위치를 `-CollectSdkEvents` 사용하여 응용 프로그램 인사이트 SDK에서 내보낸 이벤트를 수집합니다.
2. 스위치를 `-CollectRedfieldEvents` 사용하여 상태 모니터 및 Redfield 런타임에서 내보낸 이벤트를 수집합니다. 이러한 로그는 IIS 및 응용 프로그램 시작을 진단할 때 유용합니다.
3. 두 스위치를 모두 사용하여 두 이벤트 유형을 모두 수집합니다.
4. 기본적으로 스위치가 지정되지 않으면 두 이벤트 유형이 모두 수집됩니다.


## <a name="parameters"></a>매개 변수

### <a name="-maxdurationinminutes"></a>-맥스지속시간
**선택적.** 이 매개 변수를 사용하여 이 스크립트에서 이벤트를 수집하는 데 걸리는 길이를 설정합니다. 기본값은 5분입니다.

### <a name="-logdirectory"></a>-로그 디렉토리
**선택적.** 이 스위치를 사용하여 ETL 파일의 출력 디렉토리를 설정합니다. 기본적으로 이 파일은 PowerShell 모듈 디렉토리에서 만들어집니다. 스크립트 실행 중에 전체 경로가 표시됩니다.


### <a name="-collectsdkevents"></a>-수집이벤트
**선택적.** 이 스위치를 사용하여 응용 프로그램 인사이트 SDK 이벤트를 수집합니다.

### <a name="-collectredfieldevents"></a>-콜렉스레드필드이벤트
**선택적.** 이 스위치를 사용하여 상태 모니터 및 Redfield 런타임에서 이벤트를 수집합니다.

### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 출력합니다.



## <a name="output"></a>출력


### <a name="example-of-application-startup-logs"></a>응용 프로그램 시작 로그의 예
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>다음 단계

추가 문제 해결:

- 여기에서 추가 문제 해결 단계를 검토합니다.https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- API [참조를](status-monitor-v2-overview.md#powershell-api-reference) 검토하여 놓친 매개 변수에 대해 알아봅니다.
- 추가 도움이 필요한 경우 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)로 문의할 수 있습니다.



 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
 - 설정이 올바르게 기록되었는지 확인하기 위해 [구성을 가져옵니다.](status-monitor-v2-api-get-config.md)
 - 모니터링을 검사할 [상태를 가져옵니다.](status-monitor-v2-api-get-status.md)
