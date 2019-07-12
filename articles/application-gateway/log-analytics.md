---
title: Azure Log Analytics를 사용 하 여 Application Gateway 웹 응용 프로그램 방화벽 로그를 검사 하려면
description: 이 아티클에서 Azure Log Analytics을 사용 하 여 Application Gateway 웹 응용 프로그램 방화벽 로그를 검사 하는 방법을
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: aa867e33ef0faa96b6a66a9075a3a5b8b0b0bca4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712177"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-logs"></a>Log Analytics를 사용 하 여 응용 프로그램 게이트웨이에 웹 응용 프로그램 방화벽 로그를 검사 하려면

응용 프로그램 게이트웨이 WAF를 작동할 수 있게 되 면 각 요청과 함께 상황을 검사 하는 로그를 사용할 수 있습니다. 방화벽 로그 제공 insight는 WAF 평가, 일치 및 차단 합니다. Log Analytics를 사용 하 여 더 자세한 정보를 제공 하는 방화벽 로그 내에 있는 데이터를 검사할 수 있습니다. Log Analytics 작업 영역을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Azure portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md)합니다. 로그 쿼리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Monitor에서 쿼리 로그 개요](../azure-monitor/log-query/log-query-overview.md)합니다.

## <a name="import-waf-logs"></a>WAF 로그 가져오기

방화벽 로그를 Log Analytics로 가져오려면 참조 [백 엔드 상태, 진단 로그 및 Application Gateway에 대 한 메트릭을](application-gateway-diagnostics.md#diagnostic-logging)합니다. 방화벽 로그를 Log Analytics 작업 영역에 있는 경우 데이터 보기, 쿼리를 작성, 시각화를 만들고 하 수을 포털 대시보드에 추가 합니다.

## <a name="explore-data-with-examples"></a>예제를 사용 하 여 데이터를 탐색 합니다.

방화벽 로그에서 원시 데이터를 보려면 다음 쿼리를 실행할 수 있습니다.

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

이 다음 쿼리는 비슷하게 표시 됩니다.

![Log Analytics 쿼리](media/log-analytics/log-query.png)

데이터를 드릴 다운 및 그래프 플롯 하거나 여기에서 시각화를 만들 수 있습니다. 시작 지점으로 다음 쿼리를 참조 하세요.

### <a name="matchedblocked-requests-by-ip"></a>IP가 일치/차단 된 요청

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI로 요청 일치/차단 됨

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>일치 하는 상위 규칙

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>상위 5 개의 일치 하는 규칙 그룹

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>대시보드에 추가

쿼리를 만든 후에 대시보드에 추가할 수 있습니다.  선택 합니다 **대시보드에 고정** 위쪽에서 log analytics 작업 영역 오른쪽에 있습니다. 예제 대시보드를 고정 이전 4 개의 쿼리를 사용 하 여 한눈에 볼 수 있습니다 하는 데이터입니다.

![대시보드](media/log-analytics/dashboard.png)

## <a name="next-steps"></a>다음 단계

[백 엔드 상태, 진단 로그 및 Application Gateway에 대 한 메트릭](application-gateway-diagnostics.md)
