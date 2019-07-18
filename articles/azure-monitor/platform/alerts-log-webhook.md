---
title: Azure alerts의 로그 경고에 대 한 웹 후크 작업
description: 이 문서에서는 Log Analytics 작업 영역 또는 Application Insights를 사용 하 여 로그 경고 규칙을 만드는 방법 설명 경고 HTTP 웹 후크를 사용할 수 있는 다른 사용자 지정의 세부 정보로 데이터를 푸시하는 방법입니다.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 6aa007c621e76cb0c188a7dab6279fd9e387b2b3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705184"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업
경우는 [Azure에서 로그 경고를 만들](alerts-log.md)의 옵션이 있습니다 [작업 그룹을 사용 하 여 구성](action-groups.md) 하나 이상의 작업을 수행 합니다. 이 문서는 사용할 수 있는 다양 한 웹 후크 작업을 설명 하 고 사용자 지정 JSON 기반 웹 후크를 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> 사용할 수도 있습니다는 [일반적인 경고 스키마](https://aka.ms/commonAlertSchemaDocs) 웹 후크 통합에 대 한 합니다. 일반 경고 스키마는 Azure Monitor에서 모든 경고 서비스에서 단일 확장 가능 하 고 통합 된 경고 페이로드는 이점이 제공 합니다. [일반 경고 스키마 정의에 대해 알아봅니다.](https://aka.ms/commonAlertSchemaDefinitions)

## <a name="webhook-actions"></a>웹후크 작업

웹 후크 작업을 사용 하 여 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다. 호출 되는 서비스는 웹 후크를 지원 하 고 수신한 모든 페이로드를 사용 하는 방법을 결정 해야 합니다.

웹후크 작업에는 다음 표의 속성이 필요합니다.

| 속성 | Description |
|:--- |:--- |
| **Webhook URL** |웹후크의 URL입니다. |
| **사용자 지정 JSON 페이로드** |경고를 만드는 동안이 옵션을 선택한 경우 웹 후크와 함께 보낼 사용자 지정 페이로드입니다. 자세한 내용은 [관리 로그 경고](alerts-log.md)합니다.|

> [!NOTE]
> **보기 웹 후크** 함께 단추를 **webhook에 대 한 사용자 지정 JSON 페이로드 포함** 제공 된 사용자 지정에 대 한 샘플 웹 후크 페이로드를 표시 하는 로그 경고에 대 한 옵션입니다. 실제 데이터를 포함 하지 않지만 로그 경고에 사용 되는 JSON 스키마의 일부를 보여 줍니다. 

웹 후크 URL 및 외부 서비스에 데이터를 전송 하는 JSON 형식의 페이로드를 포함 합니다. 기본적으로 페이로드는 다음 표의 값을 포함합니다. 이 페이로드를 자신만의 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다. 이 경우 변수를 사용할 테이블의 각 매개 변수에 대 한 해당 값을 사용자 지정 페이로드에 포함 하려면.


| 매개 변수 | 변수 | Description |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |경고 규칙의 이름입니다. |
| *Severity* |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| *AlertThresholdOperator* |#thresholdoperator |보다 크거나 작은 사용 하는 경고 규칙에 대 한 임계값 연산자 보다 합니다. |
| *AlertThresholdValue* |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| *LinkToSearchResults* |#linktosearchresults |경고를 생성 하는 쿼리에서 레코드를 반환 하는 Analytics 포털에 연결 합니다. |
| *ResultCount* |#searchresultcount |검색 결과의 레코드 수입니다. |
| *검색 간격 종료 시간* |#searchintervalendtimeutc |종료 시간 utc에서 형식 mm/dd/yyyy hh: mm 사용 하 여 쿼리에 대해: ss AM/PM입니다. |
| *검색 간격* |#searchinterval |H:mm: ss 형식 사용 하 여 경고 규칙에 대 한 기간입니다. |
| *검색 간격 시작 시간* |#searchintervalstarttimeutc |형식 mm/dd/yyyy hh: mm 사용 하 여 쿼리에 대해 시간 UTC에 시작: ss AM/PM입니다. 
| *SearchQuery* |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| *SearchResults* |"IncludeSearchResults": true|경우는 처음 1,000 개의 레코드로 제한 JSON 테이블로 쿼리에 의해 반환 된 레코드 "IncludeSearchResults": true를 사용자 지정 JSON 웹 후크 정의 최상위 속성으로 추가 됩니다. |
| *경고 유형*| #alerttype | 로그 경고 규칙으로 구성 된 유형의 [미터법](alerts-unified-log.md#metric-measurement-alert-rules) 또는 [결과 수가](alerts-unified-log.md#number-of-results-alert-rules)합니다.|
| *WorkspaceID* |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| *응용 프로그램 ID* |#applicationid |Application Insights의 ID 앱. |
| *구독 ID* |#subscriptionid |사용 하는 Azure 구독의 ID입니다. 

> [!NOTE]
> *LinkToSearchResults* 와 같은 매개 변수 전달 *SearchQuery*합니다 *검색 간격 시작 시간*, 및 *검색 간격 종료 시간* Azure URL에 분석 섹션에서 보기에 대 한 포털입니다. Azure portal에 약 2,000 자로의 URI 크기 제한이 있습니다. 포털 됩니다 *되지* 매개 변수 값을 한도 초과 하는 경우 경고에 제공 된 링크를 엽니다. Analytics 포털에서 결과 보려면 세부 정보를 수동으로 입력할 수 있습니다. 또는 사용할 수는 [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) 또는 [Log Analytics REST API](/rest/api/loganalytics/) 결과 프로그래밍 방식으로 검색 하려면. 

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다. 이 웹 후크를 호출 하는 서비스에서는이 매개 변수입니다.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
이 예제 페이로드는 웹 후크에 보낼 때 다음과 같이 해결 됩니다.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
"#Searchinterval,"와 같은 JSON 인클로저, 내에 사용자 지정 웹 후크 안에 있는 모든 변수를 지정 해야 하기 때문에 결과 웹 후크 역시 엔클로저 내의 변수 데이터와 같은 "00: 05:00입니다."

검색 결과 사용자 지정 페이로드에 포함 하려면 **IncludeSearchResults** JSON 페이로드의 최상위 속성으로 설정 됩니다. 

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 로그 알림 위한 webhook에 대 한 샘플 페이로드를 보여줍니다. 샘플 페이로드는 페이로드는 표준 및 사용자 지정 하는 경우 예제를 포함 합니다.

### <a name="standard-webhook-for-log-alerts"></a>로그 경고에 대 한 표준 웹 후크 
이 예제 모두 두 개의 행 및 두 개의 열만 포함 된 더미 페이로드입니다.

#### <a name="log-alert-for-log-analytics"></a>Log Analytics에 대 한 로그 경고
다음 샘플 페이로드는 표준 웹 후크 작업에 대 한 *사용자 지정 JSON 옵션이 없는* Log Analytics 기반 경고에 사용 되는:

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
> "심각도" 필드 값을 설정한 경우 변경 될 수 있습니다 [API 기본 설정 전환](alerts-log-api-switch.md) Log Analytics에서 로그 경고에 대 한 합니다.


#### <a name="log-alert-for-application-insights"></a>Application Insights에 대 한 로그 경고
다음 샘플 페이로드는 표준 웹 후크 *사용자 지정 JSON 옵션이 없는* Application Insights 기반 로그 경고에 대 한 사용 하는 경우:
    
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

#### <a name="log-alert-with-custom-json-payload"></a>사용자 지정 JSON 페이로드를 사용 하 여 로그 경고
예를 들어, 경고 이름 및 검색 결과 포함 하는 사용자 지정 페이로드를 만들려면 사용할 수 있습니다 다음. 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

로그 경고에 대 한 사용자 지정 웹 후크 작업에 대 한 다음 샘플 페이로드가입니다.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
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
        }
    }
```


## <a name="next-steps"></a>다음 단계
- 에 대 한 자세한 [Azure alerts의 로그 경고](alerts-unified-log.md)합니다.
- 이해 하는 방법 [Azure에서 로그 경고를 관리](alerts-log.md)합니다.
- 만들기 및 관리 [Azure에서 작업 그룹](action-groups.md)합니다.
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
- 에 대해 자세히 알아보세요 [쿼리를 로깅](../log-query/log-query-overview.md)합니다. 

