---
title: Azure Alerts의 로그 경고에 대한 웹후크 작업
description: 이 문서에서는 어떻게 로그 경고 규칙에 log analytics 작업 영역 또는 application insights를 사용 하 여 가능한 데이터를 푸시하 HTTP 웹 후크 및 다른 사용자 지정의 세부 사항으로 설명 합니다.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 2307fa985c88608d80400c8951c47b9f20caa1dc
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006625"
---
# <a name="webhook-actions-for-log-alert-rules"></a>로그 경고 규칙에 대한 웹후크 작업
[Azure에서 로그 경고를 생성](alerts-log.md)할 때 하나 이상의 작업을 수행하도록 선택적으로 [작업 그룹 사용을 구성](action-groups.md)할 수 있습니다.  이 문서에서는 사용 가능한 다양한 웹후크 작업 및 사용자 지정 JSON 기반 웹후크 구성에 대해 설명합니다.


## <a name="webhook-actions"></a>웹후크 작업

웹후크 작업을 사용하여 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다.  호출 중인 서비스는 웹후크를 지원하고 수신하는 페이로드를 사용하는 방법을 결정해야 합니다.    

웹후크 작업에는 다음 표의 속성이 필요합니다.

| 자산 | 설명 |
|:--- |:--- |
| Webhook URL |웹후크의 URL입니다. |
| 사용자 지정 JSON 페이로드 |경고를 만드는 동안 이 옵션을 선택한 경우 웹후크와 함께 전송할 사용자 지정 페이로드입니다. [로그 경고 관리](alerts-log.md)에서 사용 가능한 세부사항 |

> [!NOTE]
> 로그 경고의 *웹후크에 대한 사용자 정의 JSON 페이로드 포함* 옵션과 함께 웹 후크 보기 단추를 누르면 제공된 사용자 정의용 샘플 웹후크 페이로드가 표시됩니다. 로그 경고에 사용된 JSON 스키마의 실제 데이터 및 표현은 포함되지 않습니다. 

웹후크는 URL 및 외부 서비스에 보낸 데이터인 JSON 형식의 페이로드를 포함하고 있습니다.  기본적으로 페이로드는 다음 표의 값을 포함합니다.  이 페이로드를 자신만의 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다.  그러한 경우 각 매개 변수에 대한 표의 변수를 사용하여 해당 값을 사용자 지정 페이로드에 포함할 수 있습니다.


| 매개 변수 | 변수 | 설명 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |경고 규칙의 이름입니다. |
| 심각도 |#severity |실행된 로그 경고에 대해 설정된 심각도입니다. |
| AlertThresholdOperator |#thresholdoperator |경고 규칙에 대한 임계값 연산자입니다.  *보다 큼* 또는 *보다 작음*. |
| AlertThresholdValue |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| LinkToSearchResults |#linktosearchresults |경고를 생성한 쿼리에서 레코드를 반환하는 Analytics 포털의 링크입니다. |
| ResultCount |#searchresultcount |검색 결과의 레코드 수입니다. |
| 검색 간격 종료 시간 |#searchintervalendtimeutc |쿼리의 종료 시간(UTC)으로, mm/dd/yyyy HH:mm:ss AM/PM 형식입니다. |
| 검색 간격 |#searchinterval |경고 규칙의 시간으로, HH:mm:ss 형식입니다. |
| 검색 간격 시작 시간 |#searchintervalstarttimeutc |쿼리의 시작 시간(UTC)으로, mm/dd/yyyy HH:mm:ss AM/PM 형식입니다. 
| SearchQuery |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| SearchResults |"IncludeSearchResults": true|“IncludeSearchResults”:true를 사용자 지정 JSON 웹후크 정의에 최상위 속성으로 추가할 경우 쿼리에서 JSON 테이블로 반환되는 레코드이며, 처음 1,000개 레코드로 제한됩니다. |
| WorkspaceID |#workspaceid |Log Analytics 작업 영역의 ID입니다. |
| 애플리케이션 UI |#applicationid |Application Insight 앱의 ID입니다. |
| 구독 ID |#subscriptionid |Application Insights에서 사용되는 Azure 구독의 ID입니다. 

> [!NOTE]
> LinkToSearchResults는 분석 섹션에서 보기 위해 URL의 SearchQuery, 검색 간격 시작 시간 및 검색 간격 종료 시간 같은 매개 변수를 Azure Portal에 전달합니다. Azure 포털에 크기 약 2,000 자입니다. 제한 하 고 URI *되지* 매개 변수 값에 언급된 한도 초과 하는 경우의 경고에서 제공 하는 링크 열기. 사용자는 세부 정보를 수동으로 입력하여 Analytics 포털에서 결과를 보거나, [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) 또는 [Log Analytics REST API](/rest/api/loganalytics/)를 사용하여 결과를 프로그래밍 방식으로 검색할 수 있습니다. 

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다.  이 웹후크가 호출하는 서비스는 이 매개 변수를 예상합니다.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
이 예제 페이로드는 웹후크에 보낼 때 다음과 같은 내용으로 확인될 수 있습니다.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
사용자 지정 웹후크의 모든 변수는 "#searchinterval"처럼 JSON 엔클로저 내에 지정되어야 하므로 그 결과로 생기는 웹후크 역시 "00:05:00"처럼 엔클로저 내의 변수 데이터를 갖습니다.

사용자 지정 페이로드에 검색 결과를 포함하려면 **IncludeSearchResults**가 json 페이로드에 최상위 속성으로 설정되어 있는지 확인합니다. 

## <a name="sample-payloads"></a>샘플 페이로드
이 섹션에서는 페이로드가 표준인 경우와 사용자 지정인 경우를 포함하여 로그 경고에 대한 웹후크의 샘플 페이로드를 보여 줍니다.

### <a name="standard-webhook-for-log-alerts"></a>로그 경고에 대한 표준 웹후크 
이 두 가지 예제에서 모두, 열과 행이 각각 두 개만 포함된 더미 페이로드를 언급했습니다.

#### <a name="log-alert-for-azure-log-analytics"></a>Azure Log Analytics에 대한 로그 경고
다음은 로그 분석 기반 경고에 사용되는 *사용자 지정 Json 옵션이 없는* 표준 웹후크 작업의 샘플 페이로드입니다.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> 있는 경우 심각도 필드 값이 변경 될 수 있습니다 [API 기본 설정 전환](alerts-log-api-switch.md) Log Analytics에서 로그 경고에 대 한 합니다.


#### <a name="log-alert-for-azure-application-insights"></a>Azure Application Insights에 대한 로그 경고
다음은 Application Insights 기반 로그 경고에 사용할 경우 *사용자 지정 JSON 옵션이 없는* 표준 웹후크의 샘플 페이로드입니다.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
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
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>사용자 지정 JSON 페이로드가 있는 로그 경고
예를 들어 경고 이름과 검색 결과만 포함하는 사용자 지정 페이로드를 만들려면 다음을 사용할 수 있습니다. 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

다음은 로그 경고에 대한 사용자 지정 웹후크 작업의 샘플 페이로드입니다.
    
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
- [Azure Alerts의 로그 경고](alerts-unified-log.md)에 대해 알아보기
- [Azure에서 로그 경고 관리](alerts-log.md)를 파악합니다.
- [Azure에서 작업 그룹](action-groups.md) 만들기 및 관리
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아보기
- 에 대해 자세히 알아보세요 [쿼리를 로깅](../log-query/log-query-overview.md)합니다. 

