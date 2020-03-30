---
title: Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅
description: FrontDoor 모니터링 및 로깅을 통해 WAF(웹 응용 프로그램 방화벽) 알아보기
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 4488fadf5db3b32049b5dce4bbee1fa76c320e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284146"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure 웹 응용 프로그램 방화벽 모니터링 및 로깅 

Azure 웹 응용 프로그램 방화벽(WAF) 모니터링 및 로깅은 Azure 모니터 및 Azure 모니터 로그와의 로깅 및 통합을 통해 제공됩니다.

## <a name="azure-monitor"></a>Azure Monitor

FrontDoor 로그가 있는 WAF는 [Azure 모니터.](../../azure-monitor/overview.md) Azure Monitor를 사용하면 WAF 경고 및 로그를 포함한 진단 정보를 추적할 수 있습니다. 진단 탭 아래의 포털또는 Azure Monitor 서비스를 통해 포털의 정문 리소스 내에서 WAF 모니터링을 직접 구성할 수 **있습니다.**

Azure 포털에서 정문 리소스 유형으로 이동합니다. 왼쪽의 **모니터링**/**메트릭** 탭에서 **WebApplicationRequestRequestCount를** 추가하여 WAF 규칙과 일치하는 요청 수를 추적할 수 있습니다. 사용자 지정 필터는 작업 유형 및 규칙 이름에 따라 만들 수 있습니다.

![WAF메트릭](../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>로그 및 진단

전면 도어가 있는 WAF는 감지하는 각 위협에 대한 자세한 보고를 제공합니다. 로깅은 Azure Diagnostics 로그에 통합되고 경고는 json 형식으로 기록됩니다. 이러한 로그는 [Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md)와 통합될 수 있습니다.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog는 고객 백 엔드로 전달되는 모든 요청을 기록합니다. FrontdoorWebApplication방화벽로그는 WAF 규칙과 일치하는 모든 요청을 기록합니다.

다음 예제 쿼리는 차단된 요청에 대한 WAF 로그를 가져옵니다.

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_name_s == "Block"

```

다음은 WAF 로그에 기록된 요청의 예입니다.

``` WAFlogQuerySample
{
    "PreciseTimeStamp": "2020-01-25T00:11:19.3866091Z",
    "time": "2020-01-25T00:11:19.3866091Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "xx.xx.xxx.xxx",
        "socketIP": "xx.xx.xxx.xxx",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/?q=../../x",
        "ruleName": "Microsoft_DefaultRuleSet-1.1-LFI-930100",
        "policy": "WafDemoCustomPolicy",
        "action": "Block",
        "host": "wafdemofrontdoorwebapp.azurefd.net",
        "refString": "0p4crXgAAAABgMq5aIpu0T6AUfCYOroltV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode": "prevention"
    }
}

``` 

다음 예제 쿼리는 AccessLogs 항목을 가져옵니다.

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

다음은 Access 로그에 기록된 요청의 예입니다.

``` AccessLogSample
{
    "PreciseTimeStamp": "2020-01-25T00:11:12.0160150Z",
    "time": "2020-01-25T00:11:12.0160150Z",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0n4crXgAAAACnRKbdALbyToAqNfSHssDvV1NURURHRTA2MTMANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "httpMethod": "GET",
        "httpVersion": "2.0",
        "requestUri": "https://wafdemofrontdoorwebapp.azurefd.net:443/",
        "requestBytes": "710",
        "responseBytes": "3116",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4017.0 Safari/537.36 Edg/81.0.389.2",
        "clientIp": "xx.xx.xxx.xxx",
        "timeTaken": "0.598",
        "securityProtocol": "TLS 1.2",
        "routingRuleName": "WAFdemoWebAppRouting",
        "backendHostname": "wafdemouksouth.azurewebsites.net:443",
        "sentToOriginShield": false,
        "httpStatusCode": "200",
        "httpStatusDetails": "200"
    }
}

```

## <a name="next-steps"></a>다음 단계

- [정문에](../../frontdoor/front-door-overview.md)대해 자세히 알아보십시오.
