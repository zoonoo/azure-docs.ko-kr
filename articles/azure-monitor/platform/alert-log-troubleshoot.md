---
title: Azure Monitor의 로그 경고 문제 해결 | Microsoft Docs
description: 일반적인 문제, 오류 및 Azure에서 로그 경고 규칙에 대 한 해결 방법입니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 03a6ea45577b4a4bf57501b1834f91438feb4e2b
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66477867"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고 문제 해결  

이 문서에서는 Azure Monitor의 로그 경고를 설정 하는 경우 발생할 수 있는 일반적인 문제를 해결 하는 방법을 보여 줍니다. 또한 기능 또는 로그 경고의 구성을 사용 하 여 일반적인 문제에 대 한 솔루션을 제공합니다. 

용어 *로그 경고* 화재에서 로그 쿼리를 기반으로 하는 규칙을 설명는 [Azure Log Analytics 작업 영역](../learn/tutorial-viewdata.md) 나 [Azure Application Insights](../../azure-monitor/app/analytics.md)합니다. 에 대 한 기능, 용어 및 자세한 형식에서 [Azure Monitor의 로그 경고](../platform/alerts-unified-log.md)합니다.

> [!NOTE]
> 이 문서에서는 Azure portal 트리거되는 경고 규칙을 보여 줍니다. 알림을 연결 된 작업 그룹에서 수행 되지 않는 경우를 고려 하지 않습니다. 이러한 경우에 대 한 세부 정보를 보려면 [만들기 및 Azure portal에서 작업 그룹 관리](../platform/action-groups.md)합니다.

## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

이유 몇 가지 일반적인 원인은 다음과 같습니다 구성 된 상태로 [Azure Monitor의 로그 경고 규칙](../platform/alerts-log.md) 표시 되지 않습니다 [으로 *발생* 예상 하는 경우](../platform/alerts-managing-alert-states.md)합니다. 

### <a name="data-ingestion-time-for-logs"></a>로그에 대 한 데이터 수집 시간

로그 경고를 기반으로 쿼리를 정기적으로 실행 [Log Analytics](../learn/tutorial-viewdata.md) 하거나 [Application Insights](../../azure-monitor/app/analytics.md)합니다. 전 세계에서 많은 테라바이트의에서 수천 명의 고객이 다양 한 원본의 데이터를 처리 하는 Azure Monitor 서비스 이므로 다양 한 지연 시간에 취약 합니다. 자세한 내용은 [Azure Monitor 로그에서 데이터 수집 시간](../platform/data-ingestion-time.md)합니다.

지연을 완화 하려면 시스템 기다린 후 다시 시도 경고 쿼리가 여러 번 찾으면, 필요한 데이터는 아직 수집 되지 않습니다. 시스템에는 기하급수적으로 증가하는 대기 시간이 설정되어 있습니다. 느린 로그 데이터 수집으로 인해 지연 될 수 있도록 데이터를 사용할 수 후에 로그 경고가 트리거될 수 있습니다. 

### <a name="incorrect-time-period-configured"></a>잘못된 기간이 구성됨

문서에 설명 된 대로 [로그 경고에 대 한 용어](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), 구성에 명시 된 기간 동안 쿼리에 대 한 시간 범위를 지정 합니다. 쿼리는이 범위 내에서 생성 된 레코드만 반환 합니다. 

기간 데이터 남용을 방지 하기 위해 로그 쿼리의 페치를 제한 하 고 모든 시간 명령을 우회 하기 (같은 **전**) 로그 쿼리를 사용 합니다. 예를 들어 기간이 60분으로 설정되고 오후 1시 15분에 쿼리를 실행하는 경우 오후 12시 15분부터 오후 1시 15분 사이에 생성된 레코드만 로그 쿼리에 사용됩니다. 로그 쿼리를 같은 시간 명령을 사용 하는 경우 **전 (1 일)** , 쿼리는 여전히 오후 12 시 15 1:15 PM 사이 데이터를 사용 기간 동안 해당 간격으로 설정 되어 있기 때문입니다.

