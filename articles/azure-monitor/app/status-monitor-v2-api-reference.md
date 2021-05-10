---
title: Azure Application Insights .Net Agent API 참조
description: Application Insights Agent API 참조. 웹 사이트 재배포 없이 웹 사이트 성능을 모니터링합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7c5c2f1d055ec6270892873548872b20b17b4158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566900"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights Agent API 참조

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet을 설명합니다.

> [!NOTE] 
> - 시작하려면 계측 키가 필요합니다. 자세한 내용은 [리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)를 참조하세요.
> - 이 cmdlet을 사용하려면 라이선스 및 개인정보처리방침을 검토하고 동의해야 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용하려면 관리자 권한 및 확장된 실행 정책이 있는 PowerShell 세션이 필요합니다. 자세한 내용은 [확장된 실행 정책이 있는 관리자로 PowerShell 실행](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)을 참조하세요.
> - 이 cmdlet을 사용하려면 라이선스 및 개인정보처리방침을 검토하고 동의해야 합니다.
> - 계측 엔진은 추가 오버헤드를 추가하며 기본적으로 해제되어 있습니다.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

일부 레지스트리 키를 설정하여 계측 엔진을 사용하도록 설정합니다.
변경 내용을 적용하려면 IIS를 다시 시작하세요.

계측 엔진은 .NET SDK에 의해 수집된 데이터를 보완할 수 있습니다.
관리형 프로세스의 실행을 설명하는 이벤트 및 메시지를 수집합니다. 이러한 이벤트와 메시지에는 종속성 결과 코드, HTTP 동사 및 [SQL 명령 텍스트](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)가 포함됩니다.

다음의 경우 계측 엔진을 사용하도록 설정합니다.
- Enable cmdlet을 사용하여 모니터링을 이미 사용하도록 설정했지만 계측 엔진을 사용하도록 설정하지 않았습니다.
- .NET SDK를 사용하여 수동으로 앱을 계측하고 원격 분석 데이터를 추가로 수집하려고 합니다.

### <a name="examples"></a>예제

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>매개 변수

#### <a name="-acceptlicense"></a>-AcceptLicense
**선택 사항입니다.** 이 스위치를 사용하여 헤드리스 설치에서 라이선스 및 개인정보처리방침에 동의합니다.

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수.** 이 스위치를 사용하여 자세한 로그를 출력합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>계측 엔진을 사용하도록 설정하는 예제 출력

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

대상 컴퓨터에서 IIS 앱에 대한 코드리스 연결 모니터링을 사용하도록 설정합니다.

이 cmdlet은 IIS applicationHost.config를 수정하고 일부 레지스트리 키를 설정합니다.
또한 각 앱에서 사용하는 계측 키를 정의하는 applicationinsights.ikey.config 파일을 만듭니다.
IIS는 시작 시 RedfieldModule를 로드합니다. 그러면 애플리케이션이 시작될 때 애플리케이션에 Application Insights SDK가 삽입됩니다.
IIS를 다시 시작하여 변경 내용을 적용합니다.

모니터링을 사용하도록 설정한 후에는 [라이브 메트릭](live-stream.md)을 사용하여 앱이 원격 분석을 전송하고 있는지 신속하게 확인하는 것이 좋습니다.

### <a name="examples"></a>예제

#### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키 사용 예제
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵이 있는 예제
이 예제에서는 다음이 적용됩니다.
- `MachineFilter`는 `'.*'` 와일드카드를 사용하여 현재 컴퓨터와 매칭합니다.
- `AppFilter='WebAppExclude'`에서는 `null` 계측 키를 제공합니다. 지정된 앱은 계측되지 않습니다.
- `AppFilter='WebAppOne'`은 지정된 앱에 고유한 계측 키를 할당합니다.
- `AppFilter='WebAppTwo'`는 지정된 앱에 고유한 계측 키를 할당합니다.
- 마지막으로, `AppFilter`는 `'.*'` 와일드카드를 사용하여 이전 규칙과 일치하지 않는 모든 웹앱을 검색하고 기본 계측 키를 할당합니다.
- 가독성을 위해 공백을 추가합니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```

> [!NOTE]
> 이 컨텍스트에서 AppFilter 이름이 혼동될 수 있으므로 `AppFilter`는 애플리케이션 이름 regex 필터(IIS의 .net의 경우 HostingEnvironment.SiteName)를 설정합니다. `VirtualPathFilter`는 가상 경로 regex 필터(IIS의 .Net의 경우, HostingEnvironment.ApplicationVirtualPath)를 설정합니다. 단일 앱을 계측하려면 VirtualPathFilter를 다음과 같이 사용해야 합니다. `Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap @(@{VirtualPathFilter="^/MyAppName$"; InstrumentationSettings=@{InstrumentationKey='<your ikey>'}})`

### <a name="parameters"></a>매개 변수

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수 사항입니다.** 이 매개 변수를 사용하여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공합니다.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수 사항입니다.** 이 매개 변수를 사용하여 여러 계측 키를 제공하고 각 앱에서 사용하는 계측 키의 매핑을 제공합니다.
`MachineFilter`를 설정하여 여러 컴퓨터에 대한 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공된 순서대로 규칙을 매칭합니다. 따라서 가장 구체적인 규칙을 먼저 지정하고 가장 일반적인 규칙을 마지막에 지정해야 합니다.

##### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **Machinefilter** 는 컴퓨터 또는 VM 이름의 필수 C# regex입니다.
    - '.*'은 모두 일치
    - 'ComputerName'은 정확한 이름이 지정된 컴퓨터에만 일치합니다.
- **Appfilter** 는 IIS 사이트 이름의 필수 C# regex입니다. [get-iissite](/powershell/module/iisadministration/get-iissite) 명령을 실행하여 서버에서 사이트 목록을 가져올 수 있습니다.
    - '.*'은 모두 일치
    - 'SiteName'은 정확한 이름이 지정된 IIS 사이트만 일치합니다.
- **InstrumentationKey** 는 위의 두 필터와 일치하는 앱의 모니터링을 사용하도록 설정하는 데 필요합니다.
    - 모니터링을 제외하는 규칙을 정의하려면 이 값을 null로 둡니다.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**선택 사항입니다.** 이 스위치를 사용하여 계측 엔진에서 관리형 프로세스를 실행하는 동안 발생하는 상황에 대한 이벤트와 메시지를 수집합니다. 이러한 이벤트와 메시지에는 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트가 포함됩니다.

계측 엔진은 오버헤드를 추가하며 기본적으로 해제되어 있습니다.

#### <a name="-acceptlicense"></a>-AcceptLicense
**선택 사항입니다.** 이 스위치를 사용하여 헤드리스 설치에서 라이선스 및 개인정보처리방침에 동의합니다.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
웹 서버 클러스터가 있는 경우 [공유 구성](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)을 사용할 수 있습니다.
HttpModule은 이 공유 구성에 삽입할 수 없습니다.
이 스크립트는 추가 설치 단계가 필요하다는 메시지가 표시되면서 실패합니다.
이 검사를 무시하고 필수 구성 요소 설치를 계속하려면 이 스위치를 사용하세요. 자세한 내용은 [known conflict-with-iis-shared-configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)을 참조하세요.

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수.** 이 스위치를 사용하여 자세한 로그를 표시합니다.

#### <a name="-whatif"></a>-WhatIf 
**일반 매개 변수.** 이 스위치를 사용하면 실제로 모니터링을 사용하지 않고 입력 매개 변수를 테스트하고 유효성을 검사할 수 있습니다.

### <a name="output"></a>출력

#### <a name="example-output-from-a-successful-enablement"></a>성공적인 사용 예제 출력

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

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

일부 레지스트리 키를 제거하여 계측 엔진을 사용하지 않도록 설정합니다.
변경 내용을 적용하려면 IIS를 다시 시작하세요.

### <a name="examples"></a>예제

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>매개 변수 

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수.** 이 스위치를 사용하여 자세한 로그를 출력합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>계측 엔진을 사용하지 않도록 설정하는 예제 출력

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

대상 컴퓨터에서 모니터링을 사용하지 않도록 설정합니다.
이 cmdlet은 IIS applicationHost.config 편집 내용을 제거하고 레지스트리 키를 제거합니다.

### <a name="examples"></a>예제

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>매개 변수 

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수.** 이 스위치를 사용하여 자세한 로그를 표시합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-disabling-monitoring"></a>모니터링을 사용하지 않도록 설정하는 예제 출력

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


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

구성 파일을 가져오고 값을 콘솔에 출력합니다.

### <a name="examples"></a>예제

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>매개 변수

매개 변수가 필요하지 않습니다.

### <a name="output"></a>출력


##### <a name="example-output-from-reading-the-config-file"></a>구성 파일 읽기의 예제 출력

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

이 cmdlet은 상태 모니터에 대한 문제 해결 정보를 제공합니다.
이 cmdlet을 사용하여 PowerShell 모듈의 모니터링 상태와 버전을 조사하고 실행 중인 프로세스를 검사할 수 있습니다.
이 cmdlet은 모니터링에 필요한 키 파일과 버전 정보를 보고합니다.

### <a name="examples"></a>예제

#### <a name="example-application-status"></a>예제: 애플리케이션 상태

`Get-ApplicationInsightsMonitoringStatus` 명령을 실행하여 웹 사이트의 모니터링 상태를 표시합니다.

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

이 예제에서는 다음이 적용됩니다.
- **머신 식별자** 는 서버를 고유하게 식별하는 데 사용되는 익명 ID입니다. 지원 요청을 만드는 경우, 서버에 대한 로그를 찾기 위해 이 ID가 필요합니다.
- IIS에서 **기본 웹 사이트** 가 중지되었습니다.
- **DemoWebApp111** 이 IIS에서 시작되었지만, 요청을 받지 못했습니다. 이 보고서는 실행 중인 프로세스가 없음을 보여 줍니다(ProcessId: 찾을 수 없음).
- **DemoWebApp222** 가 실행 중이며 모니터링되고 있습니다(계측됨: true). 사용자 구성에 따라 이 사이트에 대해 xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 계측 키가 일치했습니다.
- Application Insights SDK를 사용하여 **DemoWebApp333** 을 수동으로 계측했습니다. 상태 모니터에서 해당 SDK를 검색했고 이 사이트를 모니터링하지 않습니다.


#### <a name="example-powershell-module-information"></a>예제: PowerShell 모듈 정보

`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 명령을 실행하여 현재 모듈에 대한 정보를 표시합니다.

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

