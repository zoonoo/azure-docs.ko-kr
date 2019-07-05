---
title: 'Azure 상태 모니터 v2 API 참조: 추적 시작 | Microsoft Docs'
description: 상태 모니터 v2 API 참조입니다. 시작-추적 합니다. 상태 모니터 및 Application Insights SDK에서 ETW 로그를 수집 합니다.
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
ms.openlocfilehash: 186324e6a93f90fd04323074e5dc883e3e271725
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478993"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace-v031-alpha"></a>상태 모니터 v2 API: 시작-ApplicationInsightsMonitoringTrace (v0.3.1 알파)

이 문서에서는 설명의 구성원임을 확인 하는 cmdlet를 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="description"></a>설명

수집 [ETW 이벤트](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) 는 코드에서 런타임 연결입니다. 이 cmdlet은 실행 하는 대안 [PerfView](https://github.com/microsoft/perfview)합니다.

수집 된 이벤트의 실시간 및 ETL 파일에 저장 된 콘솔에 인쇄 됩니다. 출력 ETL 파일을 열 수 있습니다 [PerfView](https://github.com/microsoft/perfview) 추가로 조사 하기 위해.

이 cmdlet를 수동으로 중지 되거나 시간 제한 기간 (기본값: 5 분)에 도달할 때까지 실행 됩니다 (`Ctrl + C`).

> [!IMPORTANT] 
> 이 cmdlet은 관리자 권한으로 PowerShell 세션에 필요합니다.

## <a name="examples"></a>예

### <a name="how-to-collect-events"></a>이벤트를 수집 하는 방법

일반적으로 응용 프로그램 계측 되지 이유를 조사 하는 이벤트를 수집 하는 주시기 바랍니다.

코드 없는 연결 런타임은 IIS가 시작 될 때 및 응용 프로그램 시작 되 면 ETW 이벤트를 내보냅니다.

에 이러한 이벤트를 수집 합니다.
1. 관리자 권한으로 cmd 콘솔에서 실행 `iisreset /stop` IIS 및 모든 웹 앱을 해제 하려면.
2. 이 cmdlet을 실행
3. 관리자 권한으로 cmd 콘솔에서 실행 `iisreset /start` IIS를 시작 합니다.
4. 앱으로 이동 하려고 합니다.
5. 앱 로드가 완료 된 후 중지할 수 있습니다. 수동으로 (`Ctrl + C`) 하거나 제한 시간까지 기다립니다.

### <a name="what-events-to-collect"></a>이벤트 수집

이벤트를 수집 하는 경우 세 가지 옵션이 있습니다.
1. 스위치를 사용 하 여 `-CollectSdkEvents` Application Insights SDK에서 내보낸 이벤트를 수집 합니다.
2. 스위치를 사용 하 여 `-CollectRedfieldEvents` 상태 모니터 및 Redfield 런타임에서 내보내는 이벤트를 수집 하도록 합니다. 이러한 로그는 IIS 진단 하는 경우에 유용한 응용 프로그램을 시작 합니다.
3. 이벤트 유형 모두를 수집 하려면 두 스위치를 사용 합니다.
4. 기본적으로 수집 되는 이벤트 유형을 모두 없는 스위치를 지정 합니다.


## <a name="parameters"></a>매개 변수

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**선택** 이 스크립트는 이벤트를 수집 하는 기간을 설정 하려면이 매개 변수를 사용 합니다. 기본값은 5분입니다.

### <a name="-logdirectory"></a>-LogDirectory
**선택** ETL 파일의 출력 디렉터리를 설정 하려면이 스위치를 사용 합니다. 기본적으로이 파일을 PowerShell Modules 디렉터리에 만들어집니다. 전체 경로 스크립트를 실행 하는 동안 표시 됩니다.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**선택** Application Insights SDK 이벤트를 수집 하려면이 스위치를 사용 합니다.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**선택** 상태 모니터 및 Redfield 런타임에서 이벤트를 수집 하려면이 스위치를 사용 합니다.

### <a name="-verbose"></a>-Verbose
**일반적인 매개 변수입니다.** 자세한 로그를 출력 하려면이 스위치를 사용 합니다.



## <a name="output"></a>Output


### <a name="example-of-application-startup-logs"></a>응용 프로그램 시작 작업 로그의 예
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

- 추가 문제 해결 단계를 검토 합니다. https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- 검토 합니다 [API 참조](status-monitor-v2-overview.md#powershell-api-reference) 하지 않았을 수 있습니다 매개 변수에 대 한 자세한 합니다.
- 추가 도움이 필요한 경우 연락 하시면 온 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)합니다.



 상태 모니터 v2 사용 하 여 더 수행 합니다.
 - 가이드를 사용 하 여 [해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
 - [구성 가져오기](status-monitor-v2-api-get-config.md) 설정을 올바르게 기록 된를 확인 합니다.
 - [상태를 가져오려면](status-monitor-v2-api-get-status.md) 모니터링 검사 합니다.
