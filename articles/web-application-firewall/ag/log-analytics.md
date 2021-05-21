---
title: Azure Log Analytics를 사용하여 Application Gateway 웹 애플리케이션 방화벽 로그 검사
description: 이 문서에서는 Azure Log Analytics를 사용하여 Application Gateway 웹 애플리케이션 방화벽 로그를 검사하는 방법을 보여줍니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: c928d4234e82e7c43c2365e20d57d0b97d4dbc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589008"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Log Analytics를 사용하여 Application Gateway WAF(웹 애플리케이션 방화벽) 로그 검사

Application Gateway WAF가 작동하고 나면 로그를 사용하여 각 요청에서 발생하는 상황을 검사할 수 있습니다. 방화벽 로그는 WAF가 평가, 일치 및 차단하는 항목에 대한 인사이트를 제공합니다. Azure Monitor Log Analytics를 사용하면 방화벽 로그 내의 데이터를 검사하여 훨씬 더 많은 인사이트를 제공할 수 있습니다. Log Analytics 작업 영역을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요. 로그 쿼리에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.

## <a name="import-waf-logs"></a>WAF 로그 가져오기

Log Analytics로 방화벽 로그를 가져오려면 [백 엔드 상태, 리소스 로그 및 Application Gateway에 대한 메트릭](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)을 참조하세요. Log Analytics 작업 영역에 방화벽 로그가 있으면 데이터를 보고, 쿼리를 작성하고, 시각화를 만들어 포털 대시보드에 추가할 수 있습니다.

## <a name="explore-data-with-examples"></a>예제를 사용하여 데이터 탐색

방화벽 로그에서 원시 데이터를 보려면 다음 쿼리를 실행하면 됩니다.

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

이는 다음 쿼리와 유사합니다.

![Log Analytics 쿼리](../media/log-analytics/log-query.png)

여기에서 데이터를 드릴다운하고 그래프를 그리거나 시각화를 만들 수 있습니다. 다음 쿼리를 시작점으로 참조하세요.

### <a name="matchedblocked-requests-by-ip"></a>IP별 일치/차단된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI별 일치/차단된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>상위 일치 규칙

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>상위 5개 일치 규칙 그룹

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>대시보드에 추가

쿼리를 만든 후 대시보드에 추가할 수 있습니다.  Log Analytics 작업 영역의 오른쪽 위에서 **대시보드에 고정** 을 선택합니다. 예제 대시보드에 고정된 이전 4개의 쿼리를 사용하여 해당 데이터를 한눈에 볼 수 있습니다.

![해당 스크린샷은 쿼리를 추가할 수 있는 Azure 대시보드를 보여줍니다.](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>다음 단계

[Application Gateway에 대한 백 엔드 상태, 리소스 로그 및 메트릭](../../application-gateway/application-gateway-diagnostics.md)