#### <a name="example-runtime-status"></a>예제: 런타임 상태

계측된 컴퓨터에서 프로세스를 검사하여 모든 DLL이 로드되었는지 확인할 수 있습니다. 모니터링이 작동하는 경우 12개 이상의 DLL을 로드해야 합니다.

`Get-ApplicationInsightsMonitoringStatus -InspectProcess` 명령을 실행합니다.


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

기본적으로 이 cmdlet은 웹 애플리케이션의 모니터링 상태를 보고합니다.
애플리케이션이 성공적으로 계측되었는지 검토하려면 이 옵션을 사용합니다.
사이트와 일치하는 계측 키를 검토할 수도 있습니다.


#### <a name="-powershellmodule"></a>-PowerShellModule
**선택 사항입니다**. 이 스위치를 사용하여 모니터링에 필요한 DLL의 버전 번호와 경로를 보고합니다.
Application Insights SDK를 포함하여 DLL의 버전을 식별해야 하는 경우 이 옵션을 사용합니다.

#### <a name="-inspectprocess"></a>-InspectProcess

**선택 사항입니다**. 이 스위치를 사용하여 IIS가 실행 중인지 보고합니다.
또한 필요한 DLL이 IIS 런타임에 로드되는지 확인하기 위해 외부 도구도 다운로드합니다.


