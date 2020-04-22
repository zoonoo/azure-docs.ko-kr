---
title: Azure 응용 프로그램 인사이트 .Net 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733675"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure 모니터 응용 프로그램 인사이트 에이전트 API 참조

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

> [!NOTE] 
> - 시작하려면 계측 키가 필요합니다. 자세한 내용은 [리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)를 참조하십시오.
> - 이 cmdlet은 당사의 라이선스 및 개인 정보 취급 방침을 검토하고 수락하도록 요구합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션과 높은 실행 정책이 필요합니다. 자세한 내용은 [PowerShell 실행 을 높은 실행 정책이 있는 관리자로 실행](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)을 참조하십시오.
> - 이 cmdlet은 당사의 라이선스 및 개인 정보 취급 방침을 검토하고 수락하도록 요구합니다.
> - 계측 엔진은 추가 오버헤드를 추가하고 기본적으로 꺼져 있습니다.


## <a name="enable-instrumentationengine"></a>인에이블 계측 엔진

일부 레지스트리 키를 설정하여 계측 엔진을 활성화합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

계측 엔진은 .NET SDK에서 수집한 데이터를 보완할 수 있습니다.
관리되는 프로세스의 실행을 설명하는 이벤트 및 메시지를 수집합니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 [SQL 명령 텍스트가](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)포함됩니다.

다음과 같은 경우 계측 엔진을 활성화합니다.
- 인에이블 cmdlet을 사용하여 모니터링을 이미 활성화했지만 계측 엔진을 사용하도록 설정하지 않았습니다.
- .NET SDK를 사용하여 앱을 수동으로 계측했으며 추가 원격 분석을 수집하려고 합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>매개 변수

#### <a name="-acceptlicense"></a>- 수락라이센스
**(선택 사항)** 이 스위치를 사용하여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침을 수락합니다.

#### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 출력합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>계측 엔진을 성공적으로 활성화한 결과물 예제

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>애플리케이션 인사이트 모니터링 지원

대상 컴퓨터에서 IIS 앱을 코드 없이 첨부모니터링할 수 있습니다.

이 cmdlet은 IIS applicationHost.config를 수정하고 일부 레지스트리 키를 설정합니다.
또한 각 앱에서 사용하는 계측 키를 정의하는 applicationinsights.ikey.config 파일을 만듭니다.
IIS는 시작 시 RedfieldModule을 로드하여 응용 프로그램이 시작될 때 응용 프로그램 인사이트 SDK를 응용 프로그램에 삽입합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

모니터링을 사용하도록 설정하면 라이브 [메트릭을](live-stream.md) 사용하여 앱에서 원격 분석을 보내는지 빠르게 확인하는 것이 좋습니다.

### <a name="examples"></a>예

#### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키의 예
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵의 예
이 예제에 대한 설명:
- `MachineFilter`와일드카드를 사용하여 현재 `'.*'` 컴퓨터와 일치합니다.
- `AppFilter='WebAppExclude'`계측 `null` 키를 제공합니다. 지정된 앱은 계측되지 않습니다.
- `AppFilter='WebAppOne'`지정된 앱에 고유한 계측 키를 할당합니다.
- `AppFilter='WebAppTwo'`지정된 앱에 고유한 계측 키를 할당합니다.
- `AppFilter` 마지막으로 와일드카드를 `'.*'` 사용하여 이전 규칙과 일치하지 않는 모든 웹 앱과 일치시키고 기본 계측 키를 할당합니다.
- 가독성을 위해 공백이 추가됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>매개 변수

#### <a name="-instrumentationkey"></a>- 계측키
**필수.** 이 매개 변수를 사용하여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공합니다.

#### <a name="-instrumentationkeymap"></a>-계측키맵
**필수.** 이 매개 변수를 사용하여 여러 계측 키와 각 앱에서 사용하는 계측 키매핑을 제공합니다.
을 설정하여 `MachineFilter`여러 컴퓨터에 대한 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공되는 순서대로 규칙과 일치합니다. 따라서 가장 구체적인 규칙을 먼저 지정하고 가장 일반적인 규칙을 마지막으로 지정해야 합니다.

##### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter는** 컴퓨터 또는 VM 이름의 필수 C# 정규식입니다.
    - '.*'는 모든
    - 'ComputerName'은 지정된 정확한 이름의 컴퓨터만 일치합니다.
- **AppFilter는** IIS 사이트 이름의 필수 C# 정규법입니다. [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)명령을 실행하여 서버의 사이트 목록을 얻을 수 있습니다.
    - '.*'는 모든
    - '사이트이름'은 IIS 사이트와 정확한 이름과 일치합니다.
- **계측키는** 앞의 두 필터와 일치하는 앱을 모니터링하는 데 필요합니다.
    - 모니터링을 제외하는 규칙을 정의하려면 이 값을 null로 둡니다.


#### <a name="-enableinstrumentationengine"></a>-인에이블계측 엔진
**(선택 사항)** 이 스위치를 사용하여 계측 엔진이 관리되는 프로세스를 실행하는 동안 발생하는 일에 대한 이벤트 및 메시지를 수집할 수 있습니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트가 포함됩니다.

계측 엔진은 오버헤드를 추가하고 기본적으로 꺼져 있습니다.

#### <a name="-acceptlicense"></a>- 수락라이센스
**(선택 사항)** 이 스위치를 사용하여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침을 수락합니다.

#### <a name="-ignoresharedconfig"></a>-무시공유구성
웹 서버 클러스터가 있는 경우 [공유 구성을](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)사용하고 있을 수 있습니다.
HttpModule은 이 공유 구성에 삽입할 수 없습니다.
이 스크립트는 추가 설치 단계가 필요하다는 메시지와 함께 실패합니다.
이 스위치를 사용하여 이 검사를 무시하고 필수 구성 조건을 계속 설치합니다. 자세한 내용은 [알려진 iis 공유 구성을 참조하십시오.](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 표시합니다.

#### <a name="-whatif"></a>-WhatIf 
**공통 매개 변수입니다.** 이 스위치를 사용하여 실제로 모니터링을 사용하지 않고 입력 매개 변수를 테스트하고 유효성을 검사합니다.

### <a name="output"></a>출력

#### <a name="example-output-from-a-successful-enablement"></a>성공적인 인에이블먼트의 예제 출력

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>비활성화 계측엔진

일부 레지스트리 키를 제거하여 계측 엔진을 사용하지 않도록 설정합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>매개 변수 

#### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 출력합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>계측 엔진을 성공적으로 비활성화한 결과

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>비활성화-애플리케이션 인사이트모니터링

대상 컴퓨터에서 모니터링을 비활성화합니다.
이 cmdlet은 IIS applicationHost.config에 대한 편집을 제거하고 레지스트리 키를 제거합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>매개 변수 

#### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 표시합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-disabling-monitoring"></a>모니터링을 성공적으로 비활성화한 예제 출력

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>응용 프로그램 인사이트 모니터링구성

구성 파일을 가져옵니다 및 콘솔에 값을 인쇄 합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>매개 변수

매개 변수가 필요하지 않습니다.

### <a name="output"></a>출력


##### <a name="example-output-from-reading-the-config-file"></a>구성 파일 읽기에서 출력 예

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>응용 프로그램 인사이트 모니터링 상태 얻기

이 cmdlet은 상태 모니터에 대한 문제 해결 정보를 제공합니다.
이 cmdlet을 사용하여 모니터링 상태, PowerShell 모듈 버전을 조사하고 실행 중인 프로세스를 검사합니다.
이 cmdlet은 모니터링에 필요한 주요 파일에 대한 버전 정보와 정보를 보고합니다.

### <a name="examples"></a>예

#### <a name="example-application-status"></a>예: 응용 프로그램 상태

명령을 `Get-ApplicationInsightsMonitoringStatus` 실행하여 웹 사이트의 모니터링 상태를 표시합니다.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

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


#### <a name="example-powershell-module-information"></a>예: PowerShell 모듈 정보

명령을 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 실행하여 현재 모듈에 대한 정보를 표시합니다.

```powershell

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

#### <a name="example-runtime-status"></a>예: 런타임 상태

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

### <a name="parameters"></a>매개 변수

#### <a name="no-parameters"></a>(매개 변수 없음)

기본적으로 이 cmdlet은 웹 응용 프로그램의 모니터링 상태를 보고합니다.
이 옵션을 사용하여 응용 프로그램이 성공적으로 계측되었는지 검토합니다.
사이트에 일치시킨 계측 키를 검토할 수도 있습니다.


#### <a name="-powershellmodule"></a>- 파워쉘모듈
**선택 사항입니다**. 이 스위치를 사용하여 모니터링에 필요한 DLL의 버전 번호와 경로를 보고합니다.
응용 프로그램 인사이트 SDK를 포함하여 DLL의 버전을 식별해야 하는 경우 이 옵션을 사용합니다.

#### <a name="-inspectprocess"></a>- 검사 과정

**선택 사항입니다**. 이 스위치를 사용하여 IIS가 실행 되고 있는지 여부를 보고합니다.
또한 외부 도구를 다운로드하여 필요한 DLL이 IIS 런타임에 로드되는지 확인합니다.


어떤 이유로든 이 프로세스가 실패하면 다음 명령을 수동으로 실행할 수 있습니다.
- 아이스레셋.exe/상태
- [핸들64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "계측 엔진 AI. 애플리케이션인사이트"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "계측 엔진 AI 애플리케이션인사이트"


#### <a name="-force"></a>-Force

**선택 사항입니다**. 검사 프로세스에만 사용됩니다. 이 스위치를 사용하여 추가 도구를 다운로드하기 전에 나타나는 사용자 프롬프트를 건너뜁니다.


## <a name="set-applicationinsightsmonitoringconfig"></a>설정 응용 프로그램 인사이트모니터링구성

전체 재설치를 수행하지 않고 구성 파일을 설정합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.


### <a name="examples"></a>예

#### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키의 예
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵의 예
이 예제에 대한 설명:
- `MachineFilter`와일드카드를 사용하여 현재 `'.*'` 컴퓨터와 일치합니다.
- `AppFilter='WebAppExclude'`계측 `null` 키를 제공합니다. 지정된 앱은 계측되지 않습니다.
- `AppFilter='WebAppOne'`지정된 앱에 고유한 계측 키를 할당합니다.
- `AppFilter='WebAppTwo'`지정된 앱에 고유한 계측 키를 할당합니다.
- `AppFilter` 마지막으로 와일드카드를 `'.*'` 사용하여 이전 규칙과 일치하지 않는 모든 웹 앱과 일치시키고 기본 계측 키를 할당합니다.
- 가독성을 위해 공백이 추가됩니다.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>매개 변수

#### <a name="-instrumentationkey"></a>- 계측키
**필수.** 이 매개 변수를 사용하여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공합니다.

#### <a name="-instrumentationkeymap"></a>-계측키맵
**필수.** 이 매개 변수를 사용하여 여러 계측 키와 각 앱에서 사용하는 계측 키매핑을 제공합니다.
을 설정하여 `MachineFilter`여러 컴퓨터에 대한 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공되는 순서대로 규칙과 일치합니다. 따라서 가장 구체적인 규칙을 먼저 지정하고 가장 일반적인 규칙을 마지막으로 지정해야 합니다.

##### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter는** 컴퓨터 또는 VM 이름의 필수 C# 정규식입니다.
    - '.*'는 모든
    - 'ComputerName'은 지정된 이름의 컴퓨터만 일치합니다.
- **AppFilter는** 컴퓨터 또는 VM 이름의 필수 C# 정규법입니다.
    - '.*'는 모든
    - 'ApplicationName'은 지정된 이름의 IIS 앱만 일치합니다.
- **계측키는** 앞의 두 필터와 일치하는 앱을 모니터링하는 데 필요합니다.
    - 모니터링을 제외하는 규칙을 정의하려면 이 값을 null로 둡니다.


#### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 표시합니다.


### <a name="output"></a>출력

기본적으로 출력이 없습니다.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey를 통해 구성 파일을 설정하여 출력을 자세한 예

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap을 통해 구성 파일 설정에서 출력을 자세한 예

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>시작-응용 프로그램 인사이트모니터링추적

코드없는 첨부 런타임에서 [ETW 이벤트를](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) 수집합니다. 이 cmdlet은 [PerfView를](https://github.com/microsoft/perfview)실행하는 대안입니다.

수집된 이벤트는 실시간으로 콘솔에 인쇄되어 ETL 파일에 저장됩니다. 추가 조사를 위해 [PerfView에서](https://github.com/microsoft/perfview) 출력 ETL 파일을 열 수 있습니다.

이 cmdlet은 시간 초과 기간(기본값 5분)에 도달하거나`Ctrl + C`수동으로 중지될 때까지 실행됩니다( ).

### <a name="examples"></a>예

#### <a name="how-to-collect-events"></a>이벤트 수집 방법

일반적으로 응용 프로그램이 계측되지 않는 이유를 조사하기 위해 이벤트를 수집하도록 요청합니다.

코드리스 첨부 런타임은 IIS가 시작되고 응용 프로그램이 시작될 때 ETW 이벤트를 내보올 것입니다.

이러한 이벤트를 수집하려면 다음 단계를 수행하십시오.
1. 관리자 권한이 있는 cmd 콘솔에서 `iisreset /stop` IIS 및 모든 웹 앱을 끄려면 실행합니다.
2. 이 cmdlet 실행
3. 관리자 권한이 있는 cmd 콘솔에서 `iisreset /start` IIS 시작을 실행합니다.
4. 앱을 찾아보십시오.
5. 앱 로드가 완료되면 수동으로 중지하거나`Ctrl + C`시간 설정을 기다릴 수 있습니다.

#### <a name="what-events-to-collect"></a>수집할 이벤트

이벤트를 수집할 때 세 가지 옵션이 있습니다.
1. 스위치를 `-CollectSdkEvents` 사용하여 응용 프로그램 인사이트 SDK에서 내보낸 이벤트를 수집합니다.
2. 스위치를 `-CollectRedfieldEvents` 사용하여 상태 모니터 및 Redfield 런타임에서 내보낸 이벤트를 수집합니다. 이러한 로그는 IIS 및 응용 프로그램 시작을 진단할 때 유용합니다.
3. 두 스위치를 모두 사용하여 두 이벤트 유형을 모두 수집합니다.
4. 기본적으로 스위치가 지정되지 않으면 두 이벤트 유형이 모두 수집됩니다.


### <a name="parameters"></a>매개 변수

#### <a name="-maxdurationinminutes"></a>-맥스지속시간
**(선택 사항)** 이 매개 변수를 사용하여 이 스크립트에서 이벤트를 수집하는 데 걸리는 길이를 설정합니다. 기본값은 5분입니다.

#### <a name="-logdirectory"></a>-로그 디렉토리
**(선택 사항)** 이 스위치를 사용하여 ETL 파일의 출력 디렉토리를 설정합니다. 기본적으로 이 파일은 PowerShell 모듈 디렉토리에서 만들어집니다. 스크립트 실행 중에 전체 경로가 표시됩니다.


#### <a name="-collectsdkevents"></a>-수집이벤트
**(선택 사항)** 이 스위치를 사용하여 응용 프로그램 인사이트 SDK 이벤트를 수집합니다.

#### <a name="-collectredfieldevents"></a>-콜렉스레드필드이벤트
**(선택 사항)** 이 스위치를 사용하여 상태 모니터 및 Redfield 런타임에서 이벤트를 수집합니다.

#### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 출력합니다.



### <a name="output"></a>출력


#### <a name="example-of-application-startup-logs"></a>응용 프로그램 시작 로그의 예
```powershell
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

  원격 분석 보기:
 - [메트릭을 탐색하여](../../azure-monitor/app/metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리에 [대한 분석을](../../azure-monitor/app/analytics.md) 사용합니다.
- [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK를 추가합니다.](../../azure-monitor/app/asp-net.md)
 
 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)






