---
title: Azure Monitor의 로그 경고
description: 지정 하는 로그 쿼리 조건을 충족 하는 경우 전자 메일, 알림, 웹 사이트 Url 호출 (웹 후크) 또는 자동화를 트리거합니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 8081c60833c3c02d55ae66ca695ba106dba01450
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294141"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고

## <a name="overview"></a>개요

로그 경고는 [Azure 경고](./alerts-overview.md)에서 지원 되는 경고 유형 중 하나입니다. 로그 경고를 통해 사용자는 [Log Analytics](../log-query/get-started-portal.md) 쿼리를 사용 하 여 설정 된 빈도 마다 리소스 로그를 평가 하 고 결과에 따라 경고를 발생 시킬 수 있습니다. 규칙은 [작업 그룹](./action-groups.md)을 사용 하 여 하나 이상의 작업을 트리거할 수 있습니다.

> [!NOTE]
> [Log Analytics 작업 영역의](../log-query/get-started-portal.md) 로그 데이터를 Azure Monitor 메트릭 저장소로 보낼 수 있습니다. 메트릭 경고는 작업 하는 데이터에 따라 더 적합할 수 있는 [동작이 다릅니다](alerts-metric-overview.md). 로그를 메트릭에 라우팅하는 방법 및 방법에 대 한 자세한 내용은 [로그에 대 한 메트릭 경고](alerts-metric-logs.md)를 참조 하세요.

> [!NOTE]
> 현재 API 버전 `2020-05-01-preview` 및 리소스 중심 로그 경고에 대 한 추가 요금은 없습니다.  미리 보기에 있는 기능의 가격은 추후 발표 되며 청구를 시작 하기 전에 제공 되는 공지가 제공 됩니다. 알림 기간이 끝난 후 새 API 버전 및 리소스 중심 로그 경고를 계속 사용 하도록 선택 하는 경우 해당 요금에 대 한 요금이 청구 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

로그 경고는 Log Analytics 데이터에 대해 쿼리를 실행 합니다. 먼저 [로그 데이터 수집](resource-logs.md) 을 시작 하 고 문제에 대 한 로그 데이터를 쿼리 합니다. Log Analytics의 [경고 쿼리 예제 항목](../log-query/saved-queries.md) 을 사용 하 여 [사용자가 직접 쿼리를 작성](../log-query/get-started-portal.md)하기 위해 검색 하거나 시작할 수 있는 항목을 이해할 수 있습니다.

[Azure 모니터링 참여자](./roles-permissions-security.md) 는 로그 경고를 만들고 수정 하 고 업데이트 하는 데 필요한 일반적인 역할입니다. 리소스 로그에 대 한 액세스 & 쿼리 실행 권한도 필요 합니다. 리소스 로그에 대 한 부분 액세스는 쿼리가 실패 하거나 부분적인 결과를 반환할 수 있습니다. [Azure에서 로그 경고를 구성 하는 방법에 대해 자세히 알아보세요](./alerts-log.md).

> [!NOTE]
> 레거시 [Log Analytics 경고 API](api-alerts.md)를 사용 하 여 관리 하는 데 사용 되는 Log Analytics에 대 한 로그 경고입니다. [현재 SCHEDULEDQUERYRULES API로 전환 하는 방법에 대해 자세히 알아보세요](alerts-log-api-switch.md).

## <a name="query-evaluation-definition"></a>쿼리 평가 정의

로그 검색 규칙 조건 정의는 다음에서 시작 됩니다.

- 실행할 쿼리
- 결과를 사용 하는 방법

다음 섹션에서는 위의 논리를 설정 하는 데 사용할 수 있는 다양 한 매개 변수를 설명 합니다.

### <a name="log-query"></a>로그 쿼리
규칙을 평가 하는 데 사용 되는 [Log Analytics](../log-query/get-started-portal.md) 쿼리입니다. 이 쿼리에서 반환 되는 결과는 경고를 트리거할 지 여부를 결정 하는 데 사용 됩니다. 쿼리 범위를 다음으로 지정할 수 있습니다.

