---
title: Azure 응용 프로그램 인사이트 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 애플리케이션 인사이트 모니터링 을 활성화합니다. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671310"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>애플리케이션 인사이트 에이전트 API: 인에이블-애플리케이션 인사이트 모니터링

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>설명

대상 컴퓨터에서 IIS 앱을 코드 없이 첨부모니터링할 수 있습니다.

이 cmdlet은 IIS applicationHost.config를 수정하고 일부 레지스트리 키를 설정합니다.
또한 각 앱에서 사용하는 계측 키를 정의하는 applicationinsights.ikey.config 파일을 만듭니다.
IIS는 시작 시 RedfieldModule을 로드하여 응용 프로그램이 시작될 때 응용 프로그램 인사이트 SDK를 응용 프로그램에 삽입합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

모니터링을 사용하도록 설정하면 라이브 [메트릭을](live-stream.md) 사용하여 앱에서 원격 분석을 보내는지 빠르게 확인하는 것이 좋습니다.


> [!NOTE] 
> - 시작하려면 계측 키가 필요합니다. 자세한 내용은 [리소스 만들기](create-new-resource.md#copy-the-instrumentation-key)를 참조하십시오.
> - 이 cmdlet은 당사의 라이선스 및 개인 정보 취급 방침을 검토하고 수락하도록 요구합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션과 높은 실행 정책이 필요합니다. 자세한 내용은 [PowerShell 실행 을 높은 실행 정책이 있는 관리자로 실행](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)을 참조하십시오.

## <a name="examples"></a>예

### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키의 예
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵의 예
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


## <a name="parameters"></a>매개 변수

### <a name="-instrumentationkey"></a>- 계측키
**필수.** 이 매개 변수를 사용하여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공합니다.

### <a name="-instrumentationkeymap"></a>-계측키맵
**필수.** 이 매개 변수를 사용하여 여러 계측 키와 각 앱에서 사용하는 계측 키매핑을 제공합니다.
을 설정하여 `MachineFilter`여러 컴퓨터에 대한 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공되는 순서대로 규칙과 일치합니다. 따라서 가장 구체적인 규칙을 먼저 지정하고 가장 일반적인 규칙을 마지막으로 지정해야 합니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter는** 컴퓨터 또는 VM 이름의 필수 C# 정규식입니다.
    - '.*'는 모든
    - 'ComputerName'은 지정된 정확한 이름의 컴퓨터만 일치합니다.
- **AppFilter는** IIS 사이트 이름의 필수 C# 정규법입니다. [get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)명령을 실행하여 서버의 사이트 목록을 얻을 수 있습니다.
    - '.*'는 모든
    - '사이트이름'은 IIS 사이트와 정확한 이름과 일치합니다.
- **계측키는** 앞의 두 필터와 일치하는 앱을 모니터링하는 데 필요합니다.
    - 모니터링을 제외하는 규칙을 정의하려면 이 값을 null로 둡니다.


### <a name="-enableinstrumentationengine"></a>-인에이블계측 엔진
**선택적.** 이 스위치를 사용하여 계측 엔진이 관리되는 프로세스를 실행하는 동안 발생하는 일에 대한 이벤트 및 메시지를 수집할 수 있습니다. 이러한 이벤트 및 메시지에는 종속성 결과 코드, HTTP 동사 및 SQL 명령 텍스트가 포함됩니다.

계측 엔진은 오버헤드를 추가하고 기본적으로 꺼져 있습니다.

### <a name="-acceptlicense"></a>- 수락라이센스
**선택적.** 이 스위치를 사용하여 헤드리스 설치에서 라이선스 및 개인 정보 취급 방침을 수락합니다.

### <a name="-ignoresharedconfig"></a>-무시공유구성
웹 서버 클러스터가 있는 경우 [공유 구성을](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)사용하고 있을 수 있습니다.
HttpModule은 이 공유 구성에 삽입할 수 없습니다.
이 스크립트는 추가 설치 단계가 필요하다는 메시지와 함께 실패합니다.
이 스위치를 사용하여 이 검사를 무시하고 필수 구성 조건을 계속 설치합니다. 자세한 내용은 [알려진 iis 공유 구성을 참조하십시오.](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 표시합니다.

### <a name="-whatif"></a>-WhatIf 
**공통 매개 변수입니다.** 이 스위치를 사용하여 실제로 모니터링을 사용하지 않고 입력 매개 변수를 테스트하고 유효성을 검사합니다.

## <a name="output"></a>출력


#### <a name="example-output-from-a-successful-enablement"></a>성공적인 인에이블먼트의 예제 출력

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
 - [메트릭을 탐색하여](../../azure-monitor/app/metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리에 [애널리틱스를 사용합니다.](../../azure-monitor/app/analytics.md)
- [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK를 추가합니다.](../../azure-monitor/app/asp-net.md)
 
 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
 - 설정이 올바르게 기록되었는지 확인하기 위해 [구성을 가져옵니다.](status-monitor-v2-api-get-config.md)
 - 모니터링을 검사할 [상태를 가져옵니다.](status-monitor-v2-api-get-status.md)
