---
title: Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅
description: FrontDoor 모니터링 및 로깅을 사용 하는 WAF (웹 응용 프로그램 방화벽) 배우기
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808944"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅

Azure WAF (웹 응용 프로그램 방화벽) 모니터링 및 로깅은 로깅 및 Azure Monitor 및 Azure Monitor 로그와의 통합을 통해 제공 됩니다.

## <a name="azure-monitor"></a>Azure Monitor

FrontDoor 로그가 포함 된 WAF는 [Azure Monitor](../../azure-monitor/overview.md)와 통합 됩니다. Azure Monitor를 사용 하면 WAF 경고 및 로그를 포함 한 진단 정보를 추적할 수 있습니다. 포털의 프런트 도어 리소스 내에서 **진단** 탭 또는 Azure Monitor 서비스를 통해 waf 모니터링을 구성할 수 있습니다.

Azure Portal에서 Front 도어 리소스 유형으로 이동 합니다. 왼쪽의 **모니터링** / **메트릭** 탭에서 **WebApplicationFirewallRequestCount** 를 추가 하 여 waf 규칙과 일치 하는 요청 수를 추적할 수 있습니다. 작업 유형 및 규칙 이름에 따라 사용자 지정 필터를 만들 수 있습니다.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics":::

## <a name="logs-and-diagnostics"></a>로그 및 진단

프런트 도어를 사용 하는 WAF는 검색 하는 각 위협에 대해 자세한 보고 기능을 제공 합니다. 로깅은 Azure Diagnostics 로그에 통합되고 경고는 json 형식으로 기록됩니다. 이러한 로그는 [Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md)와 통합될 수 있습니다.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) 모든 요청을 기록 합니다. FrontdoorWebApplicationFirewallLog는 아래 스키마를 포함 하는 WAF 규칙과 일치 하는 모든 요청을 기록 합니다.

| 속성  | 설명 |
| ------------- | ------------- |
|작업|요청에서 수행 된 작업|
| ClientIp | 요청한 클라이언트의 IP 주소입니다. 요청에 X로 전달 된 헤더가 있는 경우 헤더 필드에서 클라이언트 IP가 선택 됩니다. |
| ClientPort | 요청을 수행한 클라이언트의 IP 포트입니다. |
| 설명|일치 된 요청에 대 한 추가 세부 정보 |
|| matchVariableName: 헤더 이름과 같이 일치 하는 요청의 http 매개 변수 이름|
|| matchVariableValue: 일치를 트리거한 값|
| 호스트 | 일치 하는 요청의 호스트 헤더 |
| 정책 | 요청을 일치 시킨 WAF 정책의 이름입니다. |
| PolicyMode | WAF 정책의 작업 모드입니다. 가능한 값은 "예방" 및 "검색"입니다. |
| RequestUri | 일치 하는 요청의 전체 URI입니다. |
| RuleName | 요청을 일치 시킨 WAF 규칙의 이름입니다. |
| SocketIp | WAF에 표시 되는 원본 IP 주소입니다. 이 IP 주소는 요청 헤더와 관계 없이 TCP 세션을 기반으로 합니다.|
| TrackingReference | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |

다음 쿼리 예에서는 차단 된 요청에 대해 WAF 로그를 반환 합니다.

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

WAF 로그에서 기록 된 요청의 예는 다음과 같습니다.

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

다음 예제 쿼리는 AccessLogs 항목을 반환 합니다.

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

액세스 로그에서 기록 된 요청의 예는 다음과 같습니다.

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>다음 단계

- [전면 도어](../../frontdoor/front-door-overview.md)에 대해 자세히 알아보세요.
