---
title: Azure Monitor를 사용 하 여 로그 경고 만들기, 보기 및 관리 Microsoft Docs
description: Azure Monitor를 사용 하 여 로그 경고 규칙을 만들고, 보고, 관리할 수 있습니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 40cf46746587cbd221bd958ccb528c9e40cf18bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299423"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리

## <a name="overview"></a>개요

로그 경고를 통해 사용자는 [Log Analytics](../log-query/get-started-portal.md) 쿼리를 사용 하 여 설정 된 빈도 마다 리소스 로그를 평가 하 고 결과에 따라 경고를 발생 시킬 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용 하 여 하나 이상의 작업을 트리거할 수 있습니다. [로그 경고의 기능 및 용어에 대해 자세히 알아보세요](alerts-unified-log.md).

이 문서에서는 Azure Monitor를 사용 하 여 로그 경고를 만들고 관리 하는 방법을 보여 줍니다. 경고 규칙은 다음과 같은 세 가지 구성 요소로 정의 됩니다.
- 대상: 모니터링할 특정 Azure 리소스입니다.
- 조건: 평가할 논리입니다. 충족 되는 경우 경고가 발생 합니다.  
- 작업: 알림 또는 자동화-전자 메일, SMS, webhook 등

[별도의 문서](alerts-log-create-templates.md)에서 설명 하는 Azure Resource Manager 템플릿을 사용 하 여 로그 경고 규칙을 만들 수도 있습니다.

> [!NOTE]
> [Log Analytics 작업 영역의](../log-query/get-started-portal.md) 로그 데이터를 Azure Monitor 메트릭 저장소로 보낼 수 있습니다. 메트릭 경고는 작업 하는 데이터에 따라 더 적합할 수 있는 [동작이 다릅니다](alerts-metric-overview.md). 로그를 메트릭에 라우팅하는 방법 및 방법에 대 한 자세한 내용은 [로그에 대 한 메트릭 경고](alerts-metric-logs.md)를 참조 하세요.

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Azure Portal을 통해 로그 경고 규칙 만들기

경고에 대 한 쿼리 작성을 시작 하는 단계는 다음과 같습니다.

