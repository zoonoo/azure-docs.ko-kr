---
title: 'Azure 상태 모니터 v2 API 참조: 상태 가져오기 | Microsoft Docs'
description: V2 API 참조를 상태 모니터 합니다. Get-ApplicationInsightsMonitoringStatus. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: 50703ede205b42032e9618c1b16b0de2bd7f3a23
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424133"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus"></a>상태 모니터 v2 API: ApplicationInsightsMonitoringStatus

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>Description

이 cmdlet은 상태 모니터에 대 한 문제 해결 정보를 제공 합니다.
이 cmdlet을 사용 하 여 PowerShell 모듈의 모니터링 상태와 버전을 조사 하 고 실행 중인 프로세스를 검사할 수 있습니다.
이 cmdlet은 버전 정보 및 모니터링에 필요한 키 파일에 대 한 정보를 보고 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.

## <a name="examples"></a>예

### <a name="example-application-status"></a>예제: 애플리케이션 상태

명령을 `Get-ApplicationInsightsMonitoringStatus` 실행 하 여 웹 사이트의 모니터링 상태를 표시 합니다.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

이 예제에서는
- **컴퓨터 식별자** 는 서버를 고유 하 게 식별 하는 데 사용 되는 익명 ID입니다. 지원 요청을 만들 경우 서버에 대 한 로그를 찾을 수 있는이 ID가 필요 합니다.
- IIS에서 **기본 웹 사이트가** 중지 되었습니다.
- **DemoWebApp111** 가 IIS에서 시작 되었지만 요청을 받지 못했습니다. 이 보고서는 실행 중인 프로세스가 없음을 보여 줍니다 (ProcessId: 찾을 수 없음).
- **DemoWebApp222** 이 실행 중 이며 모니터링 되 고 있습니다 (계측 됨: true). 사용자 구성에 따라이 사이트에 대해 xxxxxxxxx123 계측 키가 일치 했습니다.
- Application Insights SDK를 사용 하 여 **DemoWebApp333** 을 수동으로 계측 했습니다. SDK를 검색 상태 모니터이 사이트를 모니터링 하지 않습니다.


### <a name="example-powershell-module-information"></a>예제: PowerShell 모듈 정보

명령을 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 실행 하 여 현재 모듈에 대 한 정보를 표시 합니다.

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

### <a name="example-runtime-status"></a>예제: 런타임 상태

계측 된 컴퓨터에서 프로세스를 검사 하 여 모든 Dll이 로드 되었는지 확인할 수 있습니다. 모니터링이 작동 하는 경우 12 개 이상의 Dll을 로드 해야 합니다.

다음 명령을 `Get-ApplicationInsightsMonitoringStatus -InspectProcess`실행 합니다.


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>매개 변수

### <a name="no-parameters"></a>(매개 변수 없음)

기본적으로이 cmdlet은 웹 응용 프로그램의 모니터링 상태를 보고 합니다.
응용 프로그램이 성공적으로 계측 되었는지 검토 하려면이 옵션을 사용 합니다.
사이트와 일치 하는 계측 키를 검토할 수도 있습니다.


### <a name="-powershellmodule"></a>-PowerShellModule
**선택 사항**입니다. 이 스위치를 사용 하 여 모니터링에 필요한 Dll의 버전 번호와 경로를 보고 합니다.
Application Insights SDK를 포함 하 여 DLL의 버전을 식별 해야 하는 경우이 옵션을 사용 합니다.

### <a name="-inspectprocess"></a>-InspectProcess

**선택 사항**입니다. 이 스위치를 사용 하 여 IIS가 실행 중인지 여부를 보고 합니다.
또한 필요한 Dll이 IIS 런타임에 로드 되는지 확인 하기 위해 외부 도구도 다운로드 합니다.


어떤 이유로 든이 프로세스가 실패 하는 경우 다음 명령을 수동으로 실행할 수 있습니다.
- iisreset.exe /status
- [handle64](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp.exe | findstr/I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp.exe | findstr/I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**선택 사항**입니다. InspectProcess에만 사용 됩니다. 이 스위치를 사용 하 여 추가 도구가 다운로드 되기 전에 표시 되는 사용자 프롬프트를 건너뛸 수 있습니다.


## <a name="next-steps"></a>다음 단계

 상태 모니터 v2를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 상태 모니터 v2 [문제를 해결](status-monitor-v2-troubleshoot.md) 하세요.
