---
title: Azure Web Application Firewall 모니터링 및 로깅
description: FrontDoor 모니터링 및 로깅을 사용하는 WAF(웹 애플리케이션 방화벽) 알아보기
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84808944"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web Application Firewall 모니터링 및 로깅

Azure WAF(웹 애플리케이션 방화벽) 모니터링 및 로깅은 로깅과 Azure Monitor 및 Azure Monitor 로그와의 통합을 통해 제공됩니다.

## <a name="azure-monitor"></a>Azure Monitor

FrontDoor 로그가 포함된 WAF가 [Azure Monitor](../../azure-monitor/overview.md)와 통합됩니다. Azure Monitor를 통해 WAF 경고 및 로그를 포함하여 진단 정보를 추적할 수 있습니다. 포털의 Front Door 리소스 내의 **진단** 탭 아래에서 또는 직접 Azure Monitor 서비스를 통해 WAF 모니터링을 구성할 수 있습니다.

Azure Portal에서 Front Door 리소스 유형으로 이동합니다. 왼쪽의 **모니터링**/**메트릭** 탭에서 **WebApplicationFirewallRequestCount** 를 추가하여 WAF 규칙과 일치하는 요청 수를 추적할 수 있습니다. 작업 유형 및 규칙 이름에 따라 사용자 지정 필터를 만들 수 있습니다.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>로그 및 진단

Front Door를 사용하는 WAF는 감지된 각 위협에 대한 상세 보고를 제공합니다. 로깅은 Azure Diagnostics 로그에 통합되고 경고는 json 형식으로 기록됩니다. 이러한 로그는 [Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md)와 통합될 수 있습니다.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

[FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md)는 모든 요청을 기록합니다. FrontdoorWebApplicationFirewallLog는 아래 스키마를 포함하는 WAF 규칙과 일치하는 모든 요청을 기록합니다.

| 속성  | 설명 |
| ------------- | ------------- |
|작업|요청에서 수행되는 동작입니다.|
| ClientIp | 요청한 클라이언트의 IP 주소입니다. 요청에 X-Forwarded-For 헤더가 있는 경우 클라이언트 IP는 헤더 필드에서 선택됩니다. |
| ClientPort | 요청한 클라이언트의 IP 포트입니다. |
| 세부 정보|일치하는 요청에 대한 추가 정보 |
|| matchVariableName: 헤더 이름과 같이 일치하는 요청의 http 매개 변수 이름|
|| matchVariableValue: 일치를 트리거한 값|
| 호스트 | 일치하는 요청의 호스트 헤더 |
| 정책 | 요청에 일치하는 WAF 정책의 이름입니다. |
| PolicyMode | WAF 정책의 작업 모드입니다. 가능한 값은 "방지" 및 "검색"입니다. |
| RequestUri | 일치하는 요청의 전체 URI입니다. |
| RuleName | 요청에 일치하는 WAF 규칙의 이름입니다. |
| SocketIp | WAF에 표시되는 원본 IP 주소입니다. 이 IP 주소는 요청 헤더와 관계 없이 TCP 세션을 기반으로 합니다.|
| TrackingReference | Front Door에서 제공하는 요청을 식별하는 고유한 참조 문자열로, 클라이언트에 X-Azure-Ref 헤더로 전송됩니다. 특정 요청의 액세스 로그에서 세부 정보를 검색하는 데 필요합니다. |

다음 쿼리 예에서는 차단된 요청에 대한 WAF 로그를 반환합니다.

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

다음은 WAF 로그의 기록된 요청의 예입니다.

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

다음 예제 쿼리는 AccessLogs 항목을 반환합니다.

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

다음은 액세스 로그의 기록된 요청의 예입니다.

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

- [Front Door](../../frontdoor/front-door-overview.md)에 대해 자세히 알아봅니다.