- 특정 리소스 (예: 가상 머신).
- 구독 또는 리소스 그룹과 같은 확장 리소스입니다.
- [리소스 간 쿼리](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)를 사용 하는 여러 리소스 
 
> [!IMPORTANT]
> 경고 쿼리에는 최적의 성능 및 결과의 관련성을 보장 하는 제약 조건이 있습니다. [자세한 내용은 여기를 참조](./alerts-log-query.md)하세요.

> [!IMPORTANT]
> 리소스 중심 및 [리소스 간 쿼리](../log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) 는 현재 scheduledQueryRules API를 사용 하는 경우에만 지원 됩니다. 레거시 [Log Analytics 경고 API](api-alerts.md)를 사용 하는 경우를 전환 해야 합니다. [전환에 대 한 자세한 정보](./alerts-log-api-switch.md)

#### <a name="query-time-range"></a>쿼리 시간 범위

시간 범위는 규칙 조건 정의에 설정 됩니다. 작업 영역 및 Application Insights에서이를 **Period**라고 합니다. 다른 모든 리소스 형식에서는 **Override 쿼리 시간 범위**라고 합니다.

Log analytics에서와 같이 시간 범위는 쿼리 데이터를 지정 된 범위로 제한 합니다. 쿼리에 이전 **명령이 사용 되 었 더라도 시간** 범위가 적용 됩니다.

예를 들어 쿼리는 시간 범위가 60 분 이면 60 분을 검색 합니다 .이 경우에는 이전 **(1d)** 이 포함 되어 있는 경우에도 마찬가지입니다. 시간 범위와 쿼리 시간 필터링이 일치 해야 합니다. 예제 사례에서 기간을 변경 하면 **Period**  /  **쿼리 시간 범위가** 1 일로 재정의 됩니다.

### <a name="measure"></a>측정값

로그 경고는 평가할 수 있는 숫자 값으로 로그를 전환 합니다. 다음 두 가지 작업을 측정할 수 있습니다.

#### <a name="count-of-the-results-table-rows"></a>결과 테이블 행 수

결과 수는 기본 측정값입니다. Windows 이벤트 로그, syslog, 응용 프로그램 예외와 같은 이벤트 작업에 적합 합니다. 로그 레코드가 발생 하거나 계산 된 시간 창에서 발생 하지 않을 때 트리거를 실행 합니다.

로그 경고는 로그에서 데이터를 검색 하 려 할 때 가장 잘 작동 합니다. 로그의 데이터 부족을 감지 하려고 하면 성능이 떨어집니다. 예를 들어 가상 머신 하트 비트에 대 한 경고입니다.

작업 영역 및 Application Insights의 경우 선택 된 **결과 수**를 **기준으로** 호출 됩니다. 다른 모든 리소스 유형에 서 선택 **테이블 행**이 있는 **측정값** 이라고 합니다.

> [!NOTE]
> 로그는 반 구조화 된 데이터 이기 때문에 기본적으로 메트릭 보다 더 숨어 있습니다. 로그에서 데이터 부족을 감지 하려고 할 때 잘못 실행 될 수 있으므로 [메트릭 경고](alerts-metric-overview.md)를 사용 하는 것이 좋습니다. [로그에 대 한 메트릭 경고](alerts-metric-logs.md)를 사용 하 여 로그에서 메트릭 저장소로 데이터를 보낼 수 있습니다.

##### <a name="example-of-results-table-rows-count-use-case"></a>결과 테이블 행 개수 사용 사례의 예

응용 프로그램이 응답 하는 시기를 확인 하려는 경우 오류 코드 500 (내부 서버 오류). 다음 세부 정보로 경고 규칙을 만들 수 있습니다.

