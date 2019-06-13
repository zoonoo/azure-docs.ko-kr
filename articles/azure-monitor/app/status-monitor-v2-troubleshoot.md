---
title: Azure 상태 모니터 v2 문제 해결 및 알려진된 문제 | Microsoft Docs
description: 상태 모니터 v2 예제 문제 해결 및 알려진된 문제입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스 또는 Azure Vm에서 호스트 하는 ASP.NET 웹 앱에서 작동 합니다.
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
ms.openlocfilehash: f2115ee14b58030f695c9410870615f03d353cd2
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734957"
---
# <a name="troubleshooting-status-monitor-v2"></a>상태 문제 해결 모니터링 v2

모니터링을 사용 하면 데이터 수집을 방해 하는 문제가 발생할 수 있습니다.
이 문서에서는 모든 알려진된 문제를 나열 하 고 문제 해결 예제를 제공 합니다.
여기에 나열 되지 않은 문제에서 제공 하는 경우 연락 하시면 온 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)합니다.


> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>응용 프로그램의 bin 디렉터리에서 충돌 하는 Dll

Bin 디렉터리에 있는 경우 이러한 Dll에 대 한 모든 모니터링 실패할 수 있습니다.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

이러한 Dll 중 일부는 앱 사용 하지 않는 경우에 Visual Studio 기본 앱 템플릿에 포함 됩니다.
집중도 동작을 확인 하려면 문제 해결 도구를 사용할 수 있습니다.

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

- IISReset 및 앱 원격 분석) (없이 로드합니다. Sysinternals Handle.exe과 ListDLLs.exe를 사용 하 여 조사 합니다.
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>IIS 공유 구성 사용 하 여 충돌

웹 서버 클러스터가 있는 경우 사용할 수 있습니다는 [공유 구성](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)합니다.
HttpModule이 공유 구성에 삽입할 수 없습니다.
각 서버의 GAC에 DLL을 설치 하려면 각 웹 서버에서 설정 명령을 실행 합니다.

Enable 명령을 실행 한 후 다음이 단계를 완료 합니다.
1. 공유 구성 디렉터리로 이동한 applicationHost.config 파일을 찾습니다.
2. 구성의 modules 섹션에이 줄을 추가 합니다.
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
## <a name="troubleshooting"></a>문제 해결
    
### <a name="troubleshooting-powershell"></a>PowerShell 문제 해결

#### <a name="determine-which-modules-are-available"></a>사용할 수 있는 모듈 확인
사용할 수는 `Get-Module -ListAvailable` 명령을 모듈 설치 되어 있는지 확인 합니다.

#### <a name="import-a-module-into-the-current-session"></a>현재 세션으로 모듈을 가져오려면
모듈을 PowerShell 세션에 로드 되지 않은 경우 로드할 수 있습니다 수동으로 사용 하 여는 `Import-Module <path to psd1>` 명령입니다.


### <a name="troubleshooting-the-status-monitor-v2-module"></a>상태 모니터 v2 모듈 문제 해결

#### <a name="list-the-commands-available-in-the-status-monitor-v2-module"></a>상태 모니터 v2 모듈에 사용할 수 있는 명령 목록
명령을 실행 하 여 `Get-Command -Module Az.ApplicationMonitor` 사용할 수 있는 명령을 가져옵니다.

```
Command type     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-status-monitor-v2-module"></a>상태 모니터 v2 모듈의 현재 버전 확인
실행 된 `Get-ApplicationInsightsMonitoringStatus` 모듈에 대 한 다음 정보를 표시 하는 명령:
   - PowerShell 모듈 버전
   - Application Insights SDK 버전
   - PowerShell 모듈의 파일 경로
    
검토 합니다 [API 참조](status-monitor-v2-api-get-status.md) 에 대 한 자세한 설명은이 cmdlet을 사용 하는 방법입니다.


### <a name="troubleshooting-running-processes"></a>실행 중인 프로세스 문제 해결

모든 Dll이 로드 하는 경우를 결정 하는 계측 된 컴퓨터의 프로세스를 검사할 수 있습니다.
모니터링이 작동 하는 경우에 12 개 이상의 Dll은 로드할 수 해야 합니다.

사용 된 `Get-ApplicationInsightsMonitoringStatus -InspectProcess` Dll을 확인 하는 명령입니다.

검토 합니다 [API 참조](status-monitor-v2-api-get-status.md) 에 대 한 자세한 설명은이 cmdlet을 사용 하는 방법입니다.


### <a name="collect-etw-logs-by-using-perfview"></a>PerfView를 사용 하 여 ETW 로그를 수집 합니다.

#### <a name="setup"></a>설정

1. 다운로드 PerfView.exe 및에서 PerfView64.exe [GitHub](https://github.com/Microsoft/perfview/releases)합니다.
2. PerfView64.exe를 시작 합니다.
3. 확장 **고급 옵션**합니다.
4. 이러한 확인란의 선택을 취소 합니다.
    - **Zip**
    - **병합**
    - **.NET 기호 컬렉션**
5. 이 속성을 설정할 **추가 공급자**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>로그 수집

1. 관리자 권한으로 명령 콘솔을 실행 하는 `iisreset /stop` IIS를 해제 하는 명령 및 모든 웹 앱입니다.
2. PerfView를 선택 **수집 시작**합니다.
3. 관리자 권한으로 명령 콘솔을 실행 하는 `iisreset /start` IIS를 시작 하려면 명령입니다.
4. 앱으로 이동 하려고 합니다.
5. 앱이 로드 되 면 선택한 PerfView 돌아갑니다 **컬렉션 중지**합니다.



## <a name="next-steps"></a>다음 단계

- 검토 합니다 [API 참조](status-monitor-v2-overview.md#powershell-api-reference) 하지 않았을 수 있습니다 매개 변수에 대 한 자세한 합니다.
- 여기에 나열 되지 않은 문제에서 제공 하는 경우 연락 하시면 온 [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues)합니다.
