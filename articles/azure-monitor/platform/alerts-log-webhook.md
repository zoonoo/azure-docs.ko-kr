---
title: Azure 경고의 로그 경고에 대한 웹후크 작업
description: 이 문서에서는 Log Analytics 작업 영역 또는 응용 프로그램 인사이트를 사용하여 로그 경고 규칙을 만드는 방법, 경고가 HTTP 웹후크로 데이터를 푸시하는 방법 및 가능한 다양한 사용자 지정에 대한 세부 정보를 설명합니다.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667706"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업
[Azure에서 로그 경고를 만들면](alerts-log.md)작업 그룹을 사용하여 하나 이상의 작업을 [수행하여 로그](action-groups.md) 경고를 구성할 수 있습니다. 이 문서에서는 사용 가능한 다양한 웹후크 작업에 대해 설명하고 사용자 지정 JSON 기반 웹후크를 구성하는 방법을 보여 주며 설명합니다.

> [!NOTE]
> 웹후크 통합에 [공통 경고 스키마를](https://aka.ms/commonAlertSchemaDocs) 사용할 수도 있습니다. 공통 경고 스키마는 Azure Monitor.Common 경고 스키마가 로그 경고에 대한 사용자 지정 JSON 옵션을 사용하지 않는 경우 Azure Monitor..새 경고 서비스의 모든 경고 서비스에서 단일 확장 가능하고 통합된 경고 페이로드를 갖는 이점을 제공합니다. 경고 규칙 수준에서 수행한 사용자 지정에 관계없이 선택된 경우 일반적인 경고 스키마 페이로드로 연기됩니다. [일반적인 경고 스키마 정의에 대해 알아봅니다.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>웹후크 작업

웹후크 작업을 사용하면 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다. 호출된 서비스는 웹후크를 지원하고 수신하는 페이로드를 사용하는 방법을 결정해야 합니다.

웹후크 작업에는 다음 표의 속성이 필요합니다.

| 속성 | 설명 |
|:--- |:--- |
| **Webhook URL** |웹후크의 URL입니다. |
| **사용자 지정 JSON 페이로드** |경고를 만드는 동안 이 옵션을 선택하면 웹후크와 함께 보낼 사용자 지정 페이로드입니다. 자세한 내용은 [로그 경고 관리를](alerts-log.md)참조하십시오.|

> [!NOTE]
> 로그 경고에 **대한 웹후크 옵션에 대한 사용자 지정 JSON 페이로드 포함과** 함께 **웹후크 보기** 단추에는 제공된 사용자 지정에 대한 샘플 웹후크 페이로드가 표시됩니다. 실제 데이터는 포함되지 않지만 로그 경고에 사용되는 JSON 스키마를 대표합니다. 

Webhook에는 데이터가 외부 서비스로 전송되는 JSON에 서식이 지정된 URL및 페이로드가 포함됩니다. 기본적으로 페이로드는 다음 표의 값을 포함합니다. 이 페이로드를 자신만의 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다. 이 경우 테이블의 변수를 사용하여 각 매개 변수에 해당 값을 사용자 지정 페이로드에 포함합니다.


| 매개 변수 | 변수 | 설명 |
|:--- |:--- |:--- |
| *경고 규칙 이름* |#alertrulename |경고 규칙의 이름입니다. |
| *심각도* |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| *AlertThresholdOperator* |#thresholdoperator |보다 크거나 더 적은 값을 사용하는 경고 규칙에 대한 임계값 연산자입니다. |
| *AlertThresholdValue* |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| *링크토서치결과* |#linktosearchresults |경고를 만든 쿼리의 레코드를 반환하는 애널리틱스 포털에 연결합니다. |
| *ResultCount* |#searchresultcount |검색 결과의 레코드 수입니다. |
| *검색 간격 종료 시간* |#searchintervalendtimeutc |UTC에서 쿼리에 대한 종료 시간(MM/dd/yyyy HH:mm:ss AM/PM)을 사용할 수 있습니다. |
| *검색 간격* |#searchinterval |HH:mm:ss 형식이 있는 경고 규칙에 대한 시간 창입니다. |
| *검색 간격 시작 시간* |#searchintervalstarttimeutc |UTC에서 쿼리를 시작할 수 있는 시간(MM/dd/yyyHH:mm:ss AM/PM)을 사용할 수 있습니다. 
| *SearchQuery* |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| *SearchResults* |"IncludeSearchResults": true|쿼리에서 JSON 테이블로 반환되는 레코드는 "포함SearchResults"인 경우 처음 1,000개의 레코드로 제한됩니다. |
| *경고 유형*| #alerttype | [메트릭 측정값](alerts-unified-log.md#metric-measurement-alert-rules) 또는 [결과 수로](alerts-unified-log.md#number-of-results-alert-rules)구성된 로그 경고 규칙의 유형입니다.|
| *작업 공간 ID* |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| *응용 프로그램 ID* |#applicationid |애플리케이션 인사이트 앱의 ID입니다. |
| *구독 ID* |#subscriptionid |사용된 Azure 구독의 ID입니다. 

> [!NOTE]
> *LinkToSearchResults* *는 검색 쿼리,* *검색 간격 시작 시간*및 검색 간격 *종료 시간과* 같은 매개 변수를 탐색 섹션에서 볼 수 있도록 Azure 포털에 전달합니다. Azure 포털에는 약 2,000자로 구성된 URI 크기 제한이 있습니다. 매개 변수 값이 제한을 초과하는 경우 포털은 경고에 제공된 링크를 *열지 않습니다.* 수동으로 세부 정보를 입력하여 애널리틱스 포털에서 결과를 볼 수 있습니다. 또는 응용 프로그램 [인사이트 분석 REST API](https://dev.applicationinsights.io/documentation/Using-the-API) 또는 [로그 분석 REST API를](/rest/api/loganalytics/) 사용하여 프로그래밍 방식으로 결과를 검색할 수 있습니다. 

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다. 이 웹후크가 호출하는 서비스는 이 매개 변수를 기대합니다.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
이 예제 페이로드는 웹후크로 전송될 때 다음과 같은 것으로 확인됩니다.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
사용자 지정 웹후크의 모든 변수는 "#searchinterval"과 같이 JSON 인클로저 내에 지정되어야 하므로 결과 웹후크에는 "00:05:00"과 같은 인클로저 내부에도 가변 데이터가 있습니다.

사용자 지정 페이로드에 검색 결과를 포함하려면 **포함SearchResults가** JSON 페이로드의 최상위 속성으로 설정되어 있는지 확인합니다. 

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 로그 경고에 대한 웹후크에 대한 샘플 페이로드를 보여 주십습니다. 샘플 페이로드에는 페이로드가 표준인 경우와 사용자 지정인 경우의 예가 포함됩니다.

### <a name="standard-webhook-for-log-alerts"></a>로그 경고를 위한 표준 웹후크 
이 두 예제모두 두 개의 열과 두 개의 행만 있는 더미 페이로드를 갖습니다.

#### <a name="log-alert-for-log-analytics"></a>로그 분석에 대한 로그 경고
다음 샘플 페이로드는 Log Analytics를 기반으로 하는 경고에 사용되는 *사용자 지정 JSON 옵션이 없는* 표준 웹후크 작업에 대한 것입니다.

```json
{
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": "log alert rule",
    "Severity": "Warning",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
 }
 ```

> [!NOTE]
> 로그 애널리틱스에서 로그 경고에 대한 [API 기본 설정을 전환한](alerts-log-api-switch.md) 경우 "심각도" 필드 값이 변경될 수 있습니다.


#### <a name="log-alert-for-application-insights"></a>애플리케이션 인사이트를 위한 로그 경고
다음 샘플 페이로드는 응용 프로그램 인사이트를 기반으로 로그 경고에 사용되는 *사용자 지정 JSON 옵션이 없는* 표준 웹후크에 대한 것입니다.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule",
    "SearchQuery":"requests | where resultCode == \"500\"",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "SearchResult":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        },
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
    "AlertType": "Number of results"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>사용자 지정 JSON 페이로드로 로그 경고
예를 들어 경고 이름과 검색 결과만 포함하는 사용자 지정 페이로드를 만들려면 다음을 사용할 수 있습니다. 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

다음 샘플 페이로드는 로그 경고에 대한 사용자 지정 웹후크 작업에 대한 것입니다.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```


## <a name="next-steps"></a>다음 단계
- Azure [경고에서 로그 경고에](alerts-unified-log.md)대해 알아봅니다.
- Azure 에서 로그 경고를 관리하는 방법을 [이해합니다.](alerts-log.md)
- Azure 에서 작업 그룹을 만들고 [관리합니다.](action-groups.md)
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
- [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아봅니다. 

