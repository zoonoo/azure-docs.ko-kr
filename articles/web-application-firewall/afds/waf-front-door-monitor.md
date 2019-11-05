---
title: Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅
description: FrontDoor 모니터링 및 로깅을 사용 하는 WAF (웹 응용 프로그램 방화벽) 배우기
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 0c705139c082f13f40362e598f0fda9ba0a128a5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512484"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅 

Azure WAF (웹 응용 프로그램 방화벽) 모니터링 및 로깅은 로깅 및 Azure Monitor 및 Azure Monitor 로그와의 통합을 통해 제공 됩니다.

## <a name="azure-monitor"></a>Azure Monitor

FrontDoor 로그가 포함 된 WAF는 [Azure Monitor](../../azure-monitor/overview.md)와 통합 됩니다. Azure Monitor를 사용 하면 WAF 경고 및 로그를 포함 한 진단 정보를 추적할 수 있습니다. 포털의 프런트 도어 리소스 내에서 **진단** 탭 또는 Azure Monitor 서비스를 통해 waf 모니터링을 구성할 수 있습니다.

Azure Portal에서 Front 도어 리소스 유형으로 이동 합니다. 왼쪽의 **모니터링**/**메트릭** 탭에서 **WebApplicationFirewallRequestCount** 를 추가 하 여 waf 규칙과 일치 하는 요청 수를 추적할 수 있습니다. 작업 유형 및 규칙 이름에 따라 사용자 지정 필터를 만들 수 있습니다.

![WAFMetrics](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>로그 및 진단

프런트 도어를 사용 하는 WAF는 검색 하는 각 위협에 대해 자세한 보고 기능을 제공 합니다. 로깅은 Azure Diagnostics 로그에 통합되고 경고는 json 형식으로 기록됩니다. 이러한 로그는 [Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md)와 통합될 수 있습니다.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog는 고객 백 엔드에 전달 되는 모든 요청을 기록 합니다. FrontdoorWebApplicationFirewallLog은 WAF 규칙과 일치 하는 모든 요청을 기록 합니다.

다음 예제 쿼리는 차단 된 요청에 대해 WAF 로그를 가져옵니다.

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

다음 예제 쿼리는 AccessLogs 항목을 가져옵니다.

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>다음 단계

- [전면 도어](../../frontdoor/front-door-overview.md)에 대해 자세히 알아보세요.

