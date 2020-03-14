---
title: Azure Monitor에서 로그 경고 문제 해결 | Microsoft Docs
description: Azure의 로그 경고 규칙에 대 한 일반적인 문제, 오류 및 해결 방법입니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: acb9784b745fa90fc9cd264162930020e6d64751
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249036"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고 문제 해결  

이 문서에서는 Azure Monitor에서 로그 경고를 설정할 때 발생할 수 있는 일반적인 문제를 해결 하는 방법을 보여 줍니다. 또한 로그 경고의 기능 또는 구성과 관련 된 일반적인 문제에 대 한 해결 방법을 제공 합니다.

용어 *로그 경고* 는 [Azure Log Analytics 작업 영역](../learn/tutorial-viewdata.md) 또는 [Azure 애플리케이션 Insights](../../azure-monitor/app/analytics.md)에서 로그 쿼리를 기반으로 발생 하는 규칙을 설명 합니다. [Azure Monitor에서 로그 경고](../platform/alerts-unified-log.md)의 기능, 용어 및 형식에 대해 자세히 알아보세요.

> [!NOTE]
> 이 문서에서는 Azure Portal 경고 규칙을 표시 하 고 연결 된 작업 그룹에서 알림을 수행 하지 않는 경우를 고려 하지 않습니다. 이러한 경우 [Azure Portal에서 작업 그룹 만들기 및 관리](../platform/action-groups.md)의 세부 정보를 참조 하세요.

## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

[Azure Monitor에서 구성 된 로그 경고 규칙](../platform/alerts-log.md) 의 상태가 [예상 대로 *실행 되는* 것으로](../platform/alerts-managing-alert-states.md)표시 되지 않는 몇 가지 일반적인 이유는 다음과 같습니다.

### <a name="data-ingestion-time-for-logs"></a>로그에 대 한 데이터 수집 시간

로그 경고는 [Log Analytics](../learn/tutorial-viewdata.md) 또는 [Application Insights](../../azure-monitor/app/analytics.md)를 기반으로 쿼리를 주기적으로 실행 합니다. Azure Monitor은 전 세계의 다양 한 소스에서 수천 대의 데이터를 처리 하기 때문에 서비스는 다양 한 시간 지연에 영향을 받습니다. 자세한 내용은 [Azure Monitor 로그의 데이터 수집 시간](../platform/data-ingestion-time.md)을 참조 하세요.

지연을 완화 하기 위해 시스템은 필요한 데이터를 아직 수집 하지 않는 경우 경고 쿼리를 여러 번 대기한 후 다시 시도 합니다. 시스템에는 기하급수적으로 증가하는 대기 시간이 설정되어 있습니다. 로그 경고는 데이터를 사용할 수 있는 경우에만 트리거되고 로그 데이터 수집 속도가 느려지는 원인일 수 있습니다.

### <a name="incorrect-time-period-configured"></a>잘못된 기간이 구성됨

[로그 경고에 대 한 용어](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)에 대 한 문서에 설명 된 대로 구성에 설명 된 기간에는 쿼리에 대 한 시간 범위가 지정 됩니다. 이 쿼리는이 범위 내에서 생성 된 레코드만 반환 합니다.

이 기간은 로그 쿼리에 대해 인출 되는 데이터를 제한 하 여 악용을 방지 하 고 로그 쿼리에 사용 되는 시간 명령 (예: **전**)을 우회 합니다. 예를 들어 기간이 60분으로 설정되고 오후 1시 15분에 쿼리를 실행하는 경우 오후 12시 15분부터 오후 1시 15분 사이에 생성된 레코드만 로그 쿼리에 사용됩니다. 로그 쿼리가 이전 **(1d)** 와 같은 시간 명령을 사용 하는 경우 해당 기간으로 설정 되어 있기 때문에 쿼리는 여전히 12:15 pm 및 1:15 pm 사이의 데이터만 사용 합니다.

