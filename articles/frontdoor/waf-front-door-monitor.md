---
title: Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅
description: 모니터링 및 로깅 정문 (frontdoor)를 사용 하 여 웹 응용 프로그램 방화벽 (WAF)에 대해 알아봅니다
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: tyao;kumud
ms.openlocfilehash: e4ba6cca679ce4910ea941d9578939721514b2ec
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66478978"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅 

로그가 azure 웹 응용 프로그램 방화벽 (WAF) 모니터링 및 로깅 로깅과 Azure Monitor 및 Azure Monitor의 통합을 통해 제공 됩니다.

## <a name="azure-monitor"></a>Azure Monitor

정문 (frontdoor) 로그를 사용 하 여 WAF와 통합 되어 [Azure Monitor](../azure-monitor/overview.md)합니다. Azure Monitor를 사용 하면 WAF 경고 및 로그를 포함 하 여 진단 정보를 추적할 수 있습니다. WAF의 포털에서 첫 번째 관문 리소스 내에서 모니터링을 구성할 수 있습니다 합니다 **진단** 탭 또는 Azure Monitor를 통해 직접를 서비스 합니다.

Azure portal에서 첫 번째 관문 리소스 형식으로 이동 합니다. **모니터링**/**메트릭** 탭 왼쪽에 추가할 수 있습니다 **WebApplicationFirewallRequestCount** WAF 규칙을 일치 하는 요청의 수를 추적할 수 있습니다. 동작 유형 이름과 규칙에 따라 사용자 지정 필터를 만들 수 있습니다.

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>로그 및 진단

첫 번째 관문을 사용 하 여 WAF를 검색 하는 각 위협에 대 한 상세 보고를 제공 합니다. 로깅은 Azure Diagnostics 로그에 통합되고 경고는 json 형식으로 기록됩니다. 이러한 로그는 [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)와 통합될 수 있습니다.

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog 고객 백 엔드에 전달 되는 모든 요청을 기록 합니다. FrontdoorWebApplicationFirewallLog은 WAF 규칙과 일치 하는 모든 요청을 기록 합니다.

다음 예제 쿼리를 차단 된 요청에 WAF 로그를 가져옵니다.

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

다음 예제 쿼리에서 AccessLogs 항목을 가져옵니다.

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [첫 번째 관문](front-door-overview.md)합니다.

