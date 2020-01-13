---
title: Application Insights의 스마트 검색 - 실패 | Microsoft Docs
description: 웹앱에 요청 실패율의 비정상적인 변경 내용에 대해 경고하고 진단 분석을 제공합니다. 구성이 필요하지 않습니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yalavi
ms.openlocfilehash: c556f726cd63971abe1e9b6d8b87117bb3e378db
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912847"
---
# <a name="smart-detection---failure-anomalies"></a>스마트 감지 - 실패
웹 앱에서 실패 한 요청 비율이 비정상적으로 증가 하는 경우 [Application Insights](../../azure-monitor/app/app-insights-overview.md) 은 거의 실시간으로 자동으로 경고 합니다. 실패했다고 보고된 HTTP 요청 또는 종속성 호출의 비율이 비정상적으로 증가하는 것을 감지합니다. 요청의 경우 실패 한 요청의 응답 코드는 일반적으로 400 이상입니다. 문제를 심사 하 고 진단 하는 데 도움이 되도록 오류 및 관련 응용 프로그램 데이터의 특징에 대 한 분석이 경고 세부 정보에 제공 됩니다. 또한 추가 진단을 위해 Application Insights 포털에 링크가 제공됩니다. 기능이 Machine Learning 알고리즘을 사용하여 일반 실패율을 예측하려면 설정 또는 구성이 필요하지 않습니다.