1. 경고를 원하는 리소스로 이동 합니다.
1. **모니터**에서 **로그**를 선택 합니다.
1. 문제를 나타낼 수 있는 로그 데이터를 쿼리합니다. [경고 쿼리 예제 항목](../log-query/saved-queries.md) 을 사용 하 여 [사용자가 직접 쿼리를 작성](../log-query/get-started-portal.md)하기 위해 검색 하거나 시작할 수 있는 항목을 이해할 수 있습니다. 또한 [최적화된 경고 쿼리를 만드는 방법에 대해 알아봅니다](alerts-log-query.md).
1. ' + 새 경고 규칙 ' 단추를 눌러 경고 생성 흐름을 시작 합니다.

    ![Log Analytics - 경고 설정](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> 리소스 그룹 또는 구독 범위를 사용 하 여 여러 리소스에서 실행 되는 로그에 대해 리소스 액세스 모드를 사용 하는 경우에는 규모에 따라 경고를 만드는 것이 좋습니다. 크기에 대 한 경고는 규칙 관리 오버 헤드를 줄입니다. 리소스를 대상으로 지정할 수 있으려면 결과에 리소스 ID 열을 포함 하세요. [차원으로 경고 분할에 대해 자세히 알아보세요](alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Log Analytics 및 Application Insights에 대 한 로그 경고

1. 쿼리 구문이 올바르면 쿼리의 기록 데이터가 지난 6 시간에서 지난 주까지 차트 기간을 조정 하는 옵션이 포함 된 그래프로 표시 됩니다.
 
    쿼리 결과에 시간 열이 없는 요약 된 데이터 또는 [프로젝트](/azure/kusto/query/projectoperator) 관련 열이 포함 된 경우 차트에는 단일 값이 표시 됩니다.

    ![경고 규칙 구성](media/alerts-log/AlertsPreviewAlertLog.png)

1. [**기간**](alerts-unified-log.md#query-time-range) 옵션을 사용 하 여 지정 된 조건을 평가 하는 시간 범위를 선택 합니다.

1. 로그 경고는 다음과 같은 두 가지 유형의 [**측정값**](alerts-unified-log.md#measure)을 기반으로 할 수 있습니다.
    1. **결과 수** -쿼리에서 반환 된 레코드 수입니다.
    1. **메트릭 측정**  -  선택 된 식 및 [bin ()](/azure/kusto/query/binfunction) 선택 항목 별로 그룹화 된 요약을 사용 하 여 계산 된 *집계 값* 입니다. 예를 들면 다음과 같습니다.

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. 메트릭 측정 경고 논리의 경우 **집계** 옵션을 사용 하 여 [경고를 차원과 분할](alerts-unified-log.md#split-by-alert-dimensions) 하는 방법을 선택적으로 지정할 수 있습니다. 행 그룹화 식은 고유 하 고 정렬 되어야 합니다.

    > [!NOTE]
    > [Bin ()](/azure/kusto/query/binfunction) 을 사용 하면 시간 간격이 불균형 해질 수 있으므로 경고 서비스는 [bin ()](/azure/kusto/query/binfunction) 함수를 런타임 시 적절 한 시간을 사용 하 여 [bin_at (](/azure/kusto/query/binatfunction) ) 함수로 자동으로 변환 하 여 고정 소수점이 있는 결과를 확인 합니다.

    > [!NOTE]
    > 경고 차원으로 분할은 현재 scheduledQueryRules API에 대해서만 사용할 수 있습니다. 레거시 [Log Analytics 경고 API](api-alerts.md)를 사용 하는 경우를 전환 해야 합니다. [전환에 대해 자세히 알아보세요](./alerts-log-api-switch.md). 규모의 리소스 중심 경고는 API 버전 이상 에서만 지원 됩니다 `2020-05-01-preview` .

    ![집계 옵션](media/alerts-log/aggregate-on.png)

1. 그런 다음 미리 보기 데이터를 기반으로 [ **연산자**, **임계값**](alerts-unified-log.md#threshold-and-operator)및 [**빈도**](alerts-unified-log.md#frequency)를 설정 합니다.

1. 또한 필요에 따라 **전체 또는 연속 위반**을 사용 하 여 [경고를 트리거하기 위한 위반 수](alerts-unified-log.md#number-of-violations-to-trigger-alert) 를 설정할 수 있습니다.

1. **완료** 를 선택합니다. 

1. **경고 규칙 이름**, **설명**을 정의 하 고 경고 **심각도**를 선택 합니다. 이러한 세부 정보는 모든 경고 작업에 사용 됩니다. 또한 만들 **때 규칙 사용**을 선택 하 여 경고 규칙을 만들 때 활성화 하지 않도록 선택할 수 있습니다.

1. 경고가 발생 한 후에 규칙 작업을 표시 하지 않을 지 여부를 선택 하 고 [**경고 표시 안 함**](alerts-unified-log.md#state-and-resolving-alerts) 옵션을 사용 합니다. 규칙은 계속 실행 되 고 경고를 생성 하지만 노이즈를 방지 하기 위해 동작이 트리거되지 않습니다. 음소거 작업 값은 경고를 적용 하는 빈도 보다 커야 합니다.

    ![로그 경고에 대한 경고 표시 안 함](media/alerts-log/AlertsPreviewSuppress.png)

1. 경고 조건이 충족 될 때 경고 규칙에서 하나 이상의 [**작업 그룹**](action-groups.md#webhook) 을 트리거해야 하는지 여부를 지정 합니다.

    > [!NOTE]
    > 수행할 수 있는 작업에 대 한 제한은 [Azure 구독 서비스 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md) 을 참조 하세요.  

1. 필요에 따라 로그 경고 규칙의 작업을 사용자 지정할 수 있습니다.

    - **사용자 지정 전자 메일 제목**: 전자 메일 작업의 *전자 메일 제목을* 무시 합니다. 메일 본문은 수정할 수 없으며이 필드는 **전자 메일 주소가 아닙니다**.
    - **사용자 지정 Json 페이로드 포함**: 작업 그룹이 웹 후크 작업을 포함 하 고 있다고 가정 하 고 작업 그룹에서 사용 하는 webhook Json을 재정의 합니다. [로그 경고에 대 한](./alerts-log-webhook.md)웹 후크 작업에 대해 자세히 알아보세요.

    ![경고 로그에 대한 작업 재정의](media/alerts-log/AlertsPreviewOverrideLog.png)

1. 모든 필드가 올바르게 설정 된 경우 **경고 규칙 만들기** 단추를 클릭 하 고 경고를 만들 수 있습니다.

    앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

    ![규칙 만들기](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>경고 관리에서 Log Analytics 및 Application Insights에 대 한 로그 경고를 만드는 중

> [!NOTE]
> 경고 관리에서 만들기는 현재 리소스 중심 로그에 대해 지원 되지 않습니다.

1. [포털](https://portal.azure.com/)에서 **모니터** 를 선택 하 고 **경고**를 선택 합니다.

    ![모니터링](media/alerts-log/AlertsPreviewMenu.png)

1. **새 경고 규칙**을 선택 합니다. 

    ![Add alert](media/alerts-log/AlertsPreviewOption.png)

1. **경고 만들기** 창이 나타납니다. 다음 네 부분으로 구성 됩니다. 
    - 경고가 적용 되는 리소스입니다.
    - 확인할 조건입니다.
    - 조건이 true 인 경우 수행할 동작입니다.
    - 경고의 이름을 입력 하 고 설명 하는 세부 정보입니다. 

    ![규칙 만들기](media/alerts-log/AlertsPreviewAdd.png)

1. **리소스 선택** 단추를 누릅니다. *구독*, *리소스 종류*를 선택 하 여 필터링 하 고 리소스를 선택 합니다. 리소스에 로그를 사용할 수 있는지 확인 합니다.

   ![리소스 선택](media/alerts-log/Alert-SelectResourceLog.png)

1. 다음으로 **조건** 추가 단추를 사용 하 여 리소스에 사용할 수 있는 신호 옵션 목록을 봅니다. **사용자 지정 로그 검색** 옵션을 선택 합니다.

   ![리소스 선택 - 사용자 지정 로그 검색](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 경고 포털은 Log Analytics 및 Application Insights에서 저장 된 쿼리를 나열 하며 템플릿 경고 쿼리로 사용할 수 있습니다.

1. 선택한 후에는 **검색 쿼리** 필드에서 경고 쿼리를 작성 하거나 붙여넣거나 편집 합니다.

1. [마지막 섹션](#log-alert-for-log-analytics-and-application-insights)에서 설명 하는 다음 단계를 계속 합니다.

### <a name="log-alert-for-all-other-resource-types"></a>다른 모든 리소스 유형에 대 한 로그 경고

> [!NOTE]
> 현재 API 버전 `2020-05-01-preview` 및 리소스 중심 로그 경고에 대 한 추가 요금은 없습니다.  미리 보기에 있는 기능의 가격은 추후 발표 되며 청구를 시작 하기 전에 제공 되는 공지가 제공 됩니다. 알림 기간이 끝난 후 새 API 버전 및 리소스 중심 로그 경고를 계속 사용 하도록 선택 하는 경우 해당 요금에 대 한 요금이 청구 됩니다.

1. **조건** 탭에서 시작 합니다.

    1. [**측정값**](alerts-unified-log.md#measure), [**집계 유형**](alerts-unified-log.md#aggregation-type)및 [**집계 세분성**](alerts-unified-log.md#aggregation-granularity) 이 올바른지 확인 합니다. 
        1. 기본적으로이 규칙은 지난 5 분 동안의 결과 수를 계산 합니다.
        1. 요약 된 쿼리 결과를 검색 하는 경우 규칙은 몇 초 내에 자동으로 업데이트 되어이를 캡처합니다.

    1. 필요한 경우 [차원으로 경고 분할](alerts-unified-log.md#split-by-alert-dimensions)을 선택 합니다. 
       - **리소스 ID 열** 이 검색 된 경우 자동으로 선택 되 고, 발생 한 경고의 컨텍스트를 레코드의 리소스로 변경 합니다. 
       - **리소스 ID 열** 을 선택 취소 하 여 구독 또는 리소스 그룹에 대 한 경고를 발생 시킬 수 있습니다. 선택 취소는 쿼리 결과가 상호 리소스를 기반으로 하는 경우에 유용 합니다. 예를 들어 리소스 그룹의 가상 머신의 80%가 높은 CPU 사용량이 발생 하는지 확인 하는 쿼리가 있습니다.
       - 차원 테이블을 사용 하 여 숫자 또는 텍스트 열 유형에 대해 최대 6 개의 추가 splittings을 선택할 수도 있습니다.
       - 경고는 고유 조합에 따라 분할에 따라 개별적으로 발생 하며 경고 페이로드에는이 정보가 포함 됩니다.
    
        ![집계 매개 변수 및 분할 선택](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. **미리 보기** 차트에는 시간별 쿼리 평가 결과가 표시 됩니다. 차트 기간을 변경 하거나 차원에 따라 고유한 경고 분할에서 생성 된 다른 시계열을 선택할 수 있습니다.

        ![차트 미리 보기](media/alerts-log/preview-chart.png)

    1. 그런 다음 미리 보기 데이터를 기반으로 **경고 논리**를 설정 합니다. [ **연산자**, **임계값**](alerts-unified-log.md#threshold-and-operator)및 [**Frequency**](alerts-unified-log.md#frequency)값이 있습니다.

        ![임계값 및 경고 논리를 사용 하 여 차트 미리 보기](media/alerts-log/chart-and-alert-logic.png)

    1. 필요에 따라 **고급 옵션** 섹션에서 [**경고를 트리거하기 위한 위반 수를**](alerts-unified-log.md#number-of-violations-to-trigger-alert) 설정할 수 있습니다.
    
        ![고급 옵션](media/alerts-log/advanced-options.png)

1. **작업** 탭에서 필요한 [작업 그룹](action-groups.md)을 선택 하거나 만듭니다.

    ![작업 탭](media/alerts-log/actions-tab.png)

1. **세부 정보** 탭에서 **경고 규칙 세부 정보**및 **프로젝트 세부 정보**를 정의 합니다. 필요에 따라 작업 **실행을 시작**하지 않을 지 여부를 설정 하거나 경고 규칙이 실행 된 후 기간에 대 한 [**작업을 음소거**](alerts-unified-log.md#state-and-resolving-alerts) 할 수 있습니다.

    > [!NOTE]
    > 로그 경고 규칙은 현재 상태 비저장 이며 음소거가 정의 되지 않은 경우 경고가 생성 될 때마다 작업을 발생 시킵니다.

    ![자세히 탭](media/alerts-log/details-tab.png)

1. **태그** 탭에서 경고 규칙 리소스에 필요한 태그를 설정 합니다.

    ![태그 탭](media/alerts-log/tags-tab.png)

1. **검토 + 만들기** 탭에서 유효성 검사가 실행 되 고 문제를 알려 줍니다. 규칙 정의를 검토 하 고 승인 합니다.
1. 모든 필드가 올바르면 **만들기** 단추를 선택 하 고 경고 규칙 만들기를 완료 합니다. 경고 관리에서 모든 경고를 볼 수 있습니다.
 
    ![검토 및 만들기 탭](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Azure Portal에서 로그 경고 보기 및 관리

1. [포털](https://portal.azure.com/)에서 관련 리소스 또는 **모니터** 서비스를 선택 합니다. 그런 다음 모니터 섹션에서 **경고** 를 선택 합니다.

1. 경고 관리는 발생 한 모든 경고를 표시 합니다. [경고 관리에 대해 자세히 알아보세요](alerts-managing-alert-instances.md).

    > [!NOTE]
    > 로그 경고 규칙은 현재 [상태 비저장 이며 확인 되지](alerts-unified-log.md#state-and-resolving-alerts)않습니다.

1. 규칙을 편집 하려면 위쪽 표시줄에서 **규칙 관리** 단추를 선택 합니다.

    ![ 경고 규칙 관리](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>PowerShell을 사용 하 여 로그 경고 관리

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell은 현재 API 버전에서 지원 되지 않습니다. `2020-05-01-preview`

아래 나열 된 PowerShell cmdlet은 [예약 된 쿼리 규칙 API](/rest/api/monitor/scheduledqueryrules/)를 사용 하 여 규칙을 관리 하는 데 사용할 수 있습니다.

- [AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : PowerShell cmdlet을 통해 새 로그 경고 규칙을 만듭니다.
- [AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : PowerShell Cmdlet을 설정 하 여 기존 로그 경고 규칙을 업데이트 합니다.
- [AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : PowerShell cmdlet은 로그 경고의 원본 매개 변수를 지정 하는 개체를 만들거나 업데이트 합니다. AzScheduledQueryRule 및 [AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet [의](/powershell/module/az.monitor/new-azscheduledqueryrule) 입력으로 사용 됩니다.
- [AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): PowerShell cmdlet은 로그 경고의 일정 매개 변수를 지정 하는 개체를 만들거나 업데이트 합니다. AzScheduledQueryRule 및 [AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet [의](/powershell/module/az.monitor/new-azscheduledqueryrule) 입력으로 사용 됩니다.
- [AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : PowerShell cmdlet은 로그 경고에 대 한 작업 매개 변수를 지정 하는 개체를 만들거나 업데이트 합니다. AzScheduledQueryRule 및 [AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet [의](/powershell/module/az.monitor/new-azscheduledqueryrule) 입력으로 사용 됩니다.
- [AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : PowerShell cmdlet은 로그 경고에 대 한 작업 그룹 매개 변수를 지정 하는 개체를 만들거나 업데이트 합니다. [AzScheduledQueryRuleAlertingAction cmdlet의](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) 입력으로 사용 됩니다.
- [AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : PowerShell cmdlet은 로그 경고에 대 한 트리거 조건 매개 변수를 지정 하는 개체를 만들거나 업데이트 합니다. [AzScheduledQueryRuleAlertingAction cmdlet의](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) 입력으로 사용 됩니다.
- [AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : PowerShell cmdlet은 메트릭 [측정 유형 로그 경고](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value)에 대 한 메트릭 트리거 조건 매개 변수를 지정 하는 개체를 만들거나 업데이트 합니다. [AzScheduledQueryRuleTriggerCondition cmdlet의](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) 입력으로 사용 됩니다.
- [AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : PowerShell cmdlet-기존 로그 경고 규칙 또는 특정 로그 경고 규칙을 나열 합니다.
- [AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : 로그 경고 규칙을 사용 하거나 사용 하지 않도록 설정 하는 PowerShell cmdlet
- [AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): 기존 로그 경고 규칙을 삭제 하는 PowerShell cmdlet

> [!NOTE]
> ScheduledQueryRules PowerShell cmdlet은 현재 [예약 된 쿼리 규칙 API](/rest/api/monitor/scheduledqueryrules/)에서 만든 규칙만 관리할 수 있습니다. 레거시 [Log Analytics 경고 api](api-alerts.md) 를 사용 하 여 만든 로그 경고 규칙은 [예약 된 쿼리 규칙 api로 전환한 후에](alerts-log-api-switch.md)만 PowerShell을 사용해 서만 관리할 수 있습니다.

PowerShell을 사용 하 여 로그 경고 규칙을 만드는 예제 단계는 다음과 같습니다.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

PowerShell을 사용 하 여 [템플릿 및 매개 변수](./alerts-log-create-templates.md) 파일을 사용 하 여 로그 경고를 만들 수도 있습니다.

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>CLI를 사용 하 여 로그 경고 관리

> [!NOTE]
> Azure CLI 지원은 scheduledQueryRules API 버전 이상 에서만 사용할 수 있습니다 `2020-05-01-preview` . 이전 API 버전은 아래에 설명 된 템플릿과 함께 Azure Resource Manager CLI를 사용할 수 있습니다. 레거시 [Log Analytics 경고 API](api-alerts.md)를 사용 하는 경우 CLI를 사용 하도록 전환 해야 합니다. [전환에 대해 자세히 알아보세요](./alerts-log-api-switch.md).

이전 섹션에서는 Azure Portal를 사용 하 여 로그 경고 규칙을 만들고, 보고, 관리 하는 방법을 설명 했습니다. 이번 섹션에서는 플랫폼 간 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 동일한 작업을 수행하는 방법을 설명합니다. Azure CLI를 가장 빠르게 사용하는 방법은 [Azure Cloud Shell](../../cloud-shell/overview.md)입니다. 이 문서에서는 Cloud Shell을 사용 합니다.

1. Azure Portal로 이동 하 여 **Cloud Shell**을 선택 합니다.

1. 프롬프트에서 ``--help`` 옵션이 있는 명령을 사용하여 명령 및 사용 방법에 대해 자세히 알아볼 수 있습니다. 예를 들어 다음 명령은 로그 경고를 만들고 보고 관리 하는 데 사용할 수 있는 명령 목록을 보여줍니다.

    ```azurecli
    az monitor scheduled-query --help
    ```

1. 시스템 이벤트 오류 개수를 모니터링 하는 로그 경고 규칙을 만들 수 있습니다.

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. 다음 명령을 사용 하 여 리소스 그룹에서 모든 로그 경고를 볼 수 있습니다.

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. 규칙의 이름 또는 리소스 ID를 사용 하 여 특정 로그 경고 규칙의 세부 정보를 볼 수 있습니다.

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. 다음 명령을 사용 하 여 로그 경고 규칙을 사용 하지 않도록 설정할 수 있습니다.

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. 다음 명령을 사용 하 여 로그 경고 규칙을 삭제할 수 있습니다.

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

Azure Resource Manager CLI를 [템플릿](./alerts-log-create-templates.md) 파일과 함께 사용할 수도 있습니다.

```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

만들기가 성공 하면 201이 반환 됩니다. 업데이트에 성공 하면 200이 반환 됩니다.

## <a name="next-steps"></a>다음 단계

* [로그 경고](./alerts-unified-log.md)에 대해 알아봅니다.
* [Azure Resource Manager 템플릿을](./alerts-log-create-templates.md)사용 하 여 로그 경고를 만듭니다.
* [로그 경고에 대 한 webhook 작업을](./alerts-log-webhook.md)이해 합니다.
* [로그 쿼리에](../log-query/log-query-overview.md)대해 자세히 알아보세요.

