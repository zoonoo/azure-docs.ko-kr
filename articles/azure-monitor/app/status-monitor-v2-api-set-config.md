---
title: 'Azure 상태 모니터 v2 API 참조: 구성 설정 | Microsoft Docs'
description: 상태 모니터 v2 API 참조입니다. Set-ApplicationInsightsMonitoringConfig. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스 또는 Azure Vm에서 호스트 하는 ASP.NET 웹 앱에서 작동 합니다.
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
ms.openlocfilehash: 562ce8a4267370be9b049e3b56f213f82deb89c0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734999"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>상태 모니터 v2 API: 집합-ApplicationInsightsMonitoringConfig (v0.2.1 알파)

이 문서에서는의 구성원임을 확인 하는 cmdlet을 설명 합니다 [Az.ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)합니다.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="description"></a>설명

전체 설치를 다시 수행 하지 않고 구성 파일을 설정 합니다.
변경 내용을 적용 하려면 IIS를 다시 시작 합니다.

> [!IMPORTANT] 
> 이 cmdlet은 관리자 권한으로 PowerShell 세션에 필요합니다.


## <a name="examples"></a>예

### <a name="example-with-a-single-instrumentation-key"></a>단일 계측 키를 사용 하 여 예제
이 예제에서는 현재 컴퓨터의 모든 앱 단일 계측 키를 할당 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>계측 키 지도와 예제
이 예제에서:
- `MachineFilter` 현재 컴퓨터를 사용 하 여 일치 하는 `'.*'` 와일드 카드입니다.
- `AppFilter='WebAppExclude'` 제공 된 `null` 계측 키입니다. 지정된 된 응용 프로그램을 계측할 수 없습니다.
- `AppFilter='WebAppOne'` 고유 계측 키를 지정 된 앱을 할당합니다.
- `AppFilter='WebAppTwo'` 고유 계측 키를 지정 된 앱을 할당합니다.
- 마지막으로, `AppFilter` 도 사용 된 `'.*'` 이전 규칙에 의해 일치 되지 않습니다 하 고 기본 계측 키를 할당 하는 모든 웹 앱에 맞게 와일드 카드입니다.
- 공백을은 가독성을 위해 추가 됩니다.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>매개 변수

### <a name="-instrumentationkey"></a>-InstrumentationKey
**필수** 이 매개 변수를 사용 하 여 대상 컴퓨터의 모든 앱에서 사용 하기 위해 단일 계측 키를 제공 합니다.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**필수** 이 매개 변수를 사용 하 여 각 앱에서 사용 하는 계측 키를 매핑하는 방법과 여러 계측 키를 제공 합니다.
설정 하 여 여러 컴퓨터에 대 한 단일 설치 스크립트를 만들 수 있습니다 `MachineFilter`합니다.

> [!IMPORTANT]
> 앱 규칙에서 제공 되는 순서 대로 규칙에 대해 일치 합니다. 따라서 가장 구체적인 규칙을 먼저 지정 해야 하 고 가장 일반적인 규칙입니다.

#### <a name="schema"></a>스키마
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** 는 반드시 C# VM 이름 또는 컴퓨터 regex.
    - '. *'는 모두 일치
    - 'ComputerName' 지정한 이름 가진 컴퓨터에만 일치 합니다.
- **AppFilter** 는 반드시 C# VM 이름 또는 컴퓨터 regex.
    - '. *'는 모두 일치
    - 'ApplicationName' 지정 된 이름의 IIS 앱만 일치 합니다.
- **InstrumentationKey** 이전 두 필터와 일치 하는 앱의 모니터링을 사용 하도록 설정 해야 합니다.
    - 모니터링을 제외 하는 규칙을 정의 하려는 경우에 null이 값을 둡니다.


### <a name="-verbose"></a>-Verbose
**일반적인 매개 변수입니다.** 자세한 로그를 표시 하려면이 스위치를 사용 합니다.


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
 - [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md) 성능 모니터링을 사용 합니다.
- [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제를 진단 합니다.
- [Analytics를 사용 하 여](../../azure-monitor/app/analytics.md) 자세한 고급 쿼리 합니다.
- [대시보드를 만들](../../azure-monitor/app/overview-dashboard.md)합니다.
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
- [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록 하므로
 
 상태 모니터 v2 사용 하 여 더 수행 합니다.
 - 가이드를 사용 하 여 [해결](status-monitor-v2-troubleshoot.md) v2 상태 모니터입니다.
 - [구성 가져오기](status-monitor-v2-api-get-config.md) 설정을 올바르게 기록 된를 확인 합니다.
 - [상태를 가져오려면](status-monitor-v2-api-get-status.md) 모니터링 검사 합니다.