이 기능은 응용 프로그램 요청이 나 종속성 데이터를 생성 하는 클라우드 또는 자체 서버에서 호스트 되는 모든 웹 앱에 대해 작동 합니다. 예를 들어, 지 수 [요청 ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 또는 지 수 [종속성 ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)을 호출 하는 작업자 역할이 있는 경우입니다.

[프로젝트에 대 한 Application Insights](../../azure-monitor/app/app-insights-overview.md)를 설정 하 고 앱이 최소한의 데이터를 생성 하는 경우 오류 이상에 대 한 스마트 감지는 앱의 정상적인 동작을 파악 하는 데 24 시간이 걸리고,이를 전환 하 고 경고를 보낼 수 있습니다.

샘플 경고는 다음과 같습니다.

[![](./media/proactive-failure-diagnostics/013.png "Sample smart detection alert showing cluster analysis around failure")](./media/proactive-failure-diagnostics/013.png#lightbox)

경고 정보는 다음을 알려 줍니다.

* 일반 앱 동작과 비교한 실패율
* 영향을 받는 사용자 수 - 얼마나 고려해야 할지 알 수 있습니다.
* 오류와 관련된 특성 패턴. 이 예제에는 특정 응답 코드, 요청 이름 (작업) 및 응용 프로그램 버전이 있습니다. 코드의 어디부터 살펴보아야 하는지 즉시 알려 줍니다. 다른 가능성으로 특정 브라우저 또는 클라이언트 운영 체제가 있을 수 있습니다.
* 특징지어진 실패와 관련된 것으로 보이는 예외, 로그 추적 및 종속성 오류(데이터베이스 또는 다른 외부 구성 요소).
* Application Insights 데이터에 대 한 관련 검색에 직접 연결 합니다.

## <a name="benefits-of-smart-detection"></a>스마트 감지의 이점
일반 [메트릭 경고](../../azure-monitor/app/alerts.md) 는 문제일 수 있음을 알려 줍니다. 그러나 스마트 검색은 진단 작업을 시작 하 여 사용자가 직접 수행 해야 하는 분석을 많이 수행 합니다. 깔끔하게 정리된 결과를 얻고 문제의 원인을 신속하게 파악할 수 있습니다.

## <a name="how-it-works"></a>작동 원리
스마트 감지는 앱에서 받은 데이터 및 특히 실패율을 모니터링 합니다. 이 규칙은 `Successful request` 속성이 false인 요청 수와 `Successful call` 속이 false인 종속성 호출 수를 계산합니다. 요청의 경우 기본적으로 `Successful request == (resultCode < 400)`입니다(사용자 지정 코드를 [필터](../../azure-monitor/app/api-filtering-sampling.md#filtering)에 작성하거나 사용자 고유 [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) 호출을 생성하지 않는 한). 

앱의 성능에는 일반적인 동작의 패턴이 있습니다. 일부 요청 또는 종속성 호출은 다른 요청보다 오류 발생 가능성이 높으며 로드가 증가함에 따라 전체 실패율이 상승할 수 있습니다. 스마트 감지는 이러한 이상을 발견하는 데 기계 학습을 사용합니다.

웹 앱에서 데이터가 Application Insights 되 면 스마트 검색은 현재 동작을 지난 며칠 동안 표시 된 패턴과 비교 합니다. 이전 성능과 비교하여 실패율에 비정상적인 증가가 관찰되면 분석이 트리거됩니다.

분석이 트리거되는 경우 서비스는 실패의 특성을 만드는 값의 패턴을 확인하기 위해 실패한 요청에 대한 클러스터 분석을 수행합니다. 

위의 예에서 분석은 대부분의 오류가 특정 결과 코드, 요청 이름, 서버 URL 호스트 및 역할 인스턴스에 대한 것임을 발견했습니다. 

이러한 호출을 사용 하 여 서비스를 계측 하는 경우 분석기는 해당 요청과 연결 된 추적 로그의 예제와 함께 확인 된 클러스터의 요청과 관련 된 예외 및 종속성 오류를 찾습니다.

다르게 구성하지 않으면 결과 분석은 경고로 전송됩니다.

[수동으로 설정한 경고](../../azure-monitor/app/alerts.md)와 같이 문제가 해결 된 경우 해결 될 수 있는 발생 한 경고의 상태를 검사할 수 있습니다. Application Insights 리소스의 경고 페이지에서 경고 규칙을 구성 합니다. 하지만 다른 경고와 달리 스마트 감지를 설정하거나 구성할 필요가 없습니다. 원하는 경우 해당 대상 전자 메일 주소를 사용하지 않거나 변경할 수 있습니다.

### <a name="alert-logic-details"></a>경고 논리 세부 정보

경고는 독점적인 기계 학습 알고리즘에 의해 트리거됩니다. 따라서 정확한 구현 세부 정보를 공유할 수 없습니다. 동시에 기본 논리의 작동 원리에 대해 자세히 알아야 할 경우가 있다는 점을 이해하고 있습니다. 경고를 트리거해야 하는 경우를 결정하기 위해 평가되는 기본 요인은 다음과 같습니다. 

* 20분의 롤링 시간 범위에서 요청/종속성의 실패율을 분석합니다.
* 최근 20분 동안 실패율을 최근 40분 및 최근 7일 동안과 비교하고 표준 편차의 X배를 초과한 심각한 편차를 찾습니다.
* 최소 실패율에 대해 앱 요청/종속성의 볼륨에 따라 달라지는 적응형 제한을 사용합니다.
* 8-24 시간 동안 문제가 더 이상 검색 되지 않는 경우 발생 하는 경고 모니터 조건을 자동으로 해결할 수 있는 논리가 있습니다.

## <a name="configure-alerts"></a>경고 구성

포털에서 또는 Azure Resource Manager를 사용 하 여 스마트 감지 경고 규칙을 사용 하지 않도록 설정할 수 있습니다 ([템플릿 예제 참조](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)).

이 경고 규칙은 전자 메일 및 웹 후크 작업을 포함 하는 "Application Insights 스마트 검색" 이라는 연결 된 [작업 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 을 사용 하 여 생성 되며 경고가 발생 하면 추가 작업을 트리거하기 위해 확장 될 수 있습니다.

> [!NOTE]
> 이제이 경고 규칙에서 보낸 전자 메일 알림이 구독의 모니터링 판독기 및 모니터링 참가자 역할에 연결 된 사용자에 게 기본적으로 전송 됩니다. 이에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification)에서 제공 됩니다.
> 이 경고 규칙에서 보낸 알림은 [일반적인 경고 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema)를 따릅니다.
>

경고 페이지를 엽니다. 실패 이상 경고 규칙은 수동으로 설정 된 경고와 함께 포함 되며 현재 경고 상태 인지 여부를 확인할 수 있습니다.

[![](./media/proactive-failure-diagnostics/021.png "On the Application Insights resource page, click 'Alerts' tile, then 'Manage alert rules'")](./media/proactive-failure-diagnostics/021.png#lightbox)

경고를 클릭하여 구성합니다.

[![](./media/proactive-failure-diagnostics/032.png "Rule configuration screen")](./media/proactive-failure-diagnostics/032.png#lightbox)

오류 이상 경고 규칙을 사용 하지 않도록 설정 하거나 삭제할 수 있지만 동일한 Application Insights 리소스에서 다른 경고 규칙을 만들 수는 없습니다.

## <a name="example-of-failure-anomalies-alert-webhook-payload"></a>오류 비정상 경고 webhook 페이로드의 예

```json
{
    "properties": {
        "essentials": {
            "severity": "Sev3",
            "signalType": "Log",
            "alertState": "New",
            "monitorCondition": "Resolved",
            "monitorService": "Smart Detector",
            "targetResource": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule",
            "targetResourceName": "test-rule",
            "targetResourceGroup": "test-group",
            "targetResourceType": "microsoft.insights/components",
            "sourceCreatedId": "1a0a5b6436a9b2a13377f5c89a3477855276f8208982e0f167697a2b45fcbb3e",
            "alertRule": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.alertsmanagement/smartdetectoralertrules/failure anomalies - test-rule",
            "startDateTime": "2019-10-30T17:52:32.5802978Z",
            "lastModifiedDateTime": "2019-10-30T18:25:23.1072443Z",
            "monitorConditionResolvedDateTime": "2019-10-30T18:25:26.4440603Z",
            "lastModifiedUserName": "System",
            "actionStatus": {
                "isSuppressed": false
            },
            "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls."
        },
        "context": {
            "DetectionSummary": "An abnormal rise in failed request rate",
            "FormattedOccurenceTime": "2019-10-30T17:50:00Z",
            "DetectedFailureRate": "50.0% (200/400 requests)",
            "NormalFailureRate": "0.0% (over the last 30 minutes)",
            "FailureRateChart": [["2019-10-30T05:20:00Z",
            0],
            ["2019-10-30T05:40:00Z",
            100],
            ["2019-10-30T06:00:00Z",
            0],
            ["2019-10-30T06:20:00Z",
            0],
            ["2019-10-30T06:40:00Z",
            100],
            ["2019-10-30T07:00:00Z",
            0],
            ["2019-10-30T07:20:00Z",
            0],
            ["2019-10-30T07:40:00Z",
            100],
            ["2019-10-30T08:00:00Z",
            0],
            ["2019-10-30T08:20:00Z",
            0],
            ["2019-10-30T08:40:00Z",
            100],
            ["2019-10-30T17:00:00Z",
            0],
            ["2019-10-30T17:20:00Z",
            0],
            ["2019-10-30T09:00:00Z",
            0],
            ["2019-10-30T09:20:00Z",
            0],
            ["2019-10-30T09:40:00Z",
            100],
            ["2019-10-30T10:00:00Z",
            0],
            ["2019-10-30T10:20:00Z",
            0],
            ["2019-10-30T10:40:00Z",
            100],
            ["2019-10-30T11:00:00Z",
            0],
            ["2019-10-30T11:20:00Z",
            0],
            ["2019-10-30T11:40:00Z",
            100],
            ["2019-10-30T12:00:00Z",
            0],
            ["2019-10-30T12:20:00Z",
            0],
            ["2019-10-30T12:40:00Z",
            100],
            ["2019-10-30T13:00:00Z",
            0],
            ["2019-10-30T13:20:00Z",
            0],
            ["2019-10-30T13:40:00Z",
            100],
            ["2019-10-30T14:00:00Z",
            0],
            ["2019-10-30T14:20:00Z",
            0],
            ["2019-10-30T14:40:00Z",
            100],
            ["2019-10-30T15:00:00Z",
            0],
            ["2019-10-30T15:20:00Z",
            0],
            ["2019-10-30T15:40:00Z",
            100],
            ["2019-10-30T16:00:00Z",
            0],
            ["2019-10-30T16:20:00Z",
            0],
            ["2019-10-30T16:40:00Z",
            100],
            ["2019-10-30T17:30:00Z",
            50]],
            "ArmSystemEventsRequest": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourceGroups/test-group/providers/microsoft.insights/components/test-rule/query?query=%0d%0a++++++++++++++++systemEvents%0d%0a++++++++++++++++%7c+where+timestamp+%3e%3d+datetime(%272019-10-30T17%3a20%3a00.0000000Z%27)+%0d%0a++++++++++++++++%7c+where+itemType+%3d%3d+%27systemEvent%27+and+name+%3d%3d+%27ProactiveDetectionInsight%27+%0d%0a++++++++++++++++%7c+where+dimensions.InsightType+in+(%275%27%2c+%277%27)+%0d%0a++++++++++++++++%7c+where+dimensions.InsightDocumentId+%3d%3d+%27718fb0c3-425b-4185-be33-4311dfb4deeb%27+%0d%0a++++++++++++++++%7c+project+dimensions.InsightOneClassTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightExceptionCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightDependencyCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightRequestCorrelationTable%2c+%0d%0a++++++++++++++++++++++++++dimensions.InsightTraceCorrelationTable%0d%0a++++++++++++&api-version=2018-04-20",
            "LinksTable": [{
                "Link": "<a href=\"https://portal.azure.com/#blade/AppInsightsExtension/ProactiveDetectionFeedBlade/ComponentId/{\"SubscriptionId\":\"4f9b81be-fa32-4f96-aeb3-fc5c3f678df9\",\"ResourceGroup\":\"test-group\",\"Name\":\"test-rule\"}/SelectedItemGroup/718fb0c3-425b-4185-be33-4311dfb4deeb/SelectedItemTime/2019-10-30T17:50:00Z/InsightType/5\" target=\"_blank\">View full details in Application Insights</a>"
            }],
            "SmartDetectorId": "FailureAnomaliesDetector",
            "SmartDetectorName": "Failure Anomalies",
            "AnalysisTimestamp": "2019-10-30T17:52:32.5802978Z"
        },
        "egressConfig": {
            "displayConfig": [{
                "rootJsonNode": null,
                "sectionName": null,
                "displayControls": [{
                    "property": "DetectionSummary",
                    "displayName": "What was detected?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "FormattedOccurenceTime",
                    "displayName": "When did this occur?",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "DetectedFailureRate",
                    "displayName": "Detected failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "property": "NormalFailureRate",
                    "displayName": "Normal failure rate",
                    "type": "Text",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "chartType": "Line",
                    "xAxisType": "Date",
                    "yAxisType": "Percentage",
                    "xAxisName": "",
                    "yAxisName": "",
                    "property": "FailureRateChart",
                    "displayName": "Failure rate over last 12 hours",
                    "type": "Chart",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "defaultLoad": true,
                    "displayConfig": [{
                        "rootJsonNode": null,
                        "sectionName": null,
                        "displayControls": [{
                            "showHeader": false,
                            "columns": [{
                                "property": "Name",
                                "displayName": "Name"
                            },
                            {
                                "property": "Value",
                                "displayName": "Value"
                            }],
                            "property": "tables[0].rows[0][0]",
                            "displayName": "All of the failed requests had these characteristics:",
                            "type": "Table",
                            "isOptional": false,
                            "isPropertySerialized": true
                        }]
                    }],
                    "property": "ArmSystemEventsRequest",
                    "displayName": "",
                    "type": "ARMRequest",
                    "isOptional": false,
                    "isPropertySerialized": false
                },
                {
                    "showHeader": false,
                    "columns": [{
                        "property": "Link",
                        "displayName": "Link"
                    }],
                    "property": "LinksTable",
                    "displayName": "Links",
                    "type": "Table",
                    "isOptional": false,
                    "isPropertySerialized": false
                }]
            }]
        }
    },
    "id": "/subscriptions/4f9b81be-fa32-4f96-aeb3-fc5c3f678df9/resourcegroups/test-group/providers/microsoft.insights/components/test-rule/providers/Microsoft.AlertsManagement/alerts/7daf8739-ca8a-4562-b69a-ff28db4ba0a5",
    "type": "Microsoft.AlertsManagement/alerts",
    "name": "Failure Anomalies - test-rule"
}
```

## <a name="triage-and-diagnose-an-alert"></a>경고 심사 및 진단

경고는 실패한 요청 속도에서 비정상적인 증가가 감지되었음을 나타냅니다. 앱 또는 해당 환경에 문제가 있을 가능성이 있습니다.

자세히 조사 하려면 ' Application Insights에서 전체 세부 정보 보기 '를 클릭 합니다 .이 페이지의 링크를 클릭 하면 관련 요청, 예외, 종속성 또는 추적으로 필터링 된 [검색 페이지로](../../azure-monitor/app/diagnostic-search.md) 바로 이동 합니다. 

[Azure Portal](https://portal.azure.com)열고 앱에 대 한 Application Insights 리소스로 이동 하 여 오류 페이지를 열 수도 있습니다.

' 실패 진단 '을 클릭 하면 자세한 정보를 얻고 문제를 해결 하는 데 도움이 됩니다.

[![](./media/proactive-failure-diagnostics/051.png "Diagnostic search")](./media/proactive-failure-diagnostics/051.png#lightbox)

영향을 받는 요청의 백분율 및 사용자 수에서 문제가 얼마나 긴급한지 결정할 수 있습니다. 위의 예에서 78.5%의 실패율은 정상 요금 2.2%와 비교 하 여 잘못 된 부분이 있음을 나타냅니다. 반면에 46 사용자 에게만 영향을 줍니다. 앱 인 경우 얼마나 심각한 지 평가할 수 있습니다.

대부분의 경우에는 요청 이름, 예외, 종속성 오류 및 제공 된 추적 데이터에서 신속 하 게 문제를 진단할 수 있습니다.

이 예제에서는 요청 제한에 도달 하 여 SQL 데이터베이스에서 예외가 발생 했습니다.

[![](./media/proactive-failure-diagnostics/052.png "Failed request details")](./media/proactive-failure-diagnostics/052.png#lightbox)

## <a name="review-recent-alerts"></a>최근 경고 검토

Application Insights 리소스 페이지에서 **경고** 를 클릭 하 여 가장 최근에 발생 한 경고를 가져옵니다.

[![](./media/proactive-failure-diagnostics/070.png "Alerts summary")](./media/proactive-failure-diagnostics/070.png#lightbox)

## <a name="whats-the-difference-"></a>차이점은 무엇입니까...
실패에 대한 스마트 검색은 Application Insights의 다른 유사하지만 고유한 기능을 보완합니다.

* [메트릭 경고](../../azure-monitor/app/alerts.md) 는 사용자에 의해 설정되며 CPU 점유율, 요청 속도, 페이지 로드 시간 등과 같은 광범위한 메트릭을 모니터링할 수 있습니다. 예를 들어 더 많은 리소스를 추가해야 하는 경우 경고하는 데 사용할 수 있습니다. 이와 대조적으로 오류 이상에 대 한 스마트 감지는 웹 앱의 정상적인 동작에 비해 웹 앱의 실패 한 요청 비율이 늘어나면 거의 실시간으로 사용자에 게 알리도록 설계 된 작은 범위 (현재 실패 한 요청 률만)를 포함 합니다. 메트릭 경고와 달리 스마트 검색은 동작의 응답 변경에 대 한 임계값을 자동으로 설정 하 고 업데이트 합니다. 또한 스마트 감지는 진단 작업을 시작 하 여 문제를 해결 하는 시간을 절약 합니다.

* [성능 이상에 대한 스마트 감지](proactive-performance-diagnostics.md)는 컴퓨터 인텔리전스를 사용하여 메트릭에서 특수한 패턴을 검색하고 사용자에 의한 구성은 필요하지 않습니다. 하지만 실패에 대한 스마트 감지와 달리 성능 이상에 대한 스마트 감지의 목적은 예를 들어 특정 형식의 브라우저에 있는 특정 페이지에서 잘못 제공될 수 있는 사용 현황 다기관의 세그먼트를 찾는 것입니다. 분석은 매일 수행되고 결과가 있으면 경고보다 긴급하지 않을 수 있습니다. 이와 대조적으로 실패 이상에 대 한 분석은 들어오는 응용 프로그램 데이터에서 지속적으로 수행 되 고 서버 실패율이 예상 보다 높은 경우 몇 분 내에 알림이 표시 됩니다.

## <a name="if-you-receive-a-smart-detection-alert"></a>스마트 감지 경고를 받은 경우
*이 경고를 받은 이유는 무엇입니까?*

* 이전 기간의 일반적인 기준선에 비해 실패한 요청 속도가 비정상적으로 증가했음을 감지했습니다. 오류 및 관련 응용 프로그램 데이터를 분석 한 후에는 확인 해야 하는 문제가 있다고 생각 합니다.

*알림은 분명히 문제가 있음을 의미합니까?*

* 앱 또는 사용자에 대한 의미 체계와 영향을 완전히 이해할 수 있는데도 앱 중단 또는 성능 저하에 대한 경고를 시도합니다.

*응용 프로그램 데이터를 살펴보겠습니다.*

* 아닙니다. 서비스는 완전 자동입니다. 사용자는 알림만 받게 됩니다. 사용자의 데이터는 [프라이빗](../../azure-monitor/app/data-retention-privacy.md)입니다.

*이 경고를 구독해야 하나요?*

* 아닙니다. 요청 데이터를 보내는 모든 응용 프로그램에는 스마트 검색 경고 규칙이 있습니다.

*구독을 취소하거나 동료에게 대신 보낸 알림을 가져올 수 있습니까?*

* 예, 경고 규칙에서 이를 구성하려면 스마트 감지 규칙을 클릭합니다. 경고를 사용하지 않도록 설정하거나 경고에 대한 받는 사람을 변경할 수 있습니다.

*이메일이 삭제되었습니다. 포털의 어디에서 알림을 찾을 수 있습니까?*

* Azure 활동 로그에서 찾을 수 있습니다. Azure에서 앱에 대한 Application Insights 리소스를 연 다음, 활동 로그를 선택합니다.

*경고의 일부가 알려진 문제이고 수신하고 싶지 않습니다.*

* [경고 동작 규칙](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-action-rules) 억제 기능을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이러한 진단 도구는 앱에서 데이터를 검사 하는 데 도움이 됩니다.

* [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)
* [검색 탐색기](../../azure-monitor/app/diagnostic-search.md)
* [분석 - 강력한 쿼리 언어](../../azure-monitor/log-query/get-started-portal.md)

스마트 감지는 자동입니다. 하지만 보다 많은 경고를 설정하고 싶을 수 있습니다.

* [수동으로 구성된 메트릭 경고](../../azure-monitor/app/alerts.md)
* [가용성 웹 테스트](../../azure-monitor/app/monitor-web-app-availability.md)
