---
title: 'Azure 상태 모니터 v2 API 참조: 구성 설정 | Microsoft Docs'
description: 상태 모니터 v2-ApplicationInsightsMonitoringConfig API 참조 집합입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. VM 또는 Azure의 온-프레미스에서 호스트되는 ASP.NET 웹앱으로 작업합니다.
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
ms.openlocfilehash: 971fea76a23859f32437a1698b6d3094113737a1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255090"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>상태 모니터 v2 API: 집합-ApplicationInsightsMonitoringConfig (v0.2.1 알파)

이 문서에서는의 구성원으로 제공 되는 cmdlet을 설명 합니다 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 참조 하세요. [추가 사용 약관에 대 한 Microsoft Azure 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>설명

전체 다시 설치를 반복 하지 않고 구성 파일을 설정 합니다. 변경 내용을 적용 하려면 IIS를 다시 시작 합니다.

> [!IMPORTANT] 
> 이 cmdlet은 관리자 권한으로 PowerShell 세션에 필요합니다.


## <a name="examples"></a>예

### <a name="example-with-single-instrumentation-key"></a>단일 계측 키를 사용 하 여 예제
이 예제에서는 현재 컴퓨터의 모든 응용 프로그램 단일 계측 키를 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>계측 키 맵 사용 하 여 예제
이 예제에서는 다음이 적용됩니다. 
- `MachineFilter` 일치를 사용 하 여 현재 컴퓨터를 `'.*'` 와일드 카드입니다.
- `AppFilter='WebAppExclude'` 제공 된 `null` InstrumentationKey 합니다. 이 앱을 계측할 수 없습니다.
- `AppFilter='WebAppOne'` 이 특정 응용 프로그램을 고유 계측 키를 할당 됩니다.
- `AppFilter='WebAppTwo'` 이 특정 응용 프로그램 고유 계측 키를 할당할 수도 됩니다.
- 마지막으로, `AppFilter` 사용을 `'.*'` 와일드 카드 일치 다른 모든 web apps를 이전 규칙에 의해 일치 없고 기본 계측 키를 할당 합니다.
- 만 가독성을 위해 추가 공간입니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>매개 변수 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 응용 프로그램에서 사용 하기 위해 단일 iKey를 제공 합니다.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수** 이 매개 변수를 사용 하 여 여러 ikey 및 ikey를 사용 하는 앱의 매핑을 제공 합니다. MachineFilter를 설정 하 여 여러 컴퓨터에 대 한 단일 설치 스크립트를 만들 수 있습니다. 

> [!IMPORTANT] 
> 응용 프로그램을 제공 하는 순서에서는 규칙에 대해 일치 합니다. 먼저 가장 구체적인 규칙을 지정 해야는 같이 및 가장 일반적인 규칙입니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** vm 또는 컴퓨터 이름의 필요한 c# regex는 합니다.
    - '. *'는 모두 일치
    - 'ComputerName' 똑같은 이름 사용 하 여 컴퓨터에만 일치 합니다.
- **AppFilter** vm 또는 컴퓨터 이름의 필요한 c# regex는 합니다.
    - '. *'는 모두 일치
    - 'ApplicationName' 똑같은 이름 사용 하 여 IIS 응용 프로그램만 일치 합니다.
- **InstrumentationKey** 위의 두 필터와 일치 하는 응용 프로그램의 모니터링을 사용 하도록 설정 해야 합니다.
    - 이 값을 모니터링을 제외 하려면 규칙을 정의 하려는 경우에 null 유지


### <a name="-verbose"></a>-Verbose
**일반적인 매개 변수입니다.** 자세한 로그를 출력 하려면이 스위치를 사용 합니다.


## <a name="output"></a>출력

기본적으로 출력이 없습니다.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>자세한 정보 표시의 예제 출력-InstrumentationKey를 통해 구성 파일 설정

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>자세한 정보 표시의 예제 출력-InstrumentationKeyMap 통해 구성 파일 설정

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
 - [메트릭을 탐색하여](../../azure-monitor/app/metrics-explorer.md) 성능 및 사용량을 모니터링합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제 진단
- [분석](../../azure-monitor/app/analytics.md)을 통해 고급 쿼리를 수행합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 삽입할 수 있도록 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록
 
 상태 모니터 v2 사용 하 여 더 수행 합니다.
 - 가이드를 사용 하 여 [문제 해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
 - [구성 가져오기](status-monitor-v2-api-get-config.md) 설정을 올바르게 기록 된를 확인 합니다.
 - [상태를 가져오려면](status-monitor-v2-api-get-status.md) 모니터링 검사 합니다.
