---
title: Azure 모니터에서 로그 경고 문제 해결 | 마이크로 소프트 문서
description: Azure의 로그 경고 규칙에 대한 일반적인 문제, 오류 및 해결 방법.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249036"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure 모니터에서 로그 경고 문제 해결  

이 문서에서는 Azure Monitor에서 로그 경고를 설정할 때 발생할 수 있는 일반적인 문제를 해결하는 방법을 보여 주며 있습니다. 또한 로그 경고의 기능 이나 구성에 대 한 일반적인 문제에 대 한 솔루션을 제공 합니다.

*로그 경고라는* 용어는 [Azure Log Analytics 작업 영역또는 Azure](../learn/tutorial-viewdata.md) 응용 프로그램 [인사이트에서](../../azure-monitor/app/analytics.md)로그 쿼리를 기반으로 작동하는 규칙을 설명합니다. [Azure 모니터의 로그 경고에서](../platform/alerts-unified-log.md)기능, 용어 및 유형에 대해 자세히 알아봅니다.

> [!NOTE]
> 이 문서에서는 Azure Portal에서 경고 규칙이 트리거되고 연결된 작업 그룹에서 알림이 수행되지 않는 경우를 고려하지 않습니다. 이러한 경우 [Azure 포털의 작업 그룹 만들기 및 관리의](../platform/action-groups.md)세부 정보를 참조하십시오.

## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

다음은 Azure Monitor에서 구성된 로그 경고 규칙에 대한 상태가 [예상시 *로고로 표시되지* ](../platform/alerts-managing-alert-states.md)않는 몇 가지 일반적인 이유입니다. [log alert rule in Azure Monitor](../platform/alerts-log.md)

### <a name="data-ingestion-time-for-logs"></a>로그에 대한 데이터 수집 시간

로그 경고는 [로그 분석](../learn/tutorial-viewdata.md) 또는 [응용 프로그램 인사이트를](../../azure-monitor/app/analytics.md)기반으로 주기적으로 쿼리를 실행합니다. Azure Monitor는 전 세계 다양한 소스에서 수천 명의 고객으로부터 많은 테라바이트 의 데이터를 처리하므로 서비스는 다양한 시간 지연에 취약합니다. 자세한 내용은 [Azure Monitor 로그의 데이터 수집 시간을 참조하세요.](../platform/data-ingestion-time.md)

지연을 완화하기 위해 시스템은 필요한 데이터가 아직 수집되지 않은 경우 경고 쿼리를 여러 번 기다렸다가 다시 시도합니다. 시스템에는 기하급수적으로 증가하는 대기 시간이 설정되어 있습니다. 로그 경고는 데이터를 사용할 수 있는 후에만 트리거되므로 로그 데이터의 느린 사용으로 인해 지연될 수 있습니다.

### <a name="incorrect-time-period-configured"></a>잘못된 기간이 구성됨

[로그 경고에 대한 용어에 대한](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)문서에서 설명한 대로 구성에 명시된 기간은 쿼리의 시간 범위를 지정합니다. 쿼리는 이 범위 내에서 만들어진 레코드만 반환합니다.

기간은 악용을 방지하기 위해 로그 쿼리에 대해 가져온 데이터를 제한하며 로그 쿼리에 사용된 모든 시간 명령(예: **이전)을**우회합니다. 예를 들어 기간이 60분으로 설정되고 오후 1시 15분에 쿼리를 실행하는 경우 오후 12시 15분부터 오후 1시 15분 사이에 생성된 레코드만 로그 쿼리에 사용됩니다. 로그 쿼리가 **이전(1d)과**같은 시간 명령을 사용하는 경우 기간은 해당 간격으로 설정되어 있으므로 쿼리는 여전히 오후 12시 15분에서 오후 1시 15분 사이의 데이터만 사용합니다.

