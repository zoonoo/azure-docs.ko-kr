---
title: Azure 애플리케이션 Insights .Net 에이전트 API 참조
description: 에이전트 API 참조를 Application Insights 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733675"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Azure Monitor Application Insights 에이전트 API 참조

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

> [!NOTE] 
> - 시작 하려면 계측 키가 필요 합니다. 자세한 내용은 [리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)를 참조 하세요.
> - 이 cmdlet을 사용 하려면 라이선스 및 개인 정보 취급 방침을 검토 하 고 동의 해야 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한 및 관리자 권한으로 실행 정책이 있는 PowerShell 세션이 필요 합니다. 자세한 내용은 [관리자 권한으로 실행 정책으로 PowerShell 실행](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)을 참조 하세요.
> - 이 cmdlet을 사용 하려면 라이선스 및 개인 정보 취급 방침을 검토 하 고 동의 해야 합니다.
> - 계측 엔진은 추가 오버 헤드를 추가 하며 기본적으로 해제 되어 있습니다.


## <a name="enable-instrumentationengine"></a>InstrumentationEngine

일부 레지스트리 키를 설정 하 여 계측 엔진을 사용 하도록 설정 합니다.
변경 내용을 적용 하려면 IIS를 다시 시작 하십시오.

계측 엔진은 .NET Sdk에 의해 수집 된 데이터를 보완할 수 있습니다.
관리 되는 프로세스의 실행을 설명 하는 이벤트 및 메시지를 수집 합니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 [SQL 명령 텍스트가](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)포함 됩니다.

다음의 경우 계측 엔진을 사용 하도록 설정 합니다.
- Enable cmdlet을 사용 하 여 모니터링을 이미 사용 하도록 설정 했지만 계측 엔진을 사용 하도록 설정 하지 않았습니다.
- 수동으로 .NET Sdk를 사용 하 여 앱을 계측 하 고 추가 원격 분석을 수집 하려고 합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>매개 변수

#### <a name="-acceptlicense"></a>-AcceptLicense
**필드.** 이 스위치를 사용 하 여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침에 동의 합니다.

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 출력 합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>계측 엔진을 사용 하도록 설정 하는 예제 출력

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>ApplicationInsightsMonitoring

대상 컴퓨터에서 IIS 앱에 대 한 코드 없는 연결 모니터링을 사용 하도록 설정 합니다.

이 cmdlet은 IIS Applicationhost.config를 수정 하 고 일부 레지스트리 키를 설정 합니다.
또한 각 앱에서 사용 하는 계측 키를 정의 하는 applicationinsights. ikey .config 파일이 만들어집니다.
IIS는 시작 시 RedfieldModule를 로드 합니다. 그러면 응용 프로그램이 시작 될 때 응용 프로그램에 Application Insights SDK가 삽입 됩니다.
IIS를 다시 시작 하 여 변경 내용을 적용 합니다.

모니터링을 사용 하도록 설정한 후에는 [라이브 메트릭을](live-stream.md) 사용 하 여 앱이 원격 분석을 전송 하 고 있는지 신속 하 게 확인 하는 것이 좋습니다.

### <a name="examples"></a>예

#### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키를 사용 하는 예제
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵이 있는 예제
이 예제에 대한 설명:
- `MachineFilter`와일드 카드를 `'.*'` 사용 하 여 현재 컴퓨터와 일치 합니다.
- `AppFilter='WebAppExclude'`계측 키 `null` 를 제공 합니다. 지정 된 앱은 계측 되지 않습니다.
- `AppFilter='WebAppOne'`지정 된 앱에 고유한 계측 키를 할당 합니다.
- `AppFilter='WebAppTwo'`지정 된 앱에 고유한 계측 키를 할당 합니다.
- `AppFilter` 마지막으로는 `'.*'` 와일드 카드를 사용 하 여 이전 규칙과 일치 하지 않는 모든 웹 앱을 검색 하 고 기본 계측 키를 할당 합니다.
- 가독성을 위해 공백을 추가 합니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>매개 변수

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수.** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공 합니다.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수.** 이 매개 변수를 사용 하 여 여러 계측 키를 제공 하 고 각 앱에서 사용 하는 계측 키의 매핑을 제공 합니다.
을 설정 `MachineFilter`하 여 여러 컴퓨터에 대해 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공 된 순서 대로 규칙에 대해 일치 합니다. 따라서 가장 구체적인 규칙을 먼저 지정 하 고 가장 일반적인 규칙을 마지막에 지정 해야 합니다.

##### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **Machinefilter** 는 컴퓨터 또는 VM 이름의 필수 c # regex입니다.
    - '. * '는 모두 일치 합니다.
    - ' ComputerName '은 정확히 이름이 지정 된 컴퓨터에만 일치 합니다.
- **Appfilter** 는 IIS 사이트 이름의 필수 c # regex입니다. [Get iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)명령을 실행 하 여 서버에서 사이트 목록을 가져올 수 있습니다.
    - '. * '는 모두 일치 합니다.
    - ' SiteName '은 정확한 이름을 지정 하 여 IIS 사이트와 일치 합니다.
- **InstrumentationKey** 는 위의 두 필터와 일치 하는 앱의 모니터링을 사용 하도록 설정 하는 데 필요 합니다.
    - 모니터링을 제외 하는 규칙을 정의 하려면이 값을 null로 둡니다.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**필드.** 이 스위치를 사용 하 여 계측 엔진에서 관리 되는 프로세스를 실행 하는 동안 발생 하는 상황에 대 한 이벤트 및 메시지를 수집할 수 있도록 합니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트가 포함 됩니다.

계측 엔진은 오버 헤드를 추가 하며 기본적으로 해제 되어 있습니다.

#### <a name="-acceptlicense"></a>-AcceptLicense
**필드.** 이 스위치를 사용 하 여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침에 동의 합니다.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
웹 서버 클러스터가 있는 경우 [공유 구성을](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)사용할 수 있습니다.
HttpModule은이 공유 구성에 삽입할 수 없습니다.
이 스크립트는 추가 설치 단계가 필요 하다는 메시지와 함께 실패 합니다.
이 검사를 무시 하 고 필수 구성 요소 설치를 계속 하려면이 스위치를 사용 하십시오. 자세한 내용은 [알려진 충돌 (iis 공유-구성](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) )을 참조 하세요.

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 표시 합니다.

#### <a name="-whatif"></a>-WhatIf 
**일반 매개 변수입니다.** 이 스위치를 사용 하면 실제로 모니터링을 사용 하지 않고 입력 매개 변수를 테스트 하 고 유효성을 검사할 수 있습니다.

### <a name="output"></a>출력

#### <a name="example-output-from-a-successful-enablement"></a>성공적인 활성화의 예제 출력

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

일부 레지스트리 키를 제거 하 여 계측 엔진을 사용 하지 않도록 설정 합니다.
변경 내용을 적용 하려면 IIS를 다시 시작 하십시오.

### <a name="examples"></a>예

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>매개 변수 

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 출력 합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>계측 엔진을 사용 하지 않도록 설정 하는 예제 출력

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

대상 컴퓨터에서 모니터링을 사용 하지 않도록 설정 합니다.
이 cmdlet은 IIS Applicationhost.config의 편집 내용을 제거 하 고 레지스트리 키를 제거 합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>매개 변수 

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 표시 합니다.

### <a name="output"></a>출력


##### <a name="example-output-from-successfully-disabling-monitoring"></a>모니터링을 사용 하지 않도록 설정 하는 예제 출력

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


## <a name="get-applicationinsightsmonitoringconfig"></a>ApplicationInsightsMonitoringConfig

구성 파일을 가져오고 값을 콘솔에 출력 합니다.

### <a name="examples"></a>예

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>매개 변수

매개 변수가 필요 하지 않습니다.

### <a name="output"></a>출력


##### <a name="example-output-from-reading-the-config-file"></a>구성 파일 읽기의 예제 출력

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>ApplicationInsightsMonitoringStatus

이 cmdlet은 상태 모니터에 대 한 문제 해결 정보를 제공 합니다.
이 cmdlet을 사용 하 여 PowerShell 모듈의 모니터링 상태와 버전을 조사 하 고 실행 중인 프로세스를 검사할 수 있습니다.
이 cmdlet은 버전 정보 및 모니터링에 필요한 키 파일에 대 한 정보를 보고 합니다.

### <a name="examples"></a>예

#### <a name="example-application-status"></a>예: 응용 프로그램 상태

명령을 `Get-ApplicationInsightsMonitoringStatus` 실행 하 여 웹 사이트의 모니터링 상태를 표시 합니다.

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