구성에서 기간에 쿼리 일치 하는지 확인 합니다. 이전에 표시 된 예제에 대 한 로그 쿼리를 사용 하는 경우 **전 (1 일)** 의 녹색 마커로 기간은 24 시간 또는 하루 1,440 분 (빨간색으로 표시 됨)로 설정 해야 합니다. 이 설정은 쿼리 의도 한 대로 실행 되는지 확인 합니다.

![기간](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>경고 표시 안 함 옵션이 설정됨

문서의 8 단계에서 설명한 것 처럼 [Azure portal에서 로그 경고 규칙을 만들](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), 제공 하는 로그 경고를 **경고 표시 안 함** 구성된 된 시간에 대 한 트리거 및 알림 작업을 표시 하지 않을 수 시간입니다. 결과적으로, 경고가 발생 하지는 생각할 수 있습니다. 실제로 실행 않은 하지만 무시 되었습니다.  

![알림 표시 안 함](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>메트릭 측정 경고 규칙이 잘못됨

*미터법 로그 경고* 특수 기능 및 제한 알림 쿼리 구문에 있는 로그 경고의 하위입니다. 미터법 로그 경고에 대 한 규칙을 쿼리 메트릭 시계열 될 출력에 필요 합니다. 즉, 출력은 해당 집계 된 값과 함께 별도 동일 하 게 크기가 지정 된 기간 동안이 있는 테이블입니다. 

추가 변수를 함께 테이블에 있는 하도록 선택할 수 있습니다 **AggregatedValue**합니다. 테이블을 정렬 하려면 이러한 변수를 사용할 수 있습니다. 

예를 들어, 미터법 로그 경고에 대 한 규칙으로 구성 되었습니다.

- 쿼리 `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 6 시간 기간
- 50 임계값
- 세 개의 연속 위반의 경고 논리
- **집계 시** 마크로 **$table**

명령이 포함 되어 있으므로 **요약 하는 중... 하 여** 두 변수를 제공 하 고 (**타임 스탬프** 하 고 **$table**), 시스템 선택 **$table** 에 대 한 **집계 시** . 시스템에서 결과 테이블을 정렬 합니다 **$table** 다음 스크린샷에 표시 된 대로 필드. 여러 확인 한 다음 **AggregatedValue** 각 테이블 형식에 대 한 인스턴스 (같은 **availabilityResults**) 세 개 이상의 연속 위반 있었는지 확인 합니다.

![여러 값을 사용 하 여 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMQuery.png)

때문에 **집계 시** 에 정의 된 **$table**, 데이터를 기준으로 정렬 됩니다는 **$table** 열 (빨간색으로 표시 됨). 그룹화 하 고 형식에 대 한 확인 합니다 **집계 시** 필드입니다. 

예를 들어 **$table**에 값 **availabilityResults** 하나의 엔터티로 그림 / (주황색으로 표시 됨)으로 간주 됩니다. 이 값 그림/엔터티를 세 개의 연속 위반 (녹색으로 표시 됨)에 대 한 경고를 확인 합니다. 위반이 테이블 값에 대 한 경고를 트리거할 **availabilityResults**합니다. 

마찬가지로, 세 개의 연속 위반의 다른 값에 대해 발생 하는 경우 **$table**, 동일한 작업에 대 한 다른 경고 알림이 트리거됩니다. 경고 서비스는 자동으로 시간별 (주황색으로 표시 됨) 한 그림/엔터티의 값을 정렬 합니다.

이제 수정 된 메트릭 측정 로그 경고에 대 한 규칙을 쿼리에서 가정 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`합니다. 구성의 나머지 동일 하 게으로 이전에 세 개의 연속 위반에 대 한 경고 논리를 포함 합니다. 합니다 **집계 시** 옵션을이 예제의 경우 **타임 스탬프** 기본적으로 합니다. 에 대 한 쿼리에서 하나의 값만 제공 됩니다 **요약 하는 중... 하 여** (즉, **타임 스탬프**). 이전 예제와 같이 실행의 끝부분에서 출력에 다음과 같이 설명 된 대로 것입니다.

   ![단일 값을 사용 하 여 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMtimestamp.png)

때문에 **집계 시** 에 정의 된 **타임 스탬프**, 데이터를 기준으로 정렬 됩니다는 **타임 스탬프** 열 (빨간색으로 표시 됨). 기준으로 그룹화 한 다음 **타임 스탬프**합니다. 예를 들어 값 `2018-10-17T06:00:00Z` 하나의 엔터티로 그림 / (주황색으로 표시 됨)으로 간주 됩니다. 이 값 그림/엔터티를 경고 서비스 없습니다 연속 위반을 찾을 수 (때문에 각 **타임 스탬프** 값에 하나의 항목만). 따라서 경고가 트리거되지 않습니다. 이러한 경우 사용자 해야 하거나 합니다.

- 더미 변수 또는 기존 변수 추가 (같은 **$table**)를 사용 하 여 올바르게 정렬 하는 **집계 시** 필드.
- 경고 논리를 기반으로 사용 하 여 경고 규칙 재구성 **위반 총** 대신 합니다.

## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함

구성 된 [Azure Monitor의 로그 경고 규칙](../platform/alerts-log.md) 에서 볼 때 예기치 않게 트리거될 수 있습니다 [Azure Alerts](../platform/alerts-managing-alert-states.md)합니다. 다음 섹션에서는 몇 가지 일반적인 이유를 설명합니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨

Log Analytics 및 Application Insights 수집 지연 및 처리에 적용 됩니다. 로그 경고 쿼리를 실행 하면 볼 수 있습니다 없는 데이터를 사용할 수 있는지 또는 일부 데이터를 사용할 수 있습니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](../platform/data-ingestion-time.md)합니다.

경고 규칙을 구성한 방법에 따라 misfiring 경우 발생할 수 있는 경고 실행 시 데이터 또는 로그에 일부 데이터가 필요 하지 않습니다. 이러한 경우 좋습니다 경고 쿼리 또는 구성을 변경할 수 있습니다. 

예를 들어 분석 쿼리에서 결과 수가 5 보다 작을 때 트리거되도록 로그 경고 규칙을 구성 하는 경우 경고가 트리거될 데이터 (레코드 0) 나 부분 결과 (레코드) 없는 경우. 하지만 전체 데이터를 사용 하 여 동일한 쿼리는 데이터 수집 지연 후 10 개의 레코드의 결과 제공할 수 있습니다.

### <a name="alert-query-output-is-misunderstood"></a>오해 되 고 경고 쿼리가 출력

분석 쿼리에서 로그 경고에 대한 논리를 제공합니다. Analytics 쿼리를 다양 한 빅 데이터 및 수치 연산 함수를 사용할 수 있습니다. 경고 서비스는 지정 된 기간에 대 한 데이터를 사용 하 여 지정 된 간격에 쿼리를 실행 합니다. 경고 서비스에서 약간의 변화가 경고 유형에 따라 쿼리를 수행 합니다. 이 변경 내용을 볼 수 있습니다 합니다 **쿼리를 실행할 수** 섹션에 **신호 논리 구성** 화면:

![쿼리를 실행할 수](media/alert-log-troubleshoot/LogAlertPreview.png)

**쿼리를 실행할 수** 상자는 로그 경고 서비스를 실행 됩니다. 명시 된 쿼리를 통해 timespan 경고 쿼리가 출력 경고를 만들기 전에 수를 파악 하려는 경우 실행할 수 있습니다 합니다 [Analytics 포털](../log-query/portals.md) 또는 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/)합니다.

