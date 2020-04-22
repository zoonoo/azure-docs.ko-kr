---
title: Azure 응용 프로그램 인사이트 에이전트 문제 해결 및 알려진 문제 | 마이크로 소프트 문서
description: 응용 프로그램 인사이트 에이전트 및 문제 해결 예제의 알려진 문제입니다. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 9bb22b12a7b3e972ff144bd121db4288801e2488
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732946"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>응용 프로그램 인사이트 에이전트 문제 해결(이전 이름 상태 모니터 v2)

모니터링을 사용하도록 설정하면 데이터 수집을 방해하는 문제가 발생할 수 있습니다.
이 문서에서는 알려진 모든 문제를 나열하고 문제 해결 예제를 제공합니다.
여기에 나열되지 않은 문제가 발생하면 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)에 문의할 수 있습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>앱의 빈 디렉터리에서 DLL 충돌

이러한 DLL 중 일부가 빈 디렉터리에 있는 경우 모니터링이 실패할 수 있습니다.

- 마이크로소프트.애플리케이션인사이트.dll
- 마이크로소프트.AspNet.텔레메트리상관.dll
- 시스템.진단.진단소스.dll

이러한 DLL 중 일부는 앱에서 사용하지 않는 경우에도 Visual Studio 기본 앱 템플릿에 포함됩니다.
문제 해결 도구를 사용하여 증상 동작을 확인할 수 있습니다.

- 퍼프뷰:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- 원격 분석 없이 IISReset 및 앱 로드. Sysinternals로 조사 (핸들.exe 및 ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>IIS 공유 구성과의 충돌

웹 서버 클러스터가 있는 경우 [공유 구성을](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)사용하고 있을 수 있습니다.
HttpModule은 이 공유 구성에 삽입할 수 없습니다.
각 웹 서버에서 활성화 명령을 실행하여 각 서버의 GAC에 DLL을 설치합니다.

활성화 명령을 실행한 후 다음 단계를 완료합니다.
1. 공유 구성 디렉토리로 이동하여 applicationHost.config 파일을 찾습니다.
2. 구성의 모듈 섹션에 이 줄을 추가합니다.
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS 중첩 응용 프로그램

버전 1.0에서는 IIS에 중첩된 응용 프로그램을 계측하지 않습니다.
여기에서 이 문제를 추적하고 [있습니다.](https://github.com/microsoft/ApplicationInsights-Home/issues/369)

### <a name="advanced-sdk-configuration-isnt-available"></a>고급 SDK 구성을 사용할 수 없습니다.

SDK 구성은 버전 1.0에서 최종 사용자에게 노출되지 않습니다.
여기에서 이 문제를 추적하고 [있습니다.](https://github.com/microsoft/ApplicationInsights-Home/issues/375)

    
    
## <a name="troubleshooting"></a>문제 해결
    
### <a name="troubleshooting-powershell"></a>파워쉘 문제 해결

#### <a name="determine-which-modules-are-available"></a>사용 가능한 모듈 결정
이 `Get-Module -ListAvailable` 명령을 사용하여 설치되는 모듈을 확인할 수 있습니다.

#### <a name="import-a-module-into-the-current-session"></a>모듈을 현재 세션으로 가져오기
모듈이 PowerShell 세션에 로드되지 않은 경우 `Import-Module <path to psd1>` 명령을 사용하여 수동으로 로드할 수 있습니다.


### <a name="troubleshooting-the-application-insights-agent-module"></a>애플리케이션 인사이트 에이전트 모듈 문제 해결

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>응용 프로그램 인사이트 에이전트 모듈에서 사용할 수 있는 명령 나열
명령을 `Get-Command -Module Az.ApplicationMonitor` 실행하여 사용 가능한 명령을 가져옵니다.

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>응용 프로그램 인사이트 에이전트 모듈의 현재 버전 확인
`Get-ApplicationInsightsMonitoringStatus -PowerShellModule` 명령을 실행하여 모듈에 대한 다음 정보를 표시합니다.
   - 파워쉘 모듈 버전
   - 애플리케이션 인사이트 SDK 버전
   - PowerShell 모듈의 파일 경로
    
이 cmdlet을 사용하는 방법에 대한 자세한 설명은 [API 참조를](status-monitor-v2-api-reference.md) 검토합니다.


### <a name="troubleshooting-running-processes"></a>실행 중인 프로세스 문제 해결

계측된 컴퓨터의 프로세스를 검사하여 모든 DLL이 로드되었는지 확인할 수 있습니다.
모니터링이 작동하는 경우 최소 12개의 DLL을 로드해야 합니다.

`Get-ApplicationInsightsMonitoringStatus -InspectProcess` 명령을 사용하여 DLL을 확인합니다.

이 cmdlet을 사용하는 방법에 대한 자세한 설명은 [API 참조를](status-monitor-v2-api-reference.md) 검토합니다.


### <a name="collect-etw-logs-by-using-perfview"></a>PerfView를 사용하여 ETW 로그 수집

#### <a name="setup"></a>설치 프로그램

1. [GitHub에서](https://github.com/Microsoft/perfview/releases)PerfView.exe 및 PerfView64.exe를 다운로드하십시오.
2. PerfView64.exe를 시작합니다.
3. **고급 옵션**을 확장합니다.
4. 다음 확인란을 선택 취소합니다.
    - **Zip**
    - **병합**
    - **.NET 심볼 컬렉션**
5. 이러한 **추가 공급자**설정:`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>로그 수집

1. 관리자 권한이 있는 명령 콘솔에서 `iisreset /stop` 명령을 실행하여 IIS 및 모든 웹 앱을 끕니다.
2. PerfView에서 **컬렉션 시작을**선택합니다.
3. 관리자 권한이 있는 명령 콘솔에서 `iisreset /start` 명령을 실행하여 IIS를 시작합니다.
4. 앱을 찾아보십시오.
5. 앱이 로드된 후 PerfView로 **돌아가컬렉션 중지를**선택합니다.



## <a name="next-steps"></a>다음 단계

- API [참조를](status-monitor-v2-overview.md#powershell-api-reference) 검토하여 놓친 매개 변수에 대해 알아봅니다.
- 여기에 나열되지 않은 문제가 발생하면 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)에 문의할 수 있습니다.
