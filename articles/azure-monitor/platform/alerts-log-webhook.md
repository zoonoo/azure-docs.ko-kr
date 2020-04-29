---
title: Azure alerts의 로그 경고에 대 한 웹 후크 작업
description: 이 문서에서는 Log Analytics 작업 영역 또는 Application Insights를 사용 하 여 로그 경고 규칙을 만드는 방법, 경고에서 데이터를 HTTP webhook로 푸시하는 방법 및 가능한 여러 사용자 지정 항목에 대해 설명 합니다.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 7b1956ad2bf9bf38ba9edc4c7234078557564071
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667706"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업
[Azure에서 로그 경고가 생성 되](alerts-log.md)면 [작업 그룹을 사용](action-groups.md) 하 여 하나 이상의 작업을 수행 하는 옵션을 구성할 수 있습니다. 이 문서에서는 사용 가능한 다양 한 웹 후크 작업을 설명 하 고 사용자 지정 JSON 기반 webhook를 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> Webhook 통합에 대 한 [일반적인 경고 스키마](https://aka.ms/commonAlertSchemaDocs) 를 사용할 수도 있습니다. 일반적인 경고 스키마는 Azure Monitor의 모든 경고 서비스에서 확장 가능 하 고 통합 된 단일 경고 페이로드를 포함 하는 이점을 제공 합니다. 일반적인 경고 스키마는 로그 경고에 대 한 사용자 지정 JSON 옵션을 사용할 수 없습니다. 경고 규칙 수준에서 수행 했을 수 있는 사용자 지정에 관계 없이 선택 된 경우 일반적인 경고 스키마 페이로드로 지연 됩니다. [일반적인 경고 스키마 정의에 대해 알아봅니다.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>웹후크 작업

Webhook 작업을 사용 하면 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다. 호출 되는 서비스는 웹 후크를 지원 하 고 수신 하는 페이로드를 사용 하는 방법을 결정 해야 합니다.

웹후크 작업에는 다음 표의 속성이 필요합니다.

| 속성 | 설명 |
|:--- |:--- |
| **Webhook URL** |웹후크의 URL입니다. |
| **사용자 지정 JSON 페이로드** |경고를 만드는 동안이 옵션을 선택 하면 웹 후크에 보낼 사용자 지정 페이로드입니다. 자세한 내용은 [로그 경고 관리](alerts-log.md)를 참조 하세요.|

> [!NOTE]
> 로그 경고에 대 한 웹 후크 **에 대 한 사용자 지정 JSON 페이로드 포함** 옵션과 함께 **webhook 보기** 단추는 제공 된 사용자 지정 항목에 대 한 샘플 Webhook 페이로드를 표시 합니다. 실제 데이터는 포함 되지 않지만 로그 경고에 사용 되는 JSON 스키마를 나타냅니다. 

웹 후크에는 데이터가 외부 서비스로 전송 되는 JSON으로 형식이 지정 된 URL 및 페이로드가 포함 됩니다. 기본적으로 페이로드는 다음 표의 값을 포함합니다. 이 페이로드를 자신만의 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다. 이 경우 각 매개 변수에 대 한 테이블의 변수를 사용 하 여 사용자 지정 페이로드에 값을 포함 합니다.


| 매개 변수 | 변수 | Description |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |경고 규칙의 이름입니다. |
| *심각도* |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| *AlertThresholdOperator* |#thresholdoperator |보다 큼 또는 보다 작음을 사용 하는 경고 규칙에 대 한 임계값 연산자입니다. |
| *AlertThresholdValue* |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| *LinkToSearchResults* |#linktosearchresults |경고를 만든 쿼리에서 레코드를 반환 하는 분석 포털에 대 한 링크입니다. |
| *ResultCount* |#searchresultcount |검색 결과의 레코드 수입니다. |
| *검색 간격 종료 시간* |#searchintervalendtimeutc |UTC 형식의 쿼리에 대 한 종료 시간 (mm/dd/yyyy HH: mm: ss AM/PM 형식)입니다. |
| *검색 간격* |#searchinterval |경고 규칙에 대 한 시간 창에 HH: mm: ss 형식을 사용 합니다. |
| *검색 간격 시작 시간* |#searchintervalstarttimeutc |UTC 형식의 쿼리 시작 시간 (mm/dd/yyyy HH: mm: ss AM/PM 형식)입니다. 
| *SearchQuery* |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| *SearchResults* |"IncludeSearchResults": true|"IncludeSearchResults": true가 사용자 지정 JSON 웹 후크 정의에 최상위 속성으로 추가 된 경우 쿼리를 통해 JSON 테이블로 반환 되는 레코드는 처음 1000 레코드로 제한 됩니다. |
| *경고 유형*| #alerttype | [메트릭 측정](alerts-unified-log.md#metric-measurement-alert-rules) 또는 [결과 수](alerts-unified-log.md#number-of-results-alert-rules)로 구성 된 로그 경고 규칙의 유형입니다.|
| *WorkspaceID* |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| *응용 프로그램 ID* |#applicationid |Application Insights 앱의 ID입니다. |
| *구독 ID* |#subscriptionid |사용 되는 Azure 구독의 ID입니다. 

> [!NOTE]
> *Linktosearchresults* 는 *searchquery*, *search Interval StartTime*및 URL의 *검색 간격 종료 시간과* 같은 매개 변수를 분석 섹션에서 볼 수 있도록 Azure Portal에 전달 합니다. Azure Portal의 URI 크기 제한은 약 2000 자입니다. 매개 변수 값이 제한을 초과 하면 포털에서 경고에 제공 된 링크를 열지 *않습니다* . 분석 포털에서 결과를 볼 수 있도록 정보를 수동으로 입력할 수 있습니다. 또는 [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) 또는 [Log Analytics REST API](/rest/api/loganalytics/) 를 사용 하 여 결과를 프로그래밍 방식으로 검색할 수 있습니다. 

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다. 이 webhook가 호출 하는 서비스에는이 매개 변수가 필요 합니다.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
이 예제 페이로드는 webhook로 전송 될 때 다음과 같은 것으로 확인 됩니다.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
사용자 지정 webhook의 모든 변수는 "#searchinterval"와 같이 JSON 엔클로저 내에서 지정 해야 하므로 결과 webhook에는 "00:05:00"와 같은 인클로저 내에 변수 데이터가 있습니다.

검색 결과를 사용자 지정 페이로드에 포함 하려면 **Includesearchresults** 가 JSON 페이로드의 최상위 속성으로 설정 되어 있는지 확인 합니다. 

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 로그 경고에 대 한 웹 후크에 대 한 샘플 페이로드를 보여 줍니다. 샘플 페이로드에는 페이로드가 표준 이며 사용자 지정 인 경우 예제가 포함 됩니다.

### <a name="standard-webhook-for-log-alerts"></a>로그 경고에 대 한 표준 webhook 
이러한 두 예제에는 두 개의 열과 두 개의 행만 포함 된 더미 페이로드가 있습니다.

#### <a name="log-alert-for-log-analytics"></a>Log Analytics에 대 한 로그 경고
다음 샘플 페이로드는 Log Analytics 기반으로 하는 경고에 사용 되는 *사용자 지정 JSON 옵션이 없는* 표준 웹 후크 작업에 대 한 것입니다.

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
> Log Analytics에서 로그 경고에 대 한 [API 기본 설정을 전환](alerts-log-api-switch.md) 했다면 "심각도" 필드 값이 변경 될 수 있습니다.


#### <a name="log-alert-for-application-insights"></a>Application Insights에 대 한 로그 경고
다음 샘플 페이로드는 Application Insights 기반으로 하는 로그 경고에 사용 될 때 *사용자 지정 JSON 옵션이 없는* 표준 webhook 용입니다.
    
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

#### <a name="log-alert-with-custom-json-payload"></a>사용자 지정 JSON 페이로드를 사용 하는 로그 경고
예를 들어 경고 이름과 검색 결과만 포함 하는 사용자 지정 페이로드를 만들려면 다음을 사용할 수 있습니다. 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

다음 샘플 페이로드는 모든 로그 경고에 대 한 사용자 지정 webhook 작업을 위한 것입니다.
    
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
- [Azure alerts의 로그 경고](alerts-unified-log.md)에 대해 알아봅니다.
- [Azure에서 로그 경고를 관리](alerts-log.md)하는 방법을 이해 합니다.
- [Azure에서 작업 그룹](action-groups.md)을 만들고 관리 합니다.
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
- [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아보세요. 