구성의 기간이 쿼리와 일치하는지 확인합니다. 앞에서 보여 준 예제의 경우 로그 쿼리가 녹색 마커와 함께 **이전(1d)을** 사용하는 경우 기간을 24시간 또는 1,440분(빨간색으로 표시)으로 설정해야 합니다. 이 설정을 사용하면 쿼리가 의도한 대로 실행됩니다.

![기간](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>경고 표시 안 함 옵션이 설정됨

[Azure Portal에서 로그 경고 규칙을 만드는](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)문서의 8단계에서 설명한 대로 로그 경고는 구성된 시간 동안 트리거링 및 알림 작업을 억제하는 경고 **억제** 옵션을 제공합니다. 따라서 경고가 발생하지 않았다고 생각할 수 있습니다. 사실, 그것은 불을 했지만 억압 되었다.  

![알림 표시 안 함](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>메트릭 측정 경고 규칙이 잘못됨

*메트릭 측정 로그 경고는* 특수 기능 및 제한된 경고 쿼리 구문이 있는 로그 경고의 하위 유형입니다. 메트릭 측정 로그 경고에 대한 규칙은 쿼리 출력을 메트릭 시간계여야 합니다. 즉, 출력은 해당 집계 된 값과 함께 고유하고 동등한 크기의 기간기간이 있는 테이블입니다.

**AggregatedValue**와 함께 테이블에 추가 변수를 갖도록 선택할 수 있습니다. 이러한 변수는 테이블을 정렬하는 데 사용할 수 있습니다.

예를 들어 메트릭 측정 로그 경고에 대한 규칙이 다음과 같이 구성되었다고 가정합니다.

- 의 쿼리`search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6시간의 기간
- 임계값 50
- 3회 연속 위반에 대한 경고 논리
- **$table** **선택시 집계**

명령에 요약이 포함되어 있기 때문에 **... 에** 의해 두 개의 변수를 제공합니다 **(타임 스탬프와** **$table),** 시스템은 집계에 대한 **$table** **선택합니다.** 다음 스크린샷과 같이 **$table** 필드별로 결과 테이블을 정렬합니다. 그런 다음 각 테이블 유형에 대한 여러 **AggregatedValue** 인스턴스(예: **가용성Results)를**확인하여 3개 이상의 연속 위반이 있는지 확인합니다.

![여러 값을 가진 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMQuery.png)

**집계 시온은** **$table**정의되므로 데이터는 **$table** 열(빨간색으로 표시)에 정렬됩니다. 그런 다음 그룹화하고 **집계 시** 필드의 유형을 찾습니다.

예를 들어 **$table**가용성에 대한 **값결과는** 하나의 플롯/엔터티(주황색으로 표시)로 간주됩니다. 이 값 플롯/엔터티에서 경고 서비스는 세 번의 연속 위반(녹색으로 표시)을 확인합니다. 위반은 테이블 값 가용성에 대한 경고를 **트리거합니다Results**.

마찬가지로 **$table**다른 값에 대해 세 번연속 위반이 발생하는 경우 동일한 일에 대해 다른 경고 알림이 트리거됩니다. 경고 서비스는 시간별로 하나의 플롯/엔터티(주황색으로 표시)의 값을 자동으로 정렬합니다.

이제 메트릭 측정 로그 경고에 대한 규칙이 수정되었으며 쿼리가 .라고 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`가정합니다. 세 번의 연속 위반에 대한 경고 논리를 포함하여 나머지 구성은 이전과 동일하게 유지되었습니다. 이 경우 **집계 시** 옵션은 기본적으로 **타임스탬프입니다.** 요약을 위해 쿼리에 하나의 값만 **제공됩니다... (즉,** **타임스탬프).** 이전 예제와 마찬가지로 실행 끝의 출력은 다음과 같습니다.

   ![단수 값을 가진 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMtimestamp.png)

**집계 시한은** **타임스탬프에**정의되므로 데이터는 **타임스탬프** 열(빨간색으로 표시)에서 정렬됩니다. 그런 다음 **타임스탬프별로**그룹화합니다. 예를 들어, `2018-10-17T06:00:00Z` 값은 하나의 플롯/엔터티(주황색으로 표시)로 간주됩니다. 이 값 플롯/엔터티에서 경고 서비스는 연속적인 위반을 찾을 수 없습니다(각 **타임스탬프** 값에는 항목이 하나만 있기 때문). 따라서 경고가 트리거되지 않습니다. 이 경우 사용자는 다음 중 하나를 수행해야 합니다.

- 더미 변수 또는 기존 변수(예: **$table)를**추가하여 집계 시 필드를 사용하여 올바르게 **정렬합니다.**
- 대신 **총 위반에** 따라 경고 논리를 사용하도록 경고 규칙을 다시 구성합니다.

## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함

Azure Alert에서 볼 때 Azure Monitor에서 구성된 [로그 경고 규칙이](../platform/alerts-log.md) 예기치 않게 트리거될 수 [있습니다.](../platform/alerts-managing-alert-states.md) 다음 섹션에서는 몇 가지 일반적인 이유를 설명합니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨

로그 분석 및 애플리케이션 인사이트는 수집 지연 및 처리의 영향을 받습니다. 로그 경고 쿼리를 실행할 때 사용할 수 있는 데이터가 없거나 일부 데이터만 사용할 수 있는 것으로 나타났습니다. 자세한 내용은 [Azure 모니터의 로그 데이터 수집 시간을](../platform/data-ingestion-time.md)참조하십시오.

경고 규칙을 구성한 방법에 따라 경고 실행 시 로그에 데이터 또는 부분 데이터가 없는 경우 잘못 발생할 수 있습니다. 이러한 경우 경고 쿼리 또는 구성을 변경하는 것이 좋습니다.

예를 들어 분석 쿼리의 결과 수가 5개 미만일 때 트리거되는 로그 경고 규칙을 구성하면 데이터(레코드 없음) 또는 부분 결과(하나의 레코드)가 없을 때 경고가 트리거됩니다. 그러나 데이터 수집이 지연된 후 전체 데이터가 있는 동일한 쿼리가 10개의 레코드의 결과를 제공할 수 있습니다.

### <a name="alert-query-output-is-misunderstood"></a>경고 쿼리 출력이 잘못 이해됨

분석 쿼리에서 로그 경고에 대한 논리를 제공합니다. 분석 쿼리는 다양한 빅 데이터 및 수학 함수를 사용할 수 있습니다. 경고 서비스는 지정된 기간 동안 데이터로 지정된 간격으로 쿼리를 실행합니다. 경고 서비스는 경고 유형에 따라 쿼리를 미묘하게 변경합니다. **신호 논리 구성** 화면에서 **실행될 쿼리** 섹션에서 이 변경 사항을 볼 수 있습니다.

![실행될 쿼리](media/alert-log-troubleshoot/LogAlertPreview.png)

**실행할 쿼리** 상자는 로그 경고 서비스가 실행되는 내용입니다. 경고를 만들기 전에 경고 쿼리 출력이 무엇인지 이해하려면 [애널리틱스 포털](../log-query/portals.md) 또는 [애널리틱스 API를](https://docs.microsoft.com/rest/api/loganalytics/)통해 명시된 쿼리 및 기간을 실행할 수 있습니다.

## <a name="log-alert-was-disabled"></a>로그 경고가 비활성화되었습니다.

다음 섹션에는 Azure Monitor에서 [로그 경고 규칙을](../platform/alerts-log.md)사용하지 않도록 설정하는 몇 가지 이유가 나열됩니다.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>경고가 생성된 리소스가 더 이상 존재하지 않습니다.

Azure Monitor에서 만든 로그 경고 규칙은 Azure Log Analytics 작업 영역, Azure 응용 프로그램 인사이트 앱 및 Azure 리소스와 같은 특정 리소스를 대상으로 합니다. 그러면 로그 경고 서비스가 지정된 대상에 대한 규칙에 제공된 분석 쿼리를 실행합니다. 그러나 규칙을 만든 후 사용자는 종종 Azure에서 삭제하거나 로그 경고 규칙의 대상인 Azure 내부로 이동합니다. 경고 규칙의 대상이 더 이상 유효하지 않으므로 규칙 실행이 실패합니다.

이러한 경우 Azure Monitor는 로그 경고를 사용하지 않도록 설정하고 규칙이 상당한 기간(예: 주)동안 계속 실행되지 않을 때 불필요하게 청구되지 않도록 합니다. Azure 모니터가 [Azure 활동 로그를](../../azure-resource-manager/management/view-activity-logs.md)통해 로그 경고를 사용하지 않도록 설정한 정확한 시간을 확인할 수 있습니다. Azure 활동 로그에서 Azure Monitor가 로그 경고 규칙을 사용하지 않도록 설정하면 이벤트가 추가됩니다.

Azure 활동 로그에서 다음 샘플 이벤트는 지속적인 오류로 인해 비활성화된 경고 규칙에 대한 것입니다.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>로그 경고에 사용된 쿼리가 잘못되었습니다.

구성의 일부로 Azure Monitor에서 만든 각 로그 경고 규칙은 경고 서비스가 주기적으로 실행되는 분석 쿼리를 지정해야 합니다. 분석 쿼리는 규칙 생성 또는 업데이트 시 올바른 구문을 가질 수 있습니다. 그러나 때로는 시간이 지남에 따라 로그 경고 규칙에 제공된 쿼리가 구문 문제를 발생하여 규칙 실행이 실패할 수 있습니다. 로그 경고 규칙에 제공된 분석 쿼리가 오류를 개발할 수 있는 몇 가지 일반적인 이유는 다음과 같습니다.

- 쿼리는 여러 [리소스에서 실행되도록](../log-query/cross-workspace-query.md)작성됩니다. 또한 지정된 리소스 중 하나 이상이 더 이상 존재하지 않습니다.
- 구성된 [메트릭 측정 유형 로그 경고에는](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) 구문 규범을 준수하지 않는 경고 쿼리가 있습니다.
- 분석 플랫폼으로의 데이터 흐름이 없습니다. [제공된 쿼리에](https://dev.loganalytics.io/documentation/Using-the-API/Errors) 대한 데이터가 없기 때문에 쿼리 실행에 오류가 발생합니다.
- 쿼리 [언어의](https://docs.microsoft.com/azure/kusto/query/) 변경 사항에는 명령 및 함수에 대한 수정된 형식이 포함됩니다. 따라서 경고 규칙의 앞에 제공된 쿼리는 더 이상 유효하지 않습니다.

[Azure Advisor는](../../advisor/advisor-overview.md) 이 동작에 대해 경고합니다. Azure Advisor의 특정 로그 경고 규칙에 대한 권장 사항이 중간 영향이 있는 고가용성 범주 및 "모니터링을 보장하기 위해 로그 경고 규칙 복구"에 대한 설명아래에 추가됩니다. Azure Advisor에서 7일 동안 권장 사항을 제공한 후 로그 경고 규칙의 경고 쿼리가 수정되지 않으면 Azure Monitor는 로그 경고를 사용하지 않도록 설정하고 상당한 기간 동안 규칙을 계속 실행할 수 없을 때 불필요하게 청구되지 않도록 합니다. 1주일)

Azure [작업 로그](../../azure-resource-manager/management/view-activity-logs.md)에서 이벤트를 검색하여 Azure Monitor에서 로그 경고 규칙을 사용하지 않도록 설정한 정확한 시간을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 로그 경고](../platform/alerts-unified-log.md)에 대해 알아봅니다.
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
- [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아봅니다.
