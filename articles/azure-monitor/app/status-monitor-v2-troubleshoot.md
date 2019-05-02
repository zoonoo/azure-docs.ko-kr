---
title: Azure 상태 모니터 v2 문제 해결 및 알려진된 문제 | Microsoft Docs
description: 상태 모니터 v2 예제 문제 해결 및 알려진된 문제입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: 26d677df81df6e10fae8d63362d49deee2cd0aca
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870749"
---
# <a name="troubleshooting-status-monitor-v2"></a>상태 문제 해결 모니터링 v2

모니터링을 사용 하면 데이터 수집을 방해 하는 문제가 발생할 수 있습니다. 이 문서에서는 모든 알려진된 문제 및 문제 해결 예제를 나열 합니다.
여기에 나열 되지 문제에서 제공 하는 경우 연락할 수 주세요 [여기](https://github.com/Microsoft/ApplicationInsights-Home/issues)합니다.


> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>알려진 문제

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>응용 프로그램의 bin 디렉터리에서 충돌 하는 Dll

Bin 디렉터리에 있는 경우 이러한 Dll에 대 한 모든 모니터링 실패할 수 있습니다.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

이러한 Dll의 일부 값은 응용 프로그램 사용 하지 않는 경우에 Visual Studio의 기본 응용 프로그램 템플릿에서 포함 됩니다.
문제 해결 도구를 사용 하 여 집중도 동작을 확인할 수 있습니다.

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- iisreset + 앱 (NO 원격 분석)를 로드합니다. Sysinternals Handle.exe과 ListDLLs.exe를 사용 하 여 조사
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>IIS 공유 구성 사용 하 여 충돌

웹 서버 클러스터가 있는 경우 사용할 수 있습니다는 [공유 구성](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)합니다. 자동으로이 공유 구성에는 HttpModule을 넣을 수 없습니다 것입니다. 각 웹 서버에는 해당 GAC에 DLL을 설치 하는 활성화 명령이 먼저 실행 해야 합니다.

사용 하도록 설정 하는 명령은 실행 한 후 
1. 공유 구성 디렉터리로 찾아보기에 `applicationHost.config` 파일입니다.
2. Modules 섹션에서 구성에이 줄을 추가 합니다.
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>문제 해결
    
### <a name="troubleshooting-powershell"></a>PowerShell 문제 해결

#### <a name="how-to-inspect-what-modules-are-available"></a>모듈을 검사 하는 방법을 사용할 수 있습니까?
설치 된 모듈의 명령을 사용 하 여 감사할 수 있습니다. `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>현재 세션으로 모듈을 가져오려면 어떻게 하나요?
명령을 사용 하 여으로 PowerShell 세션에 모듈 로드 되지 않은 경우 수동으로 로드할 수 있습니다. `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>상태 모니터 v2 모듈 문제 해결

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>상태 모니터 v2 모듈에서 사용할 수 있는 명령이 검토 하는 방법
- 명령을 실행: `Get-Command -Module Az.ApplicationMonitor` 사용할 수 있는 명령을 가져옵니다.

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>상태 모니터 v2 모듈의 현재 버전은 무엇입니까?
- 명령을 실행: `Get-ApplicationInsightsMonitoringStatus` 이 모듈에 대 한 정보는 출력을 가져오려면:

    ```
    PowerShell Module version:
    0.2.1-alpha

    Application Insights SDK version:
    2.9.0.3872

    Executing PowerShell Module Assembly:
    Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.9129, Culture=neutral, PublicKeyToken=31bf3856ad364e35

    PowerShell Module Directory:
    C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\PowerShell

    Runtime Paths:
    ParentDirectory: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content Exists: True
    ConfigurationPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\applicationInsights.ikey.config Exists: False
    ManagedHttpModuleHelperPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll Exists: True
    RedfieldIISModulePath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll Exists: True
    InstrumentationEngine86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll Exists: True
    InstrumentationEngine64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll Exists: True
    InstrumentationEngineExtensionHost86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll Exists: True
    InstrumentationEngineExtensionHost64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll Exists: True
    InstrumentationEngineExtensionConfig86Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config Exists: True
    InstrumentationEngineExtensionConfig64Path: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config Exists: True
    ApplicationInsightsSdkPath: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.1\content\Runtime\Microsoft.ApplicationInsights.dll Exists: True
    ```




### <a name="troubleshooting-running-processes"></a>실행 중인 프로세스 문제 해결

계측 된 모든 Dll이 로드 하는 경우 참조 컴퓨터에서 프로세스를 검사할 수 있습니다.
모니터링이 작동 하는 경우에 12 개 이상의 DLL은 로드할 수 해야 합니다.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

    ```
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

### <a name="collect-etw-logs-with-perfview"></a>PerfView 사용 하 여 ETW 로그를 수집 합니다.

#### <a name="setup"></a>설정

1. PerfView.exe 및에서 PerfView64.exe 다운로드 https://github.com/Microsoft/perfview/releases
2. Launch PerfView64.exe
3. "고급 옵션" 확장
4. 선택 취소 합니다.
    - Zip
    - 병합
    - .NET 기호 컬렉션
5. 추가 공급자를 설정 합니다. `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>로그를 수집하는 중

1. 관리자 권한으로 cmd 콘솔에서 실행 `iisreset /stop` IIS 및 모든 웹 앱을 해제 하려면.
2. PerfView를 "컬렉션 시작" 클릭
3. 관리자 권한으로 cmd 콘솔에서 실행 `iisreset /start` IIS를 시작 합니다.
4. 앱으로 이동 하려고 합니다.
5. 앱 로드가 완료 되 면 PerfView 돌아가서 "컬렉션 중지"를 클릭 합니다.

