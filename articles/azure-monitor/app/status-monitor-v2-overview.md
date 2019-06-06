---
title: Azure 상태 모니터 v2 개요 | Microsoft Docs
description: 상태 모니터 v2 간략히 설명 합니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스 또는 Azure Vm에서 호스트 하는 ASP.NET 웹 앱에서 작동 합니다.
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
ms.openlocfilehash: 2126408222433e6339723dc2da0d2611bb234fe8
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734152"
---
# <a name="status-monitor-v2"></a>상태 모니터 v2

상태 모니터 v2는 PowerShell 모듈에 게시 합니다 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)합니다.
대체 [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)합니다.
모듈의 IIS를 사용 하 여 호스트 되는.NET 웹 앱 코드 계측을 제공 합니다.
원격 분석을 할 수 있는 Azure portal로 전송 됩니다 [모니터](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 앱.

> [!IMPORTANT]
> 상태 모니터 v2는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 하 고 프로덕션 워크 로드에 대 한 권장 하지 않습니다. 일부 기능은 지원 되지 않는, 및 일부 기능이 제한 될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="powershell-gallery"></a>PowerShell 갤러리

PowerShell 갤러리는 여기: https://www.powershellgallery.com/packages/Az.ApplicationMonitor합니다.


## <a name="instructions"></a>지침
- 참조 된 [지침 시작](status-monitor-v2-get-started.md) 간결한 코드 샘플을 사용 하 여 시작 하려면.
- 참조 된 [상세 지침](status-monitor-v2-detailed-instructions.md) 시작 하는 방법에 자세히입니다.

## <a name="powershell-api-reference"></a>PowerShell API 참조
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>문제 해결
- [문제 해결](status-monitor-v2-troubleshoot.md)
- [알려진 문제](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- 상태 모니터 v2 프록시 설치를 지원 하나요?

  *예*합니다. 상태 모니터 v2를 다운로드 하는 방법은 여러 가지입니다. 컴퓨터에 인터넷 액세스를 등록할 수 있습니다 PowerShell 갤러리를 사용 하 여 `-Proxy` 매개 변수입니다.
모듈을 수동으로 다운로드할 수 있으며 컴퓨터에 설치 하거나 직접 사용 합니다.
이러한 각 옵션에 설명 되어는 [상세 지침](status-monitor-v2-detailed-instructions.md)합니다.
  
- 사용 성공 했는지 어떻게 확인 합니까?

   활성화에 성공 했는지 확인 cmdlet이 있습니다.
사용 하는 것이 좋습니다 [라이브 메트릭](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) 는 앱 원격 분석을 전송 하는 경우 쉽게 확인할 수 있습니다.

   사용할 수도 있습니다 [Log Analytics](../log-query/get-started-portal.md) 현재 원격 분석을 보내는 모든 클라우드 역할을 나열 하려면:
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>다음 단계

원격 분석 보기:

* [메트릭 탐색](../../azure-monitor/app/metrics-explorer.md) 성능 모니터링을 사용 합니다.
* [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md) 문제를 진단 합니다.
* [Analytics를 사용 하 여](../../azure-monitor/app/analytics.md) 자세한 고급 쿼리 합니다.
* [대시보드를 만들](../../azure-monitor/app/overview-dashboard.md)합니다.

원격 분석 더 추가:

* [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지 되도록 합니다.
* [웹 클라이언트 원격 분석 추가](../../azure-monitor/app/javascript.md) 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
* [코드에 Application Insights SDK 추가](../../azure-monitor/app/asp-net.md) 추적을 삽입 하 고 호출을 기록할 수 있도록 합니다.