구성의 기간이 쿼리와 일치 하는지 확인 합니다. 앞에서 설명한 예의 경우 로그 쿼리에서 녹색 표식을 사용 하 여 **(1d)** 를 사용 하는 경우 기간은 24 시간 또는 1440 분 (빨간색으로 표시)으로 설정 되어야 합니다. 이 설정은 쿼리가 의도 한 대로 실행 되도록 합니다.

![기간](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>경고 표시 안 함 옵션이 설정됨

[Azure Portal에서 로그 경고 규칙을 만드는](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)방법에 대 한 문서 8 단계에서 설명한 대로 로그 경고는 구성 된 시간 동안 트리거 및 알림 작업을 표시 하지 않도록 **경고 표시 안** 함 옵션을 제공 합니다. 따라서 경고가 발생 하지 않은 것으로 생각할 수 있습니다. 실제로는 발생 했지만 억제 되었습니다.  

![알림 표시 안 함](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>메트릭 측정 경고 규칙이 잘못됨

*메트릭 측정 로그 경고* 는 특별 한 기능 및 제한 된 경고 쿼리 구문을 포함 하는 로그 경고의 하위 형식입니다. 메트릭 측정 로그 경고에 대 한 규칙을 사용 하려면 쿼리 출력이 메트릭 시계열 이어야 합니다. 즉, 출력은 해당 집계 된 값과 함께 크기가 동일 하 고 크기가 같은 고유 테이블입니다.

**AggregatedValue**와 함께 테이블에 추가 변수를 포함 하도록 선택할 수 있습니다. 이러한 변수를 사용 하 여 테이블을 정렬할 수 있습니다.

예를 들어 메트릭 측정 로그 경고에 대 한 규칙이 다음과 같이 구성 되어 있다고 가정 합니다.

- `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)` 쿼리  
- 6 시간의 기간
- 50의 임계값
- 세 개의 연속 위반에 대 한 경고 논리
- **$Table** 으로 선택한 **집계**

명령에는 **요약 ... 및은** 두 변수 (**timestamp** 및 **$table**)를 제공 합니다. 시스템은 **집계**를 위해 **$table** 를 선택 합니다. 다음 스크린샷에 표시 된 것 처럼 시스템은 **$table** 필드를 기준으로 결과 테이블을 정렬 합니다. 그런 다음, 각 테이블 형식 (예: **availabilityResults**)에 대 한 여러 **AggregatedValue** 인스턴스를 조사 하 여 세 개 이상의 연속 위반이 있는지 확인 합니다.

![다중 값을 사용 하 여 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMQuery.png)

**집계 시** 가 **$table**에 정의 되어 있기 때문에 데이터는 **$table** 열 (빨간색으로 표시 됨)에 따라 정렬 됩니다. 그런 다음 **집계** 필드의 형식을 그룹화 하 고 찾습니다.

예를 들어 **$table**의 경우 **availabilityResults** 의 값은 하나의 플롯/엔터티로 간주 됩니다 (주황색으로 표시 됨). 이 값 플롯/엔터티에서 경고 서비스는 세 개의 연속 위반 (녹색으로 표시 됨)을 확인 합니다. 위반은 테이블 값 **availabilityResults**에 대 한 경고를 트리거합니다.

마찬가지로 **$table**의 다른 값에 대해 세 가지 연속 위반이 발생 하는 경우 동일한 작업에 대해 다른 경고 알림이 트리거됩니다. 경고 서비스는 시간에 따라 하나의 플롯/엔터티 (주황색으로 표시 됨)로 값을 자동으로 정렬 합니다.

이제 메트릭 측정 로그 경고에 대 한 규칙이 수정 되었고 쿼리가 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`되어 있다고 가정 합니다. 나머지 구성은 세 가지 연속 된 위반에 대 한 경고 논리를 비롯 하 여 이전과 동일 하 게 유지 됩니다. 이 경우의 **집계** 옵션은 기본적으로 **타임 스탬프** 입니다. 요약 ...에는 값이 하나만 쿼리에 제공 됩니다.  **by** ( **타임 스탬프**)를 기준으로 합니다. 이전 예제와 마찬가지로 실행 종료 시의 출력은 다음과 같이 표시 됩니다.

   ![단일 값을 사용 하 여 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMtimestamp.png)

**집계가** **타임 스탬프**에 대해 정의 되므로 **타임 스탬프** 열 (빨간색으로 표시 됨)으로 데이터가 정렬 됩니다. 그런 다음 **timestamp**를 기준으로 그룹화 합니다. 예를 들어 `2018-10-17T06:00:00Z`의 값은 하나의 플롯/엔터티로 간주 됩니다 (주황색으로 표시 됨). 이 값 플롯/엔터티에는 경고 서비스에서 연속 위반을 찾을 수 없습니다 (각 **타임 스탬프** 값에 항목이 하나만 있으므로). 따라서 경고가 트리거되지 않습니다. 이 경우 사용자는 다음 중 하나를 수행 해야 합니다.

- 더미 변수나 기존 변수 (예: **$table**)를 추가 하 여 **집계** 필드를 사용 하 여 올바르게 정렬 합니다.
- 대신 **총 위반** 을 기준으로 경고 논리를 사용 하도록 경고 규칙을 다시 구성 합니다.

## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함

[Azure Monitor에서 구성 된 로그 경고 규칙](../platform/alerts-log.md) 은 [Azure Alerts](../platform/alerts-managing-alert-states.md)에서 볼 때 예기치 않게 트리거될 수 있습니다. 다음 섹션에서는 몇 가지 일반적인 이유를 설명 합니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨

Log Analytics 및 Application Insights 수집 지연과 처리가 적용 됩니다. 로그 경고 쿼리를 실행할 때 데이터를 사용할 수 없거나 일부 데이터만 사용할 수 있는 것을 확인할 수 있습니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](../platform/data-ingestion-time.md)을 참조 하세요.

경고 규칙을 구성 하는 방법에 따라 경고 실행 시 로그에 데이터 또는 데이터 일부가 없으면 잘못 실행 될 수 있습니다. 이 경우 경고 쿼리 또는 구성을 변경 하는 것이 좋습니다.

예를 들어 분석 쿼리의 결과 수가 5 미만일 때 트리거되는 로그 경고 규칙을 구성 하는 경우 데이터 (제로 레코드) 또는 일부 결과 (레코드 1 개)가 없는 경우 경고가 트리거됩니다. 그러나 데이터 수집 지연 후에 전체 데이터가 있는 동일한 쿼리는 10 개의 레코드로 결과를 제공할 수 있습니다.

### <a name="alert-query-output-is-misunderstood"></a>경고 쿼리 출력이 잘못 되었습니다.

분석 쿼리에서 로그 경고에 대한 논리를 제공합니다. 분석 쿼리는 다양 한 빅 데이터 및 수치 연산 함수를 사용할 수 있습니다. 경고 서비스는 지정 된 기간 동안 데이터가 지정 된 간격에 따라 쿼리를 실행 합니다. 경고 서비스는 경고 유형을 기반으로 쿼리를 미묘한 변경 합니다. **신호 논리 구성** 화면의 **실행할 쿼리** 섹션에서이 변경 내용을 볼 수 있습니다.

![실행할 쿼리](media/alert-log-troubleshoot/LogAlertPreview.png)

**실행할 쿼리** 상자는 로그 경고 서비스를 실행 하는 것입니다. 경고를 만들기 전에 경고 쿼리 출력이 무엇 인지 파악 하려는 경우 [분석 포털](../log-query/portals.md) 또는 [분석 API](https://docs.microsoft.com/rest/api/loganalytics/)를 통해 명시 된 쿼리 및 timespan을 실행할 수 있습니다.

## <a name="log-alert-was-disabled"></a>로그 경고를 사용 하지 않도록 설정 했습니다.

다음 섹션에서는 Azure Monitor [로그 경고 규칙](../platform/alerts-log.md)을 사용 하지 않도록 설정할 수 있는 몇 가지 이유를 나열 합니다.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>경고가 생성 된 리소스가 더 이상 존재 하지 않습니다.

Azure Monitor에서 만든 로그 경고 규칙은 Azure Log Analytics 작업 영역, Azure 애플리케이션 Insights 앱 및 Azure 리소스와 같은 특정 리소스를 대상으로 합니다. 그러면 로그 경고 서비스는 지정 된 대상에 대 한 규칙에 제공 된 분석 쿼리를 실행 합니다. 하지만 규칙을 만든 후에는 사용자가 Azure에서 삭제 하거나 Azure 내부에서 이동-로그 경고 규칙의 대상으로 이동 하는 경우가 많습니다. 경고 규칙의 대상이 더 이상 유효 하지 않기 때문에 규칙 실행이 실패 합니다.

이러한 경우, Azure Monitor는 로그 경고를 사용 하지 않도록 설정 하 고, 규칙을 계속 해 서 (예: 주간)에 지속적으로 실행할 수 없는 경우이를 불필요 하 게 청구 하지 않도록 합니다. Azure Monitor [Azure 활동 로그](../../azure-resource-manager/management/view-activity-logs.md)를 통해 로그 경고를 사용 하지 않도록 설정 하는 정확한 시간을 확인할 수 있습니다. Azure 활동 로그에서 Azure Monitor 로그 경고 규칙을 사용 하지 않도록 설정 하면 이벤트가 추가 됩니다.

Azure 활동 로그의 다음 샘플 이벤트는 지속적으로 실패 하 여 비활성화 된 경고 규칙에 대 한 것입니다.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>로그 경고에 사용 된 쿼리가 잘못 되었습니다.

구성의 일부로 Azure Monitor에서 생성 되는 각 로그 경고 규칙은 경고 서비스가 정기적으로 실행할 분석 쿼리를 지정 해야 합니다. 분석 쿼리는 규칙을 만들거나 업데이트할 때 올바른 구문이 될 수 있습니다. 그러나 일정 기간 동안 로그 경고 규칙에 제공 된 쿼리가 구문 문제를 개발 하 고 규칙 실행이 실패할 수 있습니다. 로그 경고 규칙에 제공 된 분석 쿼리가 오류를 개발할 수 있는 몇 가지 일반적인 이유는 다음과 같습니다.

- 쿼리는 [여러 리소스에서 실행](../log-query/cross-workspace-query.md)되도록 작성 됩니다. 하나 이상의 지정 된 리소스가 더 이상 존재 하지 않습니다.
- 구성 된 [메트릭 측정 유형 로그 경고](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) 에서 경고 쿼리가 표준 구문을 준수 하지 않습니다.
- 분석 플랫폼에 대 한 데이터 흐름이 없습니다. 제공 된 쿼리에 대 한 데이터가 없기 때문에 [쿼리 실행 시 오류가](https://dev.loganalytics.io/documentation/Using-the-API/Errors) 발생 합니다.
- [쿼리 언어](https://docs.microsoft.com/azure/kusto/query/) 의 변경 내용에는 명령 및 함수에 대 한 수정 된 형식이 포함 됩니다. 따라서 경고 규칙에서 이전에 제공 된 쿼리가 더 이상 유효 하지 않습니다.

이 동작에 대 한 경고를 [Azure Advisor](../../advisor/advisor-overview.md) 합니다. Azure Advisor에 대 한 특정 로그 경고 규칙에 대 한 권장 사항은 중간에 영향을 주는 고가용성의 범주 아래에 추가 되 고 "모니터링을 보장 하기 위해 로그 경고 규칙 복구"에 대 한 설명이 추가 됩니다. Azure Advisor에서 7 일에 대 한 권장 사항을 제공한 후 로그 경고 규칙의 경고 쿼리가 수정 되지 않는 경우 Azure Monitor는 로그 경고를 사용 하지 않도록 설정 하 고, 규칙을 계속 해 서 사용할 수 없을 때 ( 주).

[Azure 활동 로그](../../azure-resource-manager/management/view-activity-logs.md)에서 이벤트를 검색 하 여 Azure Monitor 로그 경고 규칙을 사용 하지 않도록 설정 하는 정확한 시간을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 로그 경고](../platform/alerts-unified-log.md)에 대해 알아봅니다.
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
- [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아보세요.
