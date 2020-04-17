---
title: Azure 응용 프로그램 인사이트 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 설정 응용 프로그램 인사이트모니터링구성. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c47b9b5f297fa62c474e6c29737d6d11b887130d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537477"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>애플리케이션 인사이트 에이전트 API: 설정-애플리케이션 인사이트모니터링구성

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>Description

전체 재설치를 수행하지 않고 구성 파일을 설정합니다.
변경 사항이 적용되면 IIS를 다시 시작합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.


## <a name="examples"></a>예

### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키의 예
이 예제에서는 현재 컴퓨터의 모든 앱에 단일 계측 키가 할당됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>계측 키 맵의 예
이 예제에서:
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

## <a name="parameters"></a>매개 변수

### <a name="-instrumentationkey"></a>- 계측키
**필수.** 이 매개 변수를 사용하여 대상 컴퓨터의 모든 앱에서 사용할 단일 계측 키를 제공합니다.

### <a name="-instrumentationkeymap"></a>-계측키맵
**필수.** 이 매개 변수를 사용하여 여러 계측 키와 각 앱에서 사용하는 계측 키매핑을 제공합니다.
을 설정하여 `MachineFilter`여러 컴퓨터에 대한 단일 설치 스크립트를 만들 수 있습니다.

> [!IMPORTANT]
> 앱은 규칙이 제공되는 순서대로 규칙과 일치합니다. 따라서 가장 구체적인 규칙을 먼저 지정하고 가장 일반적인 규칙을 마지막으로 지정해야 합니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter는** 컴퓨터 또는 VM 이름의 필수 C# 정규식입니다.
    - '.*'는 모든
    - 'ComputerName'은 지정된 이름의 컴퓨터만 일치합니다.
- **AppFilter는** 컴퓨터 또는 VM 이름의 필수 C# 정규법입니다.
    - '.*'는 모든
    - 'ApplicationName'은 지정된 이름의 IIS 앱만 일치합니다.
- **계측키는** 앞의 두 필터와 일치하는 앱을 모니터링하는 데 필요합니다.
    - 모니터링을 제외하는 규칙을 정의하려면 이 값을 null로 둡니다.


### <a name="-verbose"></a>-Verbose
**공통 매개 변수입니다.** 이 스위치를 사용하여 자세한 로그를 표시합니다.


## <a name="output"></a>출력

기본적으로 출력이 없습니다.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey를 통해 구성 파일을 설정하여 출력을 자세한 예

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap을 통해 구성 파일 설정에서 출력을 자세한 예

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

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색하여](../../azure-monitor/platform/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리에 [애널리틱스를 사용합니다.](../../azure-monitor/app/analytics.md)
- [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK 추가](../../azure-monitor/app/asp-net.md)
 
 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
 - 설정이 올바르게 기록되었는지 확인하기 위해 [구성을 가져옵니다.](status-monitor-v2-api-get-config.md)
 - 모니터링을 검사할 [상태를 가져옵니다.](status-monitor-v2-api-get-status.md)