## <a name="log-alert-was-disabled"></a>로그 경고를 사용 하지 않도록 설정

다음 섹션에서는 Azure Monitor를 비활성화할 수 있습니다 이유 몇 가지 이유를 나열 합니다 [로그 경고 규칙](../platform/alerts-log.md)합니다.

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>경고가 더 이상 생성 된 리소스가 존재

Azure Monitor에서 만든 로그 경고 규칙 대상으로 Azure Log Analytics 작업 영역을, Azure Application Insights 앱 및 Azure 리소스와 같은 특정 리소스. 로그 경고 서비스를 지정된 된 대상에 대 한 규칙에서 제공 하는 분석 쿼리를 실행 됩니다. 하지만 규칙을 만들면 사용자가 자주 이동-Azure에서 삭제 하거나 로그 경고 규칙의 대상 Azure 내에서 이동 합니다. 경고 규칙의 대상이 유효한 이상 이므로 규칙의 실행이 실패 합니다.

이러한 경우에 Azure Monitor 로그 경고를 사용 하지 않도록 설정 하 고는 하면는 요금이 청구 되지 불필요 하 게 규칙 수 없습니다 (예: 주) 크기 조정 가능 기간 동안 지속적으로 실행 되도록 합니다. Azure Monitor를 통해 로그 경고를 비활성화 하는 경우 정확한 시간을 확인할 수 있습니다 [Azure 활동 로그](../../azure-resource-manager/resource-group-audit.md)합니다. Azure 활동 로그 이벤트는 Azure Monitor의 로그 경고 규칙을 사용 하지 않도록 설정 하는 경우 추가 됩니다.

