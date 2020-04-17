---
title: Azure 응용 프로그램 인사이트 에이전트 API 참조
description: 응용 프로그램 인사이트 에이전트 API 참조. 응용 프로그램 인사이트 모니터링구성. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0cb5991b381acc9a0ec36113d0e15a7731b5074d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537494"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>애플리케이션 인사이트 에이전트 API: 애플리케이션 인사이트 모니터링구성

이 문서에서는 [Az.ApplicationMonitor PowerShell 모듈의](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)구성원인 cmdlet에 대해 설명합니다.

## <a name="description"></a>Description

구성 파일을 가져옵니다 및 콘솔에 값을 인쇄 합니다.

> [!IMPORTANT] 
> 이 cmdlet에는 관리자 권한이 있는 PowerShell 세션이 필요합니다.

## <a name="examples"></a>예

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>매개 변수

매개 변수가 필요하지 않습니다.

## <a name="output"></a>출력


#### <a name="example-output-from-reading-the-config-file"></a>구성 파일 읽기에서 출력 예

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>다음 단계

  원격 분석 보기:
 - [메트릭을 탐색하여](../../azure-monitor/platform/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
- [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
- 고급 쿼리에 [대한 분석을](../../azure-monitor/app/analytics.md) 사용합니다.
- [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)
 
 원격 분석 더 추가:
 - [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK를 추가합니다.](../../azure-monitor/app/asp-net.md)
 
 애플리케이션 인사이트 에이전트로 더 많은 작업을 수행합니다.
 - 가이드를 사용하여 애플리케이션 인사이트 에이전트 [문제를 해결하세요.](status-monitor-v2-troubleshoot.md)
 - [설정 구성](status-monitor-v2-api-set-config.md) cmdlet을 사용하여 구성을 변경합니다.
