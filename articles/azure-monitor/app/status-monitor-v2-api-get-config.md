---
title: Azure 애플리케이션 Insights 에이전트 API 참조
description: 에이전트 API 참조를 Application Insights 합니다. Get-ApplicationInsightsMonitoringConfig. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 93ab8f613e5634b2eabe7c02189e223d3dfbb0a8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671276"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights 에이전트 API: ApplicationInsightsMonitoringConfig

이 문서에서는 [Az. ApplicationMonitor PowerShell 모듈](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)의 멤버인 cmdlet에 대해 설명 합니다.

## <a name="description"></a>Description

구성 파일을 가져오고 값을 콘솔에 출력 합니다.

> [!IMPORTANT] 
> 이 cmdlet을 사용 하려면 관리자 권한이 있는 PowerShell 세션이 있어야 합니다.

## <a name="examples"></a>예

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>매개 변수

매개 변수가 필요 하지 않습니다.

## <a name="output"></a>출력


#### <a name="example-output-from-reading-the-config-file"></a>구성 파일 읽기의 예제 출력

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석](../../azure-monitor/app/analytics.md) 을 사용 합니다.
- [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되는지 확인 합니다.
- 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](../../azure-monitor/app/asp-net.md) 합니다.
 
 Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:
 - 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.
 - [Set config](status-monitor-v2-api-set-config.md) cmdlet을 사용 하 여 구성을 변경할 수 있습니다.
