---
title: Azure 상태 모니터 v2 개요 | Microsoft Docs
description: 상태 모니터 v2의 개요입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
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
ms.openlocfilehash: 38359858d16a20f73cf845fa547899bdc5ee7fd3
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326243"
---
# <a name="status-monitor-v2"></a>상태 모니터 v2

상태 모니터 v2는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)에 게시 된 PowerShell 모듈입니다.
[상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)를 대체 합니다.
이 모듈은 IIS를 사용 하 여 호스트 되는 .NET 웹 앱의 코드 없는 계측을 제공 합니다.
원격 분석은 앱을 [모니터링할](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 수 있는 Azure Portal 전송 됩니다.

## <a name="powershell-gallery"></a>PowerShell 갤러리

상태 모니터 v2는에 https://www.powershellgallery.com/packages/Az.ApplicationMonitor 있습니다.

![PowerShell 갤러리](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>지침
- 간결한 코드 샘플을 가져오려면 [시작 지침](status-monitor-v2-get-started.md) 을 참조 하세요.
- 시작 하는 방법에 대 한 심층 [정보는 자세한 지침](status-monitor-v2-detailed-instructions.md) 을 참조 하세요.

## <a name="powershell-api-reference"></a>PowerShell API 참조
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>문제 해결
- [문제 해결](status-monitor-v2-troubleshoot.md)
- [알려진 문제](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- V2 상태 모니터 프록시 설치를 지원 하나요?

  *예*. 상태 모니터 v2를 다운로드 하는 방법에는 여러 가지가 있습니다. 컴퓨터에서 인터넷에 액세스할 수 있는 경우 매개 변수를 사용 `-Proxy` 하 여 PowerShell 갤러리에 등록할 수 있습니다.
또한 모듈을 수동으로 다운로드 하 고 컴퓨터에 설치 하거나 직접 사용할 수 있습니다.
이러한 각 옵션에 대 한 [자세한 지침은](status-monitor-v2-detailed-instructions.md)을 참조 하십시오.
  
- 성공적으로 작동 하 고 있는지 확인 어떻게 할까요??

   성공적으로 수행 되었는지 확인 하는 cmdlet이 없습니다.
앱에서 원격 분석을 전송 하 고 있는지 신속 하 게 확인 하려면 [라이브 메트릭을](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) 사용 하는 것이 좋습니다.

   [Log Analytics](../log-query/get-started-portal.md) 를 사용 하 여 현재 원격 분석을 보내는 모든 클라우드 역할을 나열할 수도 있습니다.
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>다음 단계

원격 분석 보기:

* [메트릭을 탐색](../../azure-monitor/app/metrics-explorer.md) 하 여 성능 및 사용량을 모니터링 합니다.
* [이벤트와 로그를 검색](../../azure-monitor/app/diagnostic-search.md) 하 여 문제를 진단 합니다.
* 고급 쿼리를 위해 [분석을 사용](../../azure-monitor/app/analytics.md) 합니다.
* [대시보드를 만듭니다](../../azure-monitor/app/overview-dashboard.md).

원격 분석 더 추가:

* [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되는지 확인 합니다.
* 웹 [클라이언트 원격 분석을 추가](../../azure-monitor/app/javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
* 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](../../azure-monitor/app/asp-net.md) 합니다.

