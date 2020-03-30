---
title: Azure 로그 분석을 사용하여 WAF 로그 검사
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure Log Analytics를 사용하여 응용 프로그램 게이트웨이 웹 응용 프로그램 방화벽 로그를 검사하는 방법을 보여 주었습니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9fe4462a71852e5f66268f798f6f0418f2dd39c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048119"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Log Analytics를 사용하여 Application Gateway 웹 애플리케이션 방화벽 로그 검사

응용 프로그램 게이트웨이 WAF가 작동하면 로그를 사용하여 각 요청에 어떤 일이 발생하는지 검사할 수 있습니다. 방화벽 로그는 WAF가 평가, 일치 및 차단하는 것에 대한 통찰력을 제공합니다. Log Analytics를 사용하면 방화벽 로그 내부의 데이터를 검사하여 더 많은 통찰력을 얻을 수 있습니다. 로그 분석 작업 영역을 만드는 데 대한 자세한 내용은 [Azure 포털에서 로그 분석 작업 영역 만들기를](../azure-monitor/learn/quick-create-workspace.md)참조하십시오. 로그 쿼리에 대한 자세한 내용은 [Azure Monitor 의 로그 쿼리 개요를](../azure-monitor/log-query/log-query-overview.md)참조하십시오.

## <a name="import-waf-logs"></a>WAF 로그 가져오기

방화벽 로그를 Log Analytics로 가져오려면 [응용 프로그램 게이트웨이에 대한 백 엔드 상태, 진단 로그 및 메트릭을](application-gateway-diagnostics.md#diagnostic-logging)참조하십시오. Log Analytics 작업 영역에 방화벽 로그가 있는 경우 데이터를 보고, 쿼리를 작성하고, 시각화를 만들고, 포털 대시보드에 추가할 수 있습니다.

## <a name="explore-data-with-examples"></a>예제를 사용하여 데이터 탐색

방화벽 로그의 원시 데이터를 보려면 다음 쿼리를 실행할 수 있습니다.

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

이는 다음 쿼리와 유사합니다.

![로그 분석 쿼리](media/log-analytics/log-query.png)

데이터를 드릴다운하고 그래프를 플롯하거나 여기에서 시각화를 만들 수 있습니다. 다음 쿼리를 시작점으로 확인합니다.

### <a name="matchedblocked-requests-by-ip"></a>IP별 일치/차단된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI에 의한 일치/차단된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>일치하는 상위 규칙

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>일치하는 상위 5개 규칙 그룹

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>대시보드에 추가

쿼리를 만든 후 대시보드에 추가할 수 있습니다.  로그 분석 작업 영역의 오른쪽 상단에 있는 **대시보드에 대한 Pin을** 선택합니다. 예제 대시보드에 고정된 이전 네 개의 쿼리를 사용하면 이 쿼리를 한눈에 볼 수 있는 데이터입니다.

![대시보드](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>다음 단계

[Application Gateway에 대한 백 엔드 상태, 진단 로그 및 메트릭](application-gateway-diagnostics.md)
