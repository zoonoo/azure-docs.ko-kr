---
title: Azure Monitor에서 로그 경고 문제 해결 | Microsoft Docs
description: Azure의 로그 경고 규칙에 대 한 일반적인 문제, 오류 및 해결 방법입니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.subservice: alerts
ms.date: 10/29/2018
ms.openlocfilehash: ec2ffe71a32781a855da258f3621738f1a5f6be4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294294"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고 문제 해결  

이 문서에서는 Azure Monitor에서 로그 경고와 관련 된 일반적인 문제를 해결 하는 방법을 보여 줍니다. 또한 로그 경고의 기능 및 구성과 관련 된 일반적인 문제에 대 한 해결 방법을 제공 합니다.

로그 경고를 통해 사용자는 [Log Analytics](../log-query/get-started-portal.md) 쿼리를 사용 하 여 설정 된 빈도 마다 리소스 로그를 평가 하 고 결과에 따라 경고를 발생 시킬 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용 하 여 하나 이상의 작업을 트리거할 수 있습니다. [로그 경고의 기능 및 용어에 대해 자세히 알아보세요](alerts-unified-log.md).

> [!NOTE]
> 이 문서에서는 Azure Portal 경고 규칙을 표시 하 고 연결 된 작업 그룹에서 알림을 수행 하지 않는 경우를 고려 하지 않습니다. 이러한 경우 문제 해결에 대 한 세부 정보를 [참조 하세요.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

### <a name="data-ingestion-time-for-logs"></a>로그에 대 한 데이터 수집 시간

Azure Monitor는 전 세계에서 tb의 고객 로그를 처리 하므로 [로그 수집 대기 시간이](./data-ingestion-time.md)발생할 수 있습니다.

로그는 반 구조화 된 데이터 이며 근본적으로 메트릭 보다 더 숨어 있습니다. 발생 한 경고의 지연 시간을 4 분 넘게 초과 하는 경우 [메트릭 경고](alerts-metric-overview.md)를 사용 하는 것이 좋습니다. [로그에 대 한 메트릭 경고](alerts-metric-logs.md)를 사용 하 여 로그에서 메트릭 저장소로 데이터를 보낼 수 있습니다.

시스템은 경고 평가를 여러 번 다시 시도 하 여 대기 시간을 줄입니다. 데이터가 도착 하면 경고가 발생 합니다. 대부분의 경우에는 로그 레코드 시간과 같지 않습니다.

### <a name="incorrect-query-time-range-configured"></a>잘못 구성 된 쿼리 시간 범위

쿼리 시간 범위는 규칙 조건 정의에 설정 되어 있습니다. 이 필드는 작업 영역 및 Application Insights에 대 한 **기간** 이라고 하 고 다른 모든 리소스 유형에 대해서는 **Override query time range** 라고 합니다. Log analytics에서와 같이 시간 범위는 쿼리 데이터를 지정 된 기간으로 제한 합니다. 쿼리에 이전 **명령이 사용 되 었 더라도 시간** 범위가 적용 됩니다. 

예를 들어 쿼리는 시간 범위가 60 분 이면 60 분을 검색 합니다 .이 경우에는 이전 **(1d)** 이 포함 되어 있는 경우에도 마찬가지입니다. 시간 범위와 쿼리 시간 필터링이 일치 해야 합니다. 예제 사례에서 기간을 변경 하면 **Period**  /  **쿼리 시간 범위가** 1 일로 재정의 됩니다.

![기간](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="actions-are-muted-in-the-alert-rule"></a>작업은 경고 규칙에서 음소거 됩니다.

로그 경고는 설정 된 시간 동안 발생 한 경고 작업을 음소거 하는 옵션을 제공 합니다. 이 필드를 작업 영역에서 **경고 표시 안 함** 및 Application Insights 이라고 합니다. 다른 모든 리소스 형식에서 **음소거 작업**이라고 합니다. 

일반적인 문제는 서비스 문제로 인해 경고가 작업을 실행 하지 않은 것으로 생각 하는 것입니다. 규칙 구성에 의해 음소거 된 경우에도 마찬가지입니다.

![알림 표시 안 함](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-with-splitting-using-the-legacy-log-analytics-api"></a>레거시 Log Analytics API를 사용 하 여 분할 하는 메트릭 측정 경고 규칙

[메트릭 측정](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 은 요약 된 시계열 결과를 기반으로 하는 로그 경고의 한 유형입니다. 이러한 규칙은 열을 기준으로 그룹화 하 여 [경고를 분할할](alerts-unified-log.md#split-by-alert-dimensions)수 있습니다. 레거시 Log Analytics API를 사용 하는 경우 분할은 예상 대로 작동 하지 않습니다. 레거시 API에서 그룹화를 선택 하는 것은 지원 되지 않습니다.

현재 ScheduledQueryRules API를 사용 하면 예상 대로 작동 하는 [메트릭 측정](alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 규칙에서 **집계** 를 설정할 수 있습니다. [현재 SCHEDULEDQUERYRULES API로 전환 하는 방법에 대해 자세히 알아보세요](alerts-log-api-switch.md).

## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함

[Azure Monitor에서 구성 된 로그 경고 규칙이](./alerts-log.md) 예기치 않게 트리거될 수 있습니다. 다음 섹션에서는 몇 가지 일반적인 이유를 설명 합니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨

Azure Monitor는 전 세계에서 tb의 고객 로그를 처리 하므로 [로그 수집 대기 시간이](./data-ingestion-time.md)발생할 수 있습니다.

로그는 반 구조화 된 데이터 이며 근본적으로 메트릭 보다 더 숨어 있습니다. 발생 한 경고에 너무 많은 발생이 발생 하는 경우 [메트릭 경고](alerts-metric-overview.md)를 사용 하는 것이 좋습니다. [로그에 대 한 메트릭 경고](alerts-metric-logs.md)를 사용 하 여 로그에서 메트릭 저장소로 데이터를 보낼 수 있습니다.

로그 경고는 로그에서 데이터를 검색 하 려 할 때 가장 잘 작동 합니다. 로그의 데이터 부족을 감지 하려고 하면 성능이 떨어집니다. 예를 들어 가상 머신 하트 비트에 대 한 경고입니다. 

거짓 경고를 방지 하는 기본 제공 기능이 있지만 대기 시간이 급증 하는 데이터 (30 분 이상) 및 데이터에 계속 발생할 수 있습니다.

### <a name="query-optimization-issues"></a>쿼리 최적화 문제

경고 서비스는 낮은 부하 및 경고 대기 시간을 최적화 하도록 쿼리를 변경 합니다. 경고 흐름은 경고에 대 한 문제를 나타내는 결과를 변환 하기 위해 작성 되었습니다. 예를 들어 다음과 같은 쿼리의 경우

``` Kusto
SecurityEvent
| where EventID == 4624
```

사용자가 경고를 사용 하는 경우이 이벤트 유형이 발생 하면 경고 논리가 쿼리에 추가 됩니다 `count` . 실행 되는 쿼리는 다음과 같습니다.

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

쿼리에 경고 논리를 추가할 필요가 없으며이로 인해 문제가 발생할 수도 있습니다. 위의 예에서는 쿼리에를 포함 하는 경우 `count` 경고 서비스가를 수행 하므로 항상 값 1이 반환 됩니다 `count` `count` .

최적화 된 쿼리는 로그 경고 서비스를 실행 하는 것입니다. Log Analytics [포털](../log-query/log-query-overview.md) 또는 [API](/rest/api/loganalytics/)에서 수정 된 쿼리를 실행할 수 있습니다.

작업 영역 및 Application Insights의 경우 조건 창에서 **실행할 쿼리** 라고 합니다. 기타 모든 리소스 종류의 조건 탭에서 **최종 경고 쿼리 보기** 를 선택 합니다.

![실행될 쿼리](media/alert-log-troubleshoot/LogAlertPreview.png)

## <a name="log-alert-was-disabled"></a>로그 경고를 사용 하지 않도록 설정 했습니다.

다음 섹션에서는 Azure Monitor 로그 경고 규칙을 사용 하지 않도록 설정할 수 있는 몇 가지 이유를 나열 합니다. [규칙을 사용 하지 않도록 설정할 때 전송 되는 활동 로그의 예](#activity-log-example-when-rule-is-disabled)도 포함 되어 있습니다.

### <a name="alert-scope-no-longer-exists-or-was-moved"></a>경고 범위가 더 이상 존재 하지 않거나 이동 되었습니다.

경고 규칙의 범위 리소스가 더 이상 유효 하지 않으면 규칙 실행이 실패 합니다. 이 경우 요금 청구도 중지 됩니다.

오류가 계속 발생 하면 일주일 후 로그 경고를 사용 하지 않도록 설정 Azure Monitor.

### <a name="query-used-in-a-log-alert-isnt-valid"></a>로그 경고에 사용 된 쿼리가 잘못 되었습니다.

로그 경고 규칙을 만들면 올바른 구문에 대 한 쿼리의 유효성이 검사 됩니다. 그러나 경우에 따라 로그 경고 규칙에 제공 된 쿼리가 실패할 수 있습니다. 몇 가지 일반적인 이유는 다음과 같습니다.

- API를 통해 규칙이 생성 되었으며 사용자가 유효성 검사를 건너뛰었습니다.
- 쿼리가 [여러 리소스에서 실행 되](../log-query/cross-workspace-query.md) 고 하나 이상의 리소스가 삭제 되거나 이동 되었습니다.
- 다음 이유로 인해 [쿼리가 실패](https://dev.loganalytics.io/documentation/Using-the-API/Errors) 합니다.
    - 로깅 솔루션이 [작업 영역에 배포](../insights/solutions.md#install-a-monitoring-solution)되지 않았으므로 테이블이 생성 되지 않습니다.
    - 30 일 넘게 쿼리 내의 테이블로 이동 하는 데이터를 중지 했습니다.
    - 데이터 흐름이 시작 되지 않았으므로 [사용자 지정 로그 테이블이](data-sources-custom-logs.md) 아직 만들어지지 않았습니다.
- [쿼리 언어](/azure/kusto/query/) 의 변경 내용에는 명령 및 함수에 대 한 수정 된 형식이 포함 됩니다. 따라서 이전에 제공 된 쿼리가 더 이상 유효 하지 않습니다.

이 동작에 대 한 경고를 [Azure Advisor](../../advisor/advisor-overview.md) 합니다. 영향을 받는 로그 경고 규칙에 대 한 권장 사항이 추가 됩니다. 사용 되는 범주는 ' 고가용성 ' 이며 중간에 영향을 주는 것 이며 ' 모니터링을 보장 하기 위해 로그 경고 규칙을 복구 하십시오. '에 대 한 설명을 제공 합니다.

## <a name="alert-rule-quota-was-reached"></a>경고 규칙 할당량에 도달 했습니다.

구독 및 리소스에 대한 로그 검색 경고 규칙 수는 [여기](../service-limits.md) 설명된 할당량 한도에 따라 결정됩니다.

### <a name="recommended-steps"></a>권장되는 단계
    
할당량 한도에 도달 하면 다음 단계를 통해 문제를 해결할 수 있습니다.

1. 더 이상 사용 되지 않는 로그 검색 경고 규칙을 삭제 하거나 사용 하지 않도록 설정 해 보세요.
1. 규칙 수를 줄이기 위해 [차원과 경고 분할](alerts-unified-log.md#split-by-alert-dimensions) 을 사용 합니다. 이러한 규칙은 다양 한 리소스 및 검색 사례를 모니터링할 수 있습니다.
1. 할당량 제한을 늘려야 하는 경우 지원 요청을 계속 열고 다음 정보를 제공 합니다.

    - 할당량 제한을 늘려야 하는 구독 Id 및 리소스 Id입니다.
    - 할당량 증가에 대 한 이유입니다.
    - 할당량 증가에 대 한 리소스 유형: **Log Analytics**, **Application Insights**등
    - 할당량 제한을 요청 했습니다.


### <a name="to-check-the-current-usage-of-new-log-alert-rules"></a>새 로그 경고 규칙의 현재 사용량을 확인 하려면
    
#### <a name="from-the-azure-portal"></a>Azure Portal에서

1. *경고* 화면을 열고 *경고 규칙 관리* 를 선택 합니다.
2. *구독* 드롭다운 컨트롤을 사용하여 관련 구독을 필터링합니다.
3. 특정 리소스 그룹, 리소스 유형 또는 리소스로 필터링 하지 않도록 합니다.
4. *신호 형식* 드롭다운 컨트롤에서 '로그 검색'을 선택합니다.
5. *상태* 드롭다운 컨트롤이 '사용'으로 설정되어 있는지 확인합니다.
6. 로그 검색 경고 규칙의 총 수가 규칙 목록 위에 표시됩니다.

#### <a name="from-api"></a>API에서

- PowerShell- [AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule)
- REST API - [구독별 목록](/rest/api/monitor/scheduledqueryrules/listbysubscription)

## <a name="activity-log-example-when-rule-is-disabled"></a>규칙을 사용 하지 않는 경우의 활동 로그 예제

쿼리가 계속 7 일 동안 실패 하면 로그 경고를 사용 하지 않도록 설정 하 고 규칙의 요금 청구를 중지 Azure Monitor 합니다. Azure Monitor [Azure 활동 로그](../../azure-resource-manager/management/view-activity-logs.md)에서 로그 경고를 사용 하지 않도록 설정 하는 정확한 시간을 확인할 수 있습니다. 다음 예제를 참조 하세요.

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

## <a name="next-steps"></a>다음 단계

- [Azure의 로그 경고](./alerts-unified-log.md)에 대해 알아봅니다.
- [로그 경고 구성](../log-query/log-query-overview.md)에 대해 자세히 알아보세요.
- [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아보세요.