- **쿼리:** 

```Kusto
requests
| where resultCode == "500"
```

- **기간:** 15 분
- **경고 빈도:** 15 분
- **임계값:** 0 초과

그러면 경고 규칙이 500 오류 코드로 끝나는 모든 요청을 모니터링 합니다. 쿼리는 최근 15 분 동안 15 분 마다 실행 됩니다. 레코드 하나를 찾으면 경고를 발생 시키고 구성 된 작업을 트리거합니다.

#### <a name="calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value"></a>숫자 열을 기준으로 하는 측정값 계산 (예: CPU 카운터 값)

작업 영역 및 Application Insights의 경우 선택 **메트릭 측정**을 **기반** 으로 호출 됩니다. 다른 모든 리소스 유형에 서 임의의 숫자 열 이름을 선택 하 여 **측정값** 이라고 합니다.

### <a name="aggregation-type"></a>집계 유형

단일 숫자 값으로 집계 하기 위해 여러 레코드에서 수행 되는 계산입니다. 예를 들면 다음과 같습니다.
- **Count** 는 쿼리의 레코드 수를 반환 합니다.
- **Average** 는 정의 된 측정값 열 [**집계 세분성**](#aggregation-granularity) 의 평균을 반환 합니다.

작업 영역 및 Application Insights에서는 **메트릭 측정** 측정 유형 에서만 지원 됩니다. 쿼리 결과에는 사용자 정의 집계 다음에 숫자 값을 제공 하는 AggregatedValue 라는 열이 포함 되어 있어야 합니다. 다른 모든 리소스 종류의 경우에는 해당 이름의 필드에서 **집계 유형이** 선택 됩니다.

### <a name="aggregation-granularity"></a>집계 세분성

여러 레코드를 하나의 숫자 값으로 집계 하는 데 사용 되는 간격을 결정 합니다. 예를 들어 **5 분**을 지정한 경우 레코드는 지정 된 **집계 유형을** 사용 하 여 5 분 간격으로 그룹화 됩니다.

작업 영역 및 Application Insights에서는 **메트릭 측정** 측정 유형 에서만 지원 됩니다. 쿼리 결과에는 쿼리 결과에서 간격을 설정 하는 [bin ()](/azure/kusto/query/binfunction) 이 포함 되어야 합니다. 다른 모든 리소스 유형에 서이 설정을 제어 하는 필드를 **집계 세분성**이라고 합니다.

> [!NOTE]
> [Bin ()](/azure/kusto/query/binfunction) 을 사용 하면 시간 간격이 불균형 해질 수 있으므로 경고 서비스는 [bin ()](/azure/kusto/query/binfunction) 함수를 런타임 시 적절 한 시간을 사용 하 여 [bin_at (](/azure/kusto/query/binatfunction) ) 함수로 자동으로 변환 하 여 고정 소수점이 있는 결과를 확인 합니다.

### <a name="split-by-alert-dimensions"></a>경고 차원으로 분할

숫자 또는 문자열 열을 기준으로 경고를 고유한 조합으로 그룹화 하 여 개별 경고로 분할 합니다. 확장 (구독 또는 리소스 그룹 범위)에서 리소스 중심 경고를 만들 때 Azure 리소스 ID 열을 기준으로 분할할 수 있습니다. Azure 리소스 ID 열을 분할 하면 경고 대상이 지정 된 리소스로 변경 됩니다.

작업 영역 및 Application Insights에서는 **메트릭 측정** 측정 유형 에서만 지원 됩니다. 필드를 **Aggregate On에서**라고 합니다. 3 개의 열로 제한 됩니다. 쿼리에 세 개 이상의 그룹을 추가 하면 예기치 않은 결과가 발생할 수 있습니다. 다른 모든 리소스 유형에 서 조건의 **분할** 섹션에서 구성 됩니다 (분할 6 개로 제한 됨).

#### <a name="example-of-splitting-by-alert-dimensions"></a>경고 차원에의 한 분할 예

예를 들어 특정 리소스 그룹에서 웹 사이트/앱을 실행 하는 여러 가상 컴퓨터에 대 한 오류를 모니터링 하려고 합니다. 로그 경고 규칙을 사용 하 여 다음과 같은 작업을 수행할 수 있습니다.

- **쿼리:** 

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    ```

    작업 영역을 사용 하 고 **메트릭 측정** 경고 논리와 Application Insights 하는 경우 쿼리 텍스트에 다음 줄을 추가 해야 합니다.

    ```Kusto
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

- **리소스 Id 열:** _ResourceId (경고 규칙의 리소스 ID 열로 분할은 현재 구독 및 리소스 그룹에 대해서만 사용할 수 있음)
- **차원/집계 위치:**
  - Computer = VM1, V M 2 (현재 작업 영역 및 Application Insights에 대 한 경고 규칙 정의의 필터링 값을 사용할 수 없습니다. 쿼리 텍스트를 필터링 합니다.
- **기간:** 15 분
- **경고 빈도:** 15 분
- **임계값:** 0 초과

이 규칙은 지난 15 분 동안 가상 컴퓨터에 오류 이벤트가 있는지 여부를 모니터링 합니다. 각 가상 머신은 별도로 모니터링 되 고 작업을 개별적으로 트리거합니다.

> [!NOTE]
> 경고 차원으로 분할은 현재 scheduledQueryRules API에 대해서만 사용할 수 있습니다. 레거시 [Log Analytics 경고 API](api-alerts.md)를 사용 하는 경우를 전환 해야 합니다. [전환에 대해 자세히 알아보세요](./alerts-log-api-switch.md). 규모의 리소스 중심 경고는 API 버전 이상 에서만 지원 됩니다 `2020-05-01-preview` .

## <a name="alert-logic-definition"></a>경고 논리 정의

결과를 실행 하 고 평가할 쿼리를 정의한 후에는 경고 논리 및 작업 발생 시기를 정의 해야 합니다. 다음 섹션에서는 사용할 수 있는 다양 한 매개 변수에 대해 설명 합니다.

### <a name="threshold-and-operator"></a>임계값 및 연산자

쿼리 결과는 임계값 및 연산자와 비교 되는 숫자로 변환 됩니다.

### <a name="frequency"></a>빈도

쿼리가 실행 되는 간격입니다. 5 분에서 1 일로 설정할 수 있습니다. 로그 레코드를 누락 하지 않으려면 [쿼리 시간 범위](#query-time-range) 보다 작거나 같아야 합니다.

예를 들어 기간을 30 분으로 설정 하 고 빈도를 1 시간으로 설정 합니다.  쿼리가 00:00에서 실행 되는 경우 23:30에서 00:00 사이의 레코드를 반환 합니다. 다음에 쿼리를 실행할 때 01:00는 00:30과 01:00 사이의 레코드를 반환 하는입니다. 00:00에서 00:30 사이에 만들어진 모든 레코드는 평가 되지 않습니다.

### <a name="number-of-violations-to-trigger-alert"></a>경고를 트리거할 위반 수

경고 평가 기간과 경고를 트리거하는 데 필요한 오류 수를 지정할 수 있습니다. 경고를 트리거하는 영향 시간을 더 효율적으로 정의할 수 있습니다. 

예를 들어 규칙 [**집계 세분성**](#aggregation-granularity) 이 ' 5 분 '으로 정의 된 경우 지난 시간 중 세 번의 오류 (15 분)가 발생 한 경우에만 경고를 트리거할 수 있습니다. 이 설정은 응용 프로그램 비즈니스 정책에 의해 정의 됩니다.

## <a name="state-and-resolving-alerts"></a>상태 및 경고 해결

로그 경고는 상태 비저장입니다. 경고는 조건이 충족 될 때마다 발생 합니다 (이전에 발생 한 경우에도). 발생 한 경고는 해결 되지 않습니다. [경고를 closed로 표시할](alerts-managing-alert-states.md)수 있습니다. 경고 규칙이 실행 된 후 일정 기간 동안 트리거되지 않도록 작업을 음소거 할 수도 있습니다.

작업 영역 및 Application Insights에서는 **경고 표시 안 함**이라고 합니다. 다른 모든 리소스 형식에서 **음소거 작업**이라고 합니다. 

이 경고 평가 예를 참조 하세요.

| Time    | 로그 조건 평가 | 결과 
| ------- | ----------| ----------| ------- 
| 00:05 | FALSE | 경고가 발생 하지 않습니다. 작업은 호출 되지 않습니다.
| 00:10 | TRUE  | 경고가 발생 하 고 라는 동작 그룹이 있습니다. 새 경고 상태가 활성입니다.
| 00:15 | TRUE  | 경고가 발생 하 고 라는 동작 그룹이 있습니다. 새 경고 상태가 활성입니다.
| 00:20 | FALSE | 경고가 발생 하지 않습니다. 작업은 호출 되지 않습니다. 이전 alerts 상태는 활성 상태로 유지 됩니다.

## <a name="pricing-and-billing-of-log-alerts"></a>로그 경고에 대 한 가격 책정 및 청구

가격 책정 정보는 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)에 있습니다. 로그 경고는 `microsoft.insights/scheduledqueryrules` 다음을 포함 하는 리소스 공급자 아래에 나열 됩니다.

- 리소스 그룹 및 경고 속성과 함께 정확한 리소스 이름과 함께 표시 되는 Application Insights에 대 한 경고를 로깅합니다.
- 리소스 그룹 및 경고 속성과 함께 정확한 리소스 이름과 함께 표시 되는 Log Analytics에 대 한 경고를 로깅합니다. [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)를 사용 하 여 만든 경우
- [레거시 LOG ANALYTICS API](./api-alerts.md) 에서 만든 로그 경고는 [Azure 리소스](../../azure-resource-manager/management/overview.md) 를 추적 하지 않으며 고유한 리소스 이름을 적용 하지 않습니다. 이러한 경고는 `microsoft.insights/scheduledqueryrules` 이 리소스 명명 구조를 포함 하는 숨겨진 리소스로 계속 생성 됩니다 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . 레거시 API에 대 한 로그 경고는 리소스 그룹 및 경고 속성과 함께 숨겨진 리소스 이름과 함께 표시 됩니다.

> [!NOTE]
> 와 같은 지원 되지 않는 리소스 문자 `<, >, %, &, \, ?, /` 는 `_` 숨겨진 리소스 이름에서로 바뀌고 청구 정보에도 반영 됩니다.

> [!NOTE]
> 레거시 [Log Analytics 경고 API](api-alerts.md) 를 사용 하 여 관리 하는 데 사용 되는 Log Analytics에 대 한 로그 경고 및 [저장 된 검색 및 경고 Log Analytics](../insights/solutions.md)의 레거시 템플릿을 사용 합니다. [현재 SCHEDULEDQUERYRULES API로 전환 하는 방법에 대해 자세히 알아보세요](alerts-log-api-switch.md). 스위치를 전환할 때까지 [레거시 LOG ANALYTICS API](api-alerts.md) 를 사용 하 여 모든 경고 규칙 관리를 수행 해야 하며, 숨겨진 리소스는 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

* [Azure의 로그 경고에서 만들기](./alerts-log.md)에 대해 알아봅니다.
* [Azure의 로그 경고에서 웹후크](alerts-log-webhook.md)를 이해합니다.
* [Azure Alerts](./alerts-overview.md)에 대해 알아봅니다.
* [Log Analytics](../log-query/log-query-overview.md)에 대해 자세히 알아보세요.

