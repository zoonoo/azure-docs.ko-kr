---
title: 'Azure 상태 모니터 v2 API 참조: 추적 시작 | Microsoft Docs'
description: V2 API 참조를 상태 모니터 합니다. 시작-추적. 상태 모니터 및 Application Insights SDK에서 ETW 로그를 수집 합니다.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3060a48e297082f5b70c8bd96030fe519f6e8b6f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326302"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace"></a>상태 모니터 v2 API: ApplicationInsightsMonitoringTrace

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>설명

코드 없는 attach 런타임에서 [ETW 이벤트](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) 를 수집 합니다. 이 cmdlet은 [Perfview](https://github.com/microsoft/perfview)를 실행 하는 대신 사용할 수 있습니다.

수집 된 이벤트는 실시간으로 콘솔에 출력 되 고 ETL 파일에 저장 됩니다. 추가 조사를 위해 [Perfview](https://github.com/microsoft/perfview) 에서 출력 ETL 파일을 열 수 있습니다.

이 cmdlet은 제한 시간 (기본값 5 분)에 도달할 때까지 실행 되거나 수동으로 중지 됩니다 (`Ctrl + C`).

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.

## <a name="examples"></a>예

### <a name="how-to-collect-events"></a>이벤트를 수집 하는 방법

일반적으로 응용 프로그램을 계측 하지 않는 이유를 조사 하기 위해 이벤트를 수집 하도록 요청 합니다.

코드 없는 attach 런타임은 IIS가 시작 될 때와 응용 프로그램이 시작 될 때 ETW 이벤트를 내보냅니다.

이러한 이벤트를 수집 하려면:
1. 관리자 권한이 있는 cmd 콘솔에서를 실행 `iisreset /stop` 하 여 IIS 및 모든 웹 앱을 해제 합니다.
2. 이 cmdlet 실행
3. 관리자 권한이 있는 cmd 콘솔에서를 실행 `iisreset /start` 하 여 IIS를 시작 합니다.
4. 앱으로 이동 해 보세요.
5. 앱 로드가 완료 되 면 수동으로 중지 (`Ctrl + C`) 하거나 시간 초과를 기다릴 수 있습니다.

### <a name="what-events-to-collect"></a>수집할 이벤트

이벤트를 수집할 때 세 가지 옵션이 있습니다.
1. Application Insights SDK에서 `-CollectSdkEvents` 내보낸 이벤트를 수집 하려면 스위치를 사용 합니다.
2. 스위치 `-CollectRedfieldEvents` 를 사용 하 여 상태 모니터 및 Redfield 런타임에서 내보낸 이벤트를 수집 합니다. 이러한 로그는 IIS 및 응용 프로그램 시작을 진단할 때 유용 합니다.
3. 두 스위치를 모두 사용 하 여 두 이벤트 유형을 모두 수집 합니다.
4. 기본적으로 스위치를 지정 하지 않으면 두 이벤트 유형이 모두 수집 됩니다.


## <a name="parameters"></a>매개 변수

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**선택** 이 매개 변수를 사용 하 여이 스크립트에서 이벤트를 수집 하는 기간을 설정 합니다. 기본값은 5분입니다.

### <a name="-logdirectory"></a>-LogDirectory
**선택** 이 스위치를 사용 하 여 ETL 파일의 출력 디렉터리를 설정 합니다. 기본적으로이 파일은 PowerShell 모듈 디렉터리에 생성 됩니다. 전체 경로는 스크립트를 실행 하는 동안 표시 됩니다.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**선택** 이 스위치를 사용 하 여 Application Insights SDK 이벤트를 수집 합니다.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**선택** 이 스위치를 사용 하 여 상태 모니터 및 Redfield 런타임에서 이벤트를 수집 합니다.

### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 출력 합니다.



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

- 여기에서 추가 문제 해결 단계를 검토 합니다. https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- [API 참조](status-monitor-v2-overview.md#powershell-api-reference) 를 검토 하 여 누락 된 매개 변수에 대해 알아보세요.
- 추가 도움이 필요 하면 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)에서 문의할 수 있습니다.



 상태 모니터 v2를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 상태 모니터 v2 [문제를 해결](status-monitor-v2-troubleshoot.md) 하세요.
 - [구성을 가져와서](status-monitor-v2-api-get-config.md) 설정이 올바르게 기록 되었는지 확인 합니다.
 - 모니터링을 검사할 [상태를 가져옵니다](status-monitor-v2-api-get-status.md) .