어떤 이유로든 이 프로세스가 실패하는 경우 다음 명령을 수동으로 실행할 수 있습니다.
- iisreset.exe /status
- [handle64.exe](/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**선택 사항입니다**. InspectProcess에만 사용됩니다. 이 스위치를 사용하여 추가 도구가 다운로드되기 전에 표시되는 사용자 프롬프트를 건너뛸 수 있습니다.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

전체를 다시 설치하지 않고 구성 파일을 설정합니다.
IIS를 다시 시작하여 변경 내용을 적용합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.


### <a name="examples"></a>예제

#### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키 사용 예제
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵이 있는 예제
이 예제에서는 다음이 적용됩니다.
- `MachineFilter`는 `'.*'` 와일드카드를 사용하여 현재 컴퓨터와 매칭합니다.
- `AppFilter='WebAppExclude'`에서는 `null` 계측 키를 제공합니다. 지정된 앱은 계측되지 않습니다.
- `AppFilter='WebAppOne'`은 지정된 앱에 고유한 계측 키를 할당합니다.
- `AppFilter='WebAppTwo'`는 지정된 앱에 고유한 계측 키를 할당합니다.
- 마지막으로, `AppFilter`는 `'.*'` 와일드카드를 사용하여 이전 규칙과 일치하지 않는 모든 웹앱을 검색하고 기본 계측 키를 할당합니다.
- 가독성을 위해 공백을 추가합니다.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>매개 변수

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수 사항입니다.** 이 매개 변수를 사용하여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공합니다.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수 사항입니다.** 이 매개 변수를 사용하여 여러 계측 키를 제공하고 각 앱에서 사용하는 계측 키의 매핑을 제공합니다.
`MachineFilter`를 설정하여 여러 컴퓨터에 대한 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공된 순서대로 규칙을 매칭합니다. 따라서 가장 구체적인 규칙을 먼저 지정하고 가장 일반적인 규칙을 마지막에 지정해야 합니다.

##### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **Machinefilter** 는 컴퓨터 또는 VM 이름의 필수 C# regex입니다.
    - '.*'은 모두 일치
    - 'ComputerName'은 지정한 이름을 사용하는 컴퓨터에만 일치합니다.
- **Appfilter** 는 컴퓨터 또는 VM 이름의 필수 C# regex입니다.
    - '.*'은 모두 일치
    - 'ApplicationName'은 지정된 이름의 IIS 앱에만 일치합니다.
- **InstrumentationKey** 는 위의 두 필터와 일치하는 앱의 모니터링을 사용하도록 설정하는 데 필요합니다.
    - 모니터링을 제외하는 규칙을 정의하려면 이 값을 null로 둡니다.


#### <a name="-verbose"></a>-Verbose
**일반 매개 변수.** 이 스위치를 사용하여 자세한 로그를 표시합니다.


### <a name="output"></a>출력

기본적으로는 출력이 없습니다.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey를 통해 구성 파일을 설정하는 방법의 예제 세부 정보 출력

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap를 통해 구성 파일을 설정하는 방법의 예제 세부 정보 출력

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

코드리스 연결 런타임에서 [ETW 이벤트](/windows/desktop/etw/event-tracing-portal)를 수집합니다. 이 cmdlet은 [Perfview](https://github.com/microsoft/perfview)를 실행하는 대신 사용할 수 있습니다.

수집된 이벤트는 실시간으로 콘솔에 출력되고 ETL 파일에 저장됩니다. 추가 조사를 위해 [Perfview](https://github.com/microsoft/perfview)에서 출력 ETL 파일을 열 수 있습니다.

이 cmdlet은 시간 제한(기본값 5분)에 도달할 때까지 실행되거나 수동으로 중지됩니다(`Ctrl + C`).

### <a name="examples"></a>예제

#### <a name="how-to-collect-events"></a>이벤트 수집 방법

일반적으로 애플리케이션을 계측하지 않는 이유를 조사하기 위해 이벤트를 수집하도록 요청합니다.

코드리스 연결 런타임은 IIS가 시작될 때와 애플리케이션이 시작될 때 ETW 이벤트를 내보냅니다.

이러한 이벤트를 수집하려면 다음을 수행합니다.
1. 관리자 권한이 있는 cmd 콘솔에서 `iisreset /stop`를 실행하여 IIS 및 모든 웹앱을 해제합니다.
2. 이 cmdlet 실행
3. 관리자 권한이 있는 cmd 콘솔에서 `iisreset /start`를 실행하여 IIS를 시작합니다.
4. 앱으로 이동해 보세요.
5. 앱 로드가 완료되면 수동으로 중지(`Ctrl + C`)하거나 시간 제한에 도달할 때까지 기다릴 수 있습니다.

#### <a name="what-events-to-collect"></a>수집할 이벤트

이벤트를 수집할 때 세 가지 옵션이 있습니다.
1. `-CollectSdkEvents` 스위치를 사용하여 Application Insights SDK에서 내보낸 이벤트를 수집합니다.
2. `-CollectRedfieldEvents` 스위치를 사용하여 상태 모니터 및 Redfield 런타임에서 내보낸 이벤트를 수집합니다. 이러한 로그는 IIS 및 애플리케이션 시작을 진단할 때 유용합니다.
3. 두 스위치를 모두 사용하여 두 이벤트 유형을 모두 수집합니다.
4. 기본적으로 스위치를 지정하지 않으면 두 이벤트 유형이 모두 수집됩니다.


### <a name="parameters"></a>매개 변수

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**선택 사항입니다.** 이 매개 변수를 사용하여 이 스크립트에서 이벤트를 수집하는 기간을 설정합니다. 기본값은 5분입니다.

#### <a name="-logdirectory"></a>-LogDirectory
**선택 사항입니다.** 이 스위치를 사용하여 ETL 파일의 출력 디렉터리를 설정합니다. 기본적으로 이 파일은 PowerShell 모듈 디렉터리에 생성됩니다. 전체 경로는 스크립트를 실행하는 동안 표시됩니다.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**선택 사항입니다.** 이 스위치를 사용하여 Application Insights SDK 이벤트를 수집합니다.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**선택 사항입니다.** 이 스위치를 사용하여 상태 모니터 및 Redfield 런타임에서 이벤트를 수집합니다.

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수.** 이 스위치를 사용하여 자세한 로그를 출력합니다.



### <a name="output"></a>출력


#### <a name="example-of-application-startup-logs"></a>애플리케이션 시작 로그의 예
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
 - [메트릭을 탐색하여](../essentials/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
- [이벤트와 로그를 검색하여](./diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리를 위해 [분석](../logs/log-query-overview.md)을 사용합니다.
- [대시보드를 만듭니다](./overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가](./javascript.md)하여 웹 페이지 코드에서 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- [Application Insights SDK를 코드에 추가하여](./asp-net.md) 추적 및 로그 호출을 삽입할 수 있도록 합니다.
 
 Application Insights Agent를 사용하여 더 많은 작업 수행:
 - 가이드를 사용하여 Application Insights Agent [문제를 해결](status-monitor-v2-troubleshoot.md)할 수 있습니다.

