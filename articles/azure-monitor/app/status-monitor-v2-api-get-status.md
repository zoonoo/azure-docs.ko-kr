---
title: Azure 응용 프로그램 인사이트 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 응용 프로그램 인사이트 모니터링 상태 받기. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 159dab4a228c822ef62c45c9ccceff638a9bea45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671259"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>애플리케이션 인사이트 에이전트 API: 애플리케이션 인사이트 모니터링 상태 얻기

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>설명

이 cmdlet은 상태 모니터에 대한 문제 해결 정보를 제공합니다.
이 cmdlet을 사용하여 모니터링 상태, PowerShell 모듈 버전을 조사하고 실행 중인 프로세스를 검사합니다.
이 cmdlet은 모니터링에 필요한 주요 파일에 대한 버전 정보와 정보를 보고합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.

## <a name="examples"></a>예

### <a name="example-application-status"></a>예: 응용 프로그램 상태

명령을 `Get-ApplicationInsightsMonitoringStatus` 실행하여 웹 사이트의 모니터링 상태를 표시합니다.

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

이 예제에서는;
- **컴퓨터 식별자는** 서버를 고유하게 식별하는 데 사용되는 익명 ID입니다. 지원 요청을 만드는 경우 서버에 대한 로그를 찾으려면 이 ID가 필요합니다.
- **IIS에서 기본 웹 사이트가** 중지됨
- **데모웹앱111은** IIS에서 시작되었지만 요청을 받지 못했습니다. 이 보고서는 실행 중인 프로세스가 없음을 보여 주며(ProcessId: 찾을 수 없음).
- **DemoWebApp22가** 실행 중이며 모니터링중입니다(계측: true). 사용자 구성에 따라 계측 키 xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx123이 이 사이트에 일치했습니다.
- **DemoWebApp333은** 애플리케이션 인사이트 SDK를 사용하여 수동으로 계측되었습니다. 상태 모니터가 SDK를 감지했으며 이 사이트를 모니터링하지 않습니다.


### <a name="example-powershell-module-information"></a>예: PowerShell 모듈 정보

명령을 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 실행하여 현재 모듈에 대한 정보를 표시합니다.

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

### <a name="example-runtime-status"></a>예: 런타임 상태

계측된 컴퓨터에서 프로세스를 검사하여 모든 DLL이 로드되었는지 확인할 수 있습니다. 모니터링이 작동하는 경우 최소 12개의 DLL을 로드해야 합니다.

명령 `Get-ApplicationInsightsMonitoringStatus -InspectProcess`실행 :


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

기본적으로 이 cmdlet은 웹 응용 프로그램의 모니터링 상태를 보고합니다.
이 옵션을 사용하여 응용 프로그램이 성공적으로 계측되었는지 검토합니다.
사이트에 일치시킨 계측 키를 검토할 수도 있습니다.


### <a name="-powershellmodule"></a>- 파워쉘모듈
**선택 사항입니다**. 이 스위치를 사용하여 모니터링에 필요한 DLL의 버전 번호와 경로를 보고합니다.
응용 프로그램 인사이트 SDK를 포함하여 DLL의 버전을 식별해야 하는 경우 이 옵션을 사용합니다.

### <a name="-inspectprocess"></a>- 검사 과정

**선택 사항입니다**. 이 스위치를 사용하여 IIS가 실행 되고 있는지 여부를 보고합니다.
또한 외부 도구를 다운로드하여 필요한 DLL이 IIS 런타임에 로드되는지 확인합니다.


어떤 이유로든 이 프로세스가 실패하면 다음 명령을 수동으로 실행할 수 있습니다.
- 아이스레셋.exe/상태
- [핸들64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "계측 엔진 AI. 애플리케이션인사이트"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "계측 엔진 AI 애플리케이션인사이트"


### <a name="-force"></a>-Force

**선택 사항입니다**. 검사 프로세스에만 사용됩니다. 이 스위치를 사용하여 추가 도구를 다운로드하기 전에 나타나는 사용자 프롬프트를 건너뜁니다.


## <a name="next-steps"></a>다음 단계

 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