Azure 활동 로그에서 다음 샘플 이벤트는 지속적인 오류로 인해 비활성화 된 경고 규칙입니다.

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

### <a name="query-used-in-a-log-alert-is-not-valid"></a>로그 경고에 사용 된 쿼리가 올바르지 않습니다.

Azure Monitor의 해당 구성의 일부로 만든 각 로그 경고 규칙 경고 서비스가 정기적으로 실행 되는 분석 쿼리가 지정 해야 합니다. 분석 쿼리 규칙 만들기 또는 업데이트 시 올바른 구문이 있을 수 있습니다. 하지만 경우에 따라 시간 동안 로그 경고 규칙에서 제공한 쿼리 구문 문제를 개발 및 수 규칙 실행이 실패 합니다. 로그 경고 규칙에서 제공 하는 analytics 쿼리 오류를 개발할 수 이유 몇 가지 일반적인 원인은 다음과 같습니다.

- 쿼리를 쓸 [여러 리소스에서 실행](../log-query/cross-workspace-query.md)합니다. 및 지정된 된 리소스 중 하나 이상이 더 이상 존재 합니다.
- [메트릭 측정 형식 로그 경고](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) 구성에 경고가 쿼리 구문은 표준에 부합 되지 않는 경우
- 분석 플랫폼으로 데이터 흐름 없음 되었습니다. [쿼리 실행 오류가 발생 하면](https://dev.loganalytics.io/documentation/Using-the-API/Errors) 하므로 제공 된 쿼리에 대 한 데이터가 없습니다.
- 변경 내용 [쿼리 언어](https://docs.microsoft.com/azure/kusto/query/) 명령 및 함수에 대 한 수정 된 형식을 포함 합니다. 따라서 경고 규칙의 앞부분에 있는 쿼리가 더 이상 유효 합니다.

[Azure Advisor](../../advisor/advisor-overview.md) 이 동작에 대 한 경고 메시지를 표시 합니다. 중간 영향을 사용 하 여 고가용성의 범주 및 "복구 로그 경고 규칙 모니터링 되도록 합니다." 설명은 Azure Advisor에서 특정 로그 경고 규칙에 대 한 권장 사항을 추가 됩니다. Azure Monitor의 로그 경고를 사용 하지 않도록 설정 되며는 요금이 없습니다 청구 않도록 불필요 하 게 규칙을 크기 조정 가능한 기간 (지속적으로 실행할 수 없습니다 하는 경우 Azure Advisor가 7 일에 대 한 권장 사항을 제공한 후 로그 경고 규칙에서 경고 쿼리를 수정 하지 않습니다 하는 경우 같은 주).

Azure Monitor에서 이벤트를 확인 하 여 로그 경고 규칙을 비활성화 하는 경우 정확한 시간을 찾을 수 있습니다 [Azure 활동 로그](../../azure-resource-manager/resource-group-audit.md)합니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 로그 경고](../platform/alerts-unified-log.md)에 대해 알아봅니다.
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
- 에 대해 자세히 알아보세요 [쿼리를 로깅](../log-query/log-query-overview.md)합니다.
