---
title: Azure alerts의 로그 경고에 대 한 웹 후크 작업
description: Webhook 작업 및 사용 가능한 사용자 지정을 사용 하 여 로그 경고 푸시를 구성 하는 방법을 설명 합니다.
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 06/25/2019
ms.subservice: alerts
ms.openlocfilehash: 9a074be9bcc62d8c20635400f462f52fb796d2fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294311"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업

[로그 경고](alerts-log.md) 는 [webhook 작업 그룹 구성을](action-groups.md#webhook)지원 합니다. 이 문서에서는 사용할 수 있는 속성 및 사용자 지정 JSON webhook를 구성 하는 방법을 설명 합니다.

> [!NOTE]
> 사용자 지정 JSON 기반 webhook는 현재 API 버전에서 지원 되지 않습니다. `2020-05-01-preview`

> [!NOTE]
> 웹 후크 통합에 [일반적인 경고 스키마](alerts-common-schema.md) 를 사용 하는 것이 좋습니다. 일반적인 경고 스키마는 Azure Monitor의 모든 경고 서비스에서 단일 확장 가능 하 고 통합 된 경고 페이로드를 포함 하는 이점을 제공 합니다. 사용자 지정 JSON 페이로드가 정의 된 로그 경고 규칙의 경우 공통 스키마를 사용 하도록 설정 하면 페이로드 스키마가 [여기](alerts-common-schema-definitions.md#log-alerts)에 설명 된 것으로 되돌아갑니다. 공통 스키마를 사용 하는 경고는 경고 당 256 KB로 제한 됩니다. 더 큰 경고에는 검색 결과가 포함 되지 않습니다. 검색 결과가 포함 되지 않은 경우 또는를 사용 하 여 `LinkToFilteredSearchResultsAPI` `LinkToSearchResultsAPI` Log Analytics API를 통해 쿼리 결과에 액세스 해야 합니다.

## <a name="webhook-payload-properties"></a>Webhook 페이로드 속성

웹 후크 작업을 통해 단일 HTTP POST 요청을 호출할 수 있습니다. 호출 되는 서비스는 웹 후크를 지원 하 고 수신 하는 페이로드를 사용 하는 방법을 알고 있어야 합니다.

기본 webhook 작업 속성 및 해당 사용자 지정 JSON 매개 변수 이름:

| 매개 변수 | 변수 | 설명 |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |경고 규칙의 이름입니다. |
| *심각도* |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| *AlertThresholdOperator* |#thresholdoperator |경고 규칙에 대한 임계값 연산자입니다. |
| *AlertThresholdValue* |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| *LinkToSearchResults* |#linktosearchresults |경고를 만든 쿼리에서 레코드를 반환 하는 분석 포털에 대 한 링크입니다. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |경고를 만든 쿼리에서 레코드를 반환 하는 분석 API에 대 한 링크입니다. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |경고를 만든 차원 값 조합 별로 필터링 된 쿼리에서 레코드를 반환 하는 분석 포털에 대 한 링크입니다. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |경고를 만든 차원 값 조합 별로 필터링 된 쿼리에서 레코드를 반환 하는 분석 API에 대 한 링크입니다. |
| *ResultCount* |#searchresultcount |검색 결과의 레코드 수입니다. |
| *검색 간격 종료 시간* |#searchintervalendtimeutc |UTC 형식의 쿼리에 대 한 종료 시간 (mm/dd/yyyy HH: mm: ss AM/PM 형식)입니다. |
| *검색 간격* |#searchinterval |경고 규칙에 대 한 시간 창에 HH: mm: ss 형식을 사용 합니다. |
| *검색 간격 시작 시간* |#searchintervalstarttimeutc |UTC 형식의 쿼리 시작 시간 (mm/dd/yyyy HH: mm: ss AM/PM 형식)입니다. 
| *SearchQuery* |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| *SearchResults* |"IncludeSearchResults": true|처음 1000 레코드로 제한 되는 JSON 테이블로 쿼리를 통해 반환 된 레코드입니다. "IncludeSearchResults": true는 사용자 지정 JSON 웹 후크 정의에 최상위 속성으로 추가 됩니다. |
| *차원* |"IncludeDimensions": true|JSON 섹션으로 해당 경고를 트리거한 차원 값 조합입니다. "IncludeDimensions": true는 사용자 지정 JSON 웹 후크 정의에 최상위 속성으로 추가 됩니다. |
| *경고 유형*| #alerttype | [메트릭 측정 또는 결과 수](alerts-unified-log.md#measure)로 구성 된 로그 경고 규칙의 유형입니다.|
| *WorkspaceID* |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| *애플리케이션 ID* |#applicationid |Application Insights 앱의 ID입니다. |
| *구독 ID* |#subscriptionid |사용 되는 Azure 구독의 ID입니다. |

## <a name="custom-webhook-payload-definition"></a>사용자 지정 webhook 페이로드 정의

위의 매개 변수를 사용 하 여 사용자 지정 JSON 페이로드를 가져오기 위해 **webhook에 대 한 사용자 지정 json 페이로드 포함** 을 사용할 수 있습니다. 추가 속성을 생성할 수도 있습니다.
예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다. 이 webhook가 호출 하는 서비스에는 다음 매개 변수가 필요 합니다.

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
사용자 지정 webhook의 변수는 JSON 인클로저 내에서 지정 해야 합니다. 예를 들어 위의 webhook 예제에서 "#searchresultcount"을 참조 하면 경고 결과에 따라 출력 됩니다.

검색 결과를 포함 하려면 사용자 지정 JSON에서 **Includesearchresults** 를 최상위 속성으로 추가 합니다. 검색 결과는 JSON 구조로 포함 되므로 사용자 지정 정의 된 필드에서 결과를 참조할 수 없습니다. 

> [!NOTE]
> **Webhook에 대 한 사용자 지정 JSON 페이로드 포함** 옵션 옆에 있는 **webhook 보기** 단추는 제공 된 항목의 미리 보기를 표시 합니다. 실제 데이터는 포함 되지 않지만 사용 될 JSON 스키마를 나타냅니다. 

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 로그 경고에 대 한 웹 후크에 대 한 샘플 페이로드를 보여 줍니다. 샘플 페이로드에는 페이로드가 표준 이며 사용자 지정 인 경우 예제가 포함 됩니다.

### <a name="log-alert-for-log-analytics"></a>Log Analytics에 대 한 로그 경고
다음 샘플 페이로드는 Log Analytics 기반으로 하는 경고에 사용 되는 표준 웹 후크 작업에 대 한 것입니다.

> [!NOTE]
> [레거시 Log Analytics 경고 api](api-alerts.md)에서 [현재 scheduledQueryRules api로 전환한](alerts-log-api-switch.md) 경우 "심각도" 필드 값이 변경 됩니다.

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Application Insights에 대 한 로그 경고
다음 샘플 페이로드는 Application Insights 리소스를 기반으로 하는 로그 경고에 사용 되는 표준 웹 후크에 대 한 것입니다.
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>다른 리소스 로그 (API 버전에서)에 대 한 경고 로그 `2020-05-01-preview`

> [!NOTE]
> 현재 API 버전 `2020-05-01-preview` 및 리소스 중심 로그 경고에 대 한 추가 요금은 없습니다.  미리 보기에 있는 기능의 가격은 추후 발표 되며 청구를 시작 하기 전에 제공 되는 공지가 제공 됩니다. 알림 기간이 끝난 후 새 API 버전 및 리소스 중심 로그 경고를 계속 사용 하도록 선택 하는 경우 해당 요금에 대 한 요금이 청구 됩니다.

다음 샘플 페이로드는 다른 리소스 로그 (작업 영역 및 Application Insights 제외)를 기반으로 하는 로그 경고에 사용 되는 경우 표준 webhook 용입니다.

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>사용자 지정 JSON 페이로드를 사용 하는 로그 경고
예를 들어 경고 이름과 검색 결과만 포함 하는 사용자 지정 페이로드를 만들려면 다음 구성을 사용 합니다. 

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
- [Application Insights](../log-query/log-query-overview.md)에 대해 자세히 알아봅니다.
- [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아보세요. 