이 예제에서는
- **컴퓨터 식별자** 는 서버를 고유 하 게 식별 하는 데 사용 되는 익명 ID입니다. 지원 요청을 만들 경우 서버에 대 한 로그를 찾을 수 있는이 ID가 필요 합니다.
- IIS에서 **기본 웹 사이트가** 중지 되었습니다.
- **DemoWebApp111** 가 IIS에서 시작 되었지만 요청을 받지 못했습니다. 이 보고서는 실행 중인 프로세스가 없음을 보여 줍니다 (ProcessId: 찾을 수 없음).
- **DemoWebApp222** 이 실행 중 이며 모니터링 되 고 있습니다 (계측 됨: true). 사용자 구성에 따라이 사이트에 대해 xxxxxxxxx123 계측 키가 일치 했습니다.
- Application Insights SDK를 사용 하 여 **DemoWebApp333** 을 수동으로 계측 했습니다. SDK를 검색 상태 모니터이 사이트를 모니터링 하지 않습니다.


#### <a name="example-powershell-module-information"></a>예: PowerShell 모듈 정보

명령을 `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 실행 하 여 현재 모듈에 대 한 정보를 표시 합니다.

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

### <a name="parameters"></a>매개 변수

#### <a name="no-parameters"></a>(매개 변수 없음)

기본적으로이 cmdlet은 웹 응용 프로그램의 모니터링 상태를 보고 합니다.
응용 프로그램이 성공적으로 계측 되었는지 검토 하려면이 옵션을 사용 합니다.
사이트와 일치 하는 계측 키를 검토할 수도 있습니다.


#### <a name="-powershellmodule"></a>-PowerShellModule
**선택 사항입니다**. 이 스위치를 사용 하 여 모니터링에 필요한 Dll의 버전 번호와 경로를 보고 합니다.
Application Insights SDK를 포함 하 여 DLL의 버전을 식별 해야 하는 경우이 옵션을 사용 합니다.

#### <a name="-inspectprocess"></a>-InspectProcess

**선택 사항입니다**. 이 스위치를 사용 하 여 IIS가 실행 중인지 여부를 보고 합니다.
또한 필요한 Dll이 IIS 런타임에 로드 되는지 확인 하기 위해 외부 도구도 다운로드 합니다.


어떤 이유로 든이 프로세스가 실패 하는 경우 다음 명령을 수동으로 실행할 수 있습니다.
- iisreset/status
- [handle64](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp.exe | findstr/I "InstrumentationEngine AI. Applicationinsights.config
- [listdlls64](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp.exe | findstr/I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**선택 사항입니다**. InspectProcess에만 사용 됩니다. 이 스위치를 사용 하 여 추가 도구가 다운로드 되기 전에 표시 되는 사용자 프롬프트를 건너뛸 수 있습니다.


## <a name="set-applicationinsightsmonitoringconfig"></a>ApplicationInsightsMonitoringConfig

전체를 다시 설치 하지 않고 구성 파일을 설정 합니다.
IIS를 다시 시작 하 여 변경 내용을 적용 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.


### <a name="examples"></a>예

#### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키를 사용 하는 예제
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵이 있는 예제
이 예제에 대한 설명:
- `MachineFilter`와일드 카드를 `'.*'` 사용 하 여 현재 컴퓨터와 일치 합니다.
- `AppFilter='WebAppExclude'`계측 키 `null` 를 제공 합니다. 지정 된 앱은 계측 되지 않습니다.
- `AppFilter='WebAppOne'`지정 된 앱에 고유한 계측 키를 할당 합니다.
- `AppFilter='WebAppTwo'`지정 된 앱에 고유한 계측 키를 할당 합니다.
- `AppFilter` 마지막으로는 `'.*'` 와일드 카드를 사용 하 여 이전 규칙과 일치 하지 않는 모든 웹 앱을 검색 하 고 기본 계측 키를 할당 합니다.
- 가독성을 위해 공백을 추가 합니다.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>매개 변수

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수.** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공 합니다.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수.** 이 매개 변수를 사용 하 여 여러 계측 키를 제공 하 고 각 앱에서 사용 하는 계측 키의 매핑을 제공 합니다.
을 설정 `MachineFilter`하 여 여러 컴퓨터에 대해 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공 된 순서 대로 규칙에 대해 일치 합니다. 따라서 가장 구체적인 규칙을 먼저 지정 하 고 가장 일반적인 규칙을 마지막에 지정 해야 합니다.

##### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **Machinefilter** 는 컴퓨터 또는 VM 이름의 필수 c # regex입니다.
    - '. * '는 모두 일치 합니다.
    - ' ComputerName '은 지정한 이름을 가진 컴퓨터에만 일치 합니다.
- **Appfilter** 는 컴퓨터 또는 VM 이름의 필수 c # regex입니다.
    - '. * '는 모두 일치 합니다.
    - ' ApplicationName '은 지정 된 이름의 IIS 앱에만 일치 합니다.
- **InstrumentationKey** 는 위의 두 필터와 일치 하는 앱의 모니터링을 사용 하도록 설정 하는 데 필요 합니다.
    - 모니터링을 제외 하는 규칙을 정의 하려면이 값을 null로 둡니다.


#### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 표시 합니다.


### <a name="output"></a>출력

기본적으로 출력은 없습니다.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey를 통해 구성 파일을 설정 하는 방법의 예제 세부 정보 출력

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap를 통해 구성 파일을 설정 하는 방법의 예제 세부 정보 출력

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

## <a name="start-applicationinsightsmonitoringtrace"></a>ApplicationInsightsMonitoringTrace

코드 없는 attach 런타임에서 [ETW 이벤트](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) 를 수집 합니다. 이 cmdlet은 [Perfview](https://github.com/microsoft/perfview)를 실행 하는 대신 사용할 수 있습니다.

수집 된 이벤트는 실시간으로 콘솔에 출력 되 고 ETL 파일에 저장 됩니다. 추가 조사를 위해 [Perfview](https://github.com/microsoft/perfview) 에서 출력 ETL 파일을 열 수 있습니다.

이 cmdlet은 제한 시간 (기본값 5 분)에 도달할 때까지 실행 되거나 수동으로 중지 됩니다 (`Ctrl + C`).

### <a name="examples"></a>예

#### <a name="how-to-collect-events"></a>이벤트를 수집 하는 방법

일반적으로 응용 프로그램을 계측 하지 않는 이유를 조사 하기 위해 이벤트를 수집 하도록 요청 합니다.

코드 없는 attach 런타임은 IIS가 시작 될 때와 응용 프로그램이 시작 될 때 ETW 이벤트를 내보냅니다.

이러한 이벤트를 수집 하려면:
1. 관리자 권한이 있는 cmd 콘솔에서를 실행 `iisreset /stop` 하 여 IIS 및 모든 웹 앱을 해제 합니다.
2. 이 cmdlet 실행
3. 관리자 권한이 있는 cmd 콘솔에서를 실행 `iisreset /start` 하 여 IIS를 시작 합니다.
4. 앱으로 이동 해 보세요.
5. 앱 로드가 완료 되 면 수동으로 중지 (`Ctrl + C`) 하거나 시간 초과를 기다릴 수 있습니다.

#### <a name="what-events-to-collect"></a>수집할 이벤트

이벤트를 수집할 때 세 가지 옵션이 있습니다.
1. Application Insights SDK에서 `-CollectSdkEvents` 내보낸 이벤트를 수집 하려면 스위치를 사용 합니다.
2. 스위치 `-CollectRedfieldEvents` 를 사용 하 여 상태 모니터 및 Redfield 런타임에서 내보낸 이벤트를 수집 합니다. 이러한 로그는 IIS 및 응용 프로그램 시작을 진단할 때 유용 합니다.
3. 두 스위치를 모두 사용 하 여 두 이벤트 유형을 모두 수집 합니다.
4. 기본적으로 스위치를 지정 하지 않으면 두 이벤트 유형이 모두 수집 됩니다.


### <a name="parameters"></a>매개 변수

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**필드.** 이 매개 변수를 사용 하 여이 스크립트에서 이벤트를 수집 하는 기간을 설정 합니다. 기본값은 5분입니다.

#### <a name="-logdirectory"></a>-LogDirectory
**필드.** 이 스위치를 사용 하 여 ETL 파일의 출력 디렉터리를 설정 합니다. 기본적으로이 파일은 PowerShell 모듈 디렉터리에 생성 됩니다. 전체 경로는 스크립트를 실행 하는 동안 표시 됩니다.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**필드.** 이 스위치를 사용 하 여 Application Insights SDK 이벤트를 수집 합니다.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**필드.** 이 스위치를 사용 하 여 상태 모니터 및 Redfield 런타임에서 이벤트를 수집 합니다.

#### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 출력 합니다.



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
 - [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석](../../azure-monitor/app/analytics.md) 을 사용 합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](../../azure-monitor/app/asp-net.md) 합니다.
 
 Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.






