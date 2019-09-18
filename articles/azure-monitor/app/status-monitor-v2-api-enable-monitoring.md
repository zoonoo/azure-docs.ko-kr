---
title: 'Azure 상태 모니터 v2 API 참조: 모니터링 사용 | Microsoft Docs'
description: V2 API 참조를 상태 모니터 합니다. Enable-ApplicationInsightsMonitoring. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 8742316697e6d6c8178bb02c8e7288499c655b6b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033124"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring"></a>상태 모니터 v2 API: ApplicationInsightsMonitoring

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>설명

대상 컴퓨터에서 IIS 앱에 대 한 코드 없는 연결 모니터링을 사용 하도록 설정 합니다.

이 cmdlet은 IIS Applicationhost.config를 수정 하 고 일부 레지스트리 키를 설정 합니다.
또한 각 앱에서 사용 하는 계측 키를 정의 하는 applicationinsights. ikey .config 파일이 만들어집니다.
IIS는 시작 시 RedfieldModule를 로드 합니다. 그러면 응용 프로그램이 시작 될 때 응용 프로그램에 Application Insights SDK가 삽입 됩니다.
IIS를 다시 시작 하 여 변경 내용을 적용 합니다.

모니터링을 사용 하도록 설정한 후에는 [라이브 메트릭을](live-stream.md) 사용 하 여 앱이 원격 분석을 전송 하 고 있는지 신속 하 게 확인 하는 것이 좋습니다.


> [!NOTE] 
> - 시작 하려면 계측 키가 필요 합니다. 자세한 내용은 [리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)를 참조 하세요.
> - 이 cmdlet을 사용 하려면 라이선스 및 개인 정보 취급 방침을 검토 하 고 동의 해야 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한 및 관리자 권한으로 실행 정책이 있는 PowerShell 세션이 필요 합니다. 자세한 내용은 [관리자 권한으로 실행 정책으로 PowerShell 실행](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)을 참조 하세요.

## <a name="examples"></a>예

### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키를 사용 하는 예제
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵이 있는 예제
이 예제에서:
- `MachineFilter`와일드 카드를 `'.*'` 사용 하 여 현재 컴퓨터와 일치 합니다.
- `AppFilter='WebAppExclude'`계측 키 `null` 를 제공 합니다. 지정 된 앱은 계측 되지 않습니다.
- `AppFilter='WebAppOne'`지정 된 앱에 고유한 계측 키를 할당 합니다.
- `AppFilter='WebAppTwo'`지정 된 앱에 고유한 계측 키를 할당 합니다.
- 마지막으로는 `'.*'` 와일드 카드를 사용 하 여 이전 규칙과 일치 하지 않는 모든 웹 앱을 검색 하 고 기본 계측 키를 할당 합니다. `AppFilter`
- 가독성을 위해 공백을 추가 합니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>매개 변수

### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공 합니다.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수** 이 매개 변수를 사용 하 여 여러 계측 키를 제공 하 고 각 앱에서 사용 하는 계측 키의 매핑을 제공 합니다.
을 설정 `MachineFilter`하 여 여러 컴퓨터에 대해 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공 된 순서 대로 규칙에 대해 일치 합니다. 따라서 가장 구체적인 규칙을 먼저 지정 하 고 가장 일반적인 규칙을 마지막에 지정 해야 합니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **Machinefilter** 는 컴퓨터 또는 C# VM 이름의 필수 regex입니다.
    - '. * '는 모두 일치 합니다.
    - ' ComputerName '은 정확히 이름이 지정 된 컴퓨터에만 일치 합니다.
- **Appfilter** 는 IIS 사이트 C# 이름의 필수 regex입니다. [Get iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)명령을 실행 하 여 서버에서 사이트 목록을 가져올 수 있습니다.
    - '. * '는 모두 일치 합니다.
    - ' SiteName '은 정확한 이름을 지정 하 여 IIS 사이트와 일치 합니다.
- **InstrumentationKey** 는 위의 두 필터와 일치 하는 앱의 모니터링을 사용 하도록 설정 하는 데 필요 합니다.
    - 모니터링을 제외 하는 규칙을 정의 하려면이 값을 null로 둡니다.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**선택** 이 스위치를 사용 하 여 계측 엔진에서 관리 되는 프로세스를 실행 하는 동안 발생 하는 상황에 대 한 이벤트 및 메시지를 수집할 수 있도록 합니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트가 포함 됩니다.

계측 엔진은 오버 헤드를 추가 하며 기본적으로 해제 되어 있습니다.

### <a name="-acceptlicense"></a>-AcceptLicense
**선택** 이 스위치를 사용 하 여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침에 동의 합니다.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
웹 서버 클러스터가 있는 경우 [공유 구성을](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)사용할 수 있습니다.
HttpModule은이 공유 구성에 삽입할 수 없습니다.
이 스크립트는 추가 설치 단계가 필요 하다는 메시지와 함께 실패 합니다.
이 검사를 무시 하 고 필수 구성 요소 설치를 계속 하려면이 스위치를 사용 하십시오. 자세한 내용은 [알려진 충돌 (iis 공유-구성](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) )을 참조 하세요.

### <a name="-verbose"></a>-Verbose
**일반 매개 변수입니다.** 이 스위치를 사용 하 여 자세한 로그를 표시 합니다.

### <a name="-whatif"></a>-WhatIf 
**일반 매개 변수입니다.** 이 스위치를 사용 하면 실제로 모니터링을 사용 하지 않고 입력 매개 변수를 테스트 하 고 유효성을 검사할 수 있습니다.

## <a name="output"></a>출력


#### <a name="example-output-from-a-successful-enablement"></a>성공적인 활성화의 예제 출력

```
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

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석을 사용](../../azure-monitor/app/analytics.md) 합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되는지 확인 합니다.
- 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](../../azure-monitor/app/asp-net.md) 합니다.
 
 상태 모니터 v2를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 상태 모니터 v2 [문제를 해결](status-monitor-v2-troubleshoot.md) 하세요.
 - [구성을 가져와서](status-monitor-v2-api-get-config.md) 설정이 올바르게 기록 되었는지 확인 합니다.
 - 모니터링을 검사할 [상태를 가져옵니다](status-monitor-v2-api-get-status.md) .
