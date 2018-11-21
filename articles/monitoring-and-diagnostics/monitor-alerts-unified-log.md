---
title: Azure Monitor의 로그 경고
description: Azure Alerts에 대해 지정한 분석 쿼리 조건이 충족될 경우 이메일, 알림, 웹 사이트 URL 호출(웹후크) 또는 자동화를 트리거합니다.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 4dae003b011e8e33f0ae935d6dd8399a5687dff6
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633770"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고
이 문서에서는 [Azure Alerts](monitoring-overview-alerts.md) 내에서 지원되는 경고 형식 중 하나이며 사용자가 Azure의 분석 플랫폼을 경고의 기준으로 사용하게 하는 로그 경고의 세부 정보를 제공합니다.

로그 경고는 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)에 대해 만든 로그 검색 규칙으로 이루어집니다. 로그 경고의 사용법에 대한 자세한 내용은 [Azure에서 로그 경고 만들기](alert-log.md)를 참조

> [!NOTE]
> [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md)에서 인기 있는 로그 데이터는 이제 Azure Monitor의 메트릭 플랫폼에서도 사용할 수 있습니다. 세부 정보 보기의 경우 [로그에 대한 메트릭 경고](monitoring-metric-alerts-logs.md)를 참조하세요.


## <a name="log-search-alert-rule---definition-and-types"></a>로그 검색 경고 규칙 - 정의 및 형식

로그 검색 규칙은 일정한 간격으로 지정된 로그 쿼리를 자동으로 실행하도록 Azure Alerts에 의해 만들어집니다.  로그 쿼리 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다. 그런 다음, 규칙은 [작업 그룹](monitoring-action-groups.md)을 사용하여 하나 이상의 작업을 자동으로 실행할 수 있습니다. 

로그 검색 규칙은 다음 세부 정보에 의해 정의됩니다.
- **로그 쿼리**  경고 규칙이 실행될 때마다 실행되는 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 생성할지 여부를 결정하는 데 사용됩니다. 사용자에게 외부 응용 프로그램에 대한 액세스 권한이 있다면 Analytics 쿼리에는 [응용 프로그램 간 호출](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), 작업 영역 간 호출 및 [리소스 간 호출](../log-analytics/log-analytics-cross-workspace-search.md)이 포함될 수 있습니다. 

    > [!IMPORTANT]
    > 사용자에게는 경고 규칙 또는 경고 쿼리의 분석 대상에 대한 액세스 및 쿼리 실행 권한과 함께 Azure Monitor에서 로그 경고 생성, 수정 및 업데이트에 대한 [Azure 모니터링 기여자](monitoring-roles-permissions-security.md) 역할이 있어야 합니다. 만드는 사용자에게 경고 규칙 또는 경고 쿼리의 모든 분석 대상에 대한 액세스 권한이 없는 경우 규칙 만들기가 실패할 수 있거나, 로그 경고 규칙이 부분적인 결과를 통해 실행됩니다.

- **기간**  쿼리에 대한 시간 범위를 지정합니다. 쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다. 기간은 남용을 방지하고 쿼리 로그에 사용되는 시간 명령(ago 등)을 회피하도록 로그 쿼리에 대해 인출된 데이터를 제한합니다. <br>*예를 들어 기간을 60분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 로그 쿼리를 실행하기 위해 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다. 이제 로그 쿼리가 ago(7d)와 같은 명령을 사용하는 경우 데이터가 지난 60분 동안만 존재하는 것처럼 로그 쿼리는 오후 12시 15분에서 1시 15분 사이의 데이터에 대해서만 실행됩니다. 쿼리 로그에서 지정된 대로 7일 동안의 데이터에 대해서는 실행되지 않습니다.*
- **빈도**.  쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간 이하여야 합니다.  값이 기간을 초과하는 경우 레코드가 누락될 위험이 있습니다.<br>*예를 들어 30분의 기간과 60분의 빈도를 사용하는 것이 좋습니다.  쿼리가 1:00에 실행되면 오후 12:30 및 1:00 사이의 레코드를 반환합니다.  1:30 및 2:00 사이의 레코드를 반환하게 된다면 다음으로 쿼리가 실행되는 시간은 2:00입니다.  1:00 및 1:30 사이에 생성된 레코드는 평가되지 않습니다.*
- **임계값**.  로그 검색 결과를 평가하여 경고를 만들어야 하는지 여부를 결정합니다.  임계값은 다양한 유형의 로그 검색 경고 규칙에 따라 다릅니다.

로그 검색 규칙은 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)에 적용되고 두 가지 형식일 수 있습니다. 이러한 각 유형에 대해서는 다음 섹션에서 자세히 설명합니다.

- **[결과 수](#number-of-results-alert-rules)** - 로그 검색에서 반환되는 레코드 수가 지정된 수를 초과할 때 만들어지는 단일 경고입니다.
- **[미터법](#metric-measurement-alert-rules)** -  로그 검색 결과에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 만들어지는 경고입니다.

경고 규칙 유형 간의 차이점은 다음과 같습니다.

- *결과 수* 경고 규칙은 항상 단일 경고를 만드는 반면, *미터법* 경고 규칙은 임계값을 초과하는 각 개체에 대해 경고를 만듭니다.
- *결과 수* 경고 규칙은 임계값을 한 번 초과하면 경고를 만듭니다. *미터법* 경고 규칙은 특정 시간 간격 동안 임계값이 특정 횟수를 초과하면 경고를 만들 수 있습니다.

### <a name="number-of-results-alert-rules"></a>결과 수 경고 규칙
**결과 수** 경고 규칙은 검색 쿼리에서 반환된 레코드 수가 지정된 임계값을 초과하면 단일 경고를 만듭니다. 이 형식의 경고 규칙은 Windows 이벤트 로그, Syslog, WebApp 응답 및 사용자 지정 로그와 같은 이벤트 작업에 적합합니다.  특정 오류 이벤트가 생성될 때 또는 특정 기간 내에 복수의 오류 이벤트가 생성되는 경우 경고를 만드는 것이 좋습니다.

**임계값**: 결과 수 경고 규칙의 임계값은 특정 값보다 크거나 작습니다.  로그 검색에서 반환된 레코드 수가 이 조건과 일치하면 경고를 만듭니다.

단일 이벤트에 대해 경고하려면 결과 수를 0보다 크게 설정하고, 마지막 쿼리 실행 이후 생성된 단일 이벤트 발생을 확인합니다. 일부 응용 프로그램은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 응용 프로그램이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 임계값을 **1보다 작게**로 설정할 수 있습니다.

#### <a name="example-of-number-of-records-type-log-alert"></a>레코드 수 형식 로그 경고의 예제
웹 기반 앱이 코드 500(즉) 내부 서버 오류로 사용자에게 응답하는 경우를 알아야 하는 시나리오를 고려해보세요. 다음 세부 정보로 경고 규칙을 만들 수 있습니다.  
- **쿼리:** requests | resultCode == "500"<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **임계값:** 0 초과<br>

그런 후 경고가 30분 동안의 데이터로 5분 간격으로 쿼리를 실행하여 결과 코드가 500인 레코드를 찾습니다. 이러한 레코드가 하나라도 있으면 경고가 발생하고, 구성된 작업이 트리거됩니다.

### <a name="metric-measurement-alert-rules"></a>미터법 경고 규칙

- **미터법** 경고 규칙은 쿼리에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 경고를 만듭니다.  **결과 수** 경고 규칙과는 다음과 같이 분명하게 구별됩니다.
- **집계 함수**: 수행되는 계산과 잠재적으로 집계할 숫자 필드를 결정합니다.  예를 들어 **count()** 는 쿼리의 레코드 수를 반환하고, **avg(CounterValue)** 는 해당 간격 동안 CounterValue 필드의 평균을 반환합니다. 쿼리의 집계 함수는 AggregatedValue로 명명되어야 하며 숫자 값을 제공해야 합니다.다. 
- **그룹 필드**: 이 필드의 각 인스턴스에 대해 집계된 값이 있는 레코드가 만들어지며 각각에 대해 경고가 생성될 수 있습니다.  예를 들어 각 컴퓨터에 대해 경고를 생성하려면 **컴퓨터별**을 사용합니다. 

    > [!NOTE]
    > Application Insights를 기반으로 하는 메트릭 측정 경고 규칙에 대해서는 데이터를 그룹화하기 위한 필드를 지정할 수 있습니다. 이를 위해 규칙 정의의 **집계** 옵션을 사용합니다.   
    
- **시간 간격**: 데이터가 집계되는 시간 간격을 정의합니다.  예를 들어 **5분**을 지정한 경우 경고에 대해 지정한 기간 동안 5분 간격으로 집계된 그룹 필드의 각 인스턴스에 대한 레코드가 만들어집니다.

    > [!NOTE]
    > 쿼리에서 bin 함수를 사용하여 간격을 지정해야 합니다. bin()이 결국 같지 않은 시간 간격이 될 수 있으므로, 경고는 런타임 시 적절한 시간을 사용하여 bin 명령을 bin_at 명령으로 자동 변환함으로써 고정 시점이 포함된 결과를 보장합니다. 로그 경고의 미터법 형식은 단일 bin() 명령을 포함한 쿼리를 사용하도록 설계되었습니다.
    
- **임계값**: 미터법 경고 규칙의 임계값은 집계 값 및 위반 횟수로 정의됩니다.  로그 검색에서 데이터 요소가 이 값을 초과하면 위반으로 간주됩니다.  결과의 개체에서 위반 횟수가 지정된 값을 초과하면 해당 개체에 대한 경고가 만들어집니다.

#### <a name="example-of-metric-measurement-type-log-alert"></a>미터법 형식 로그 경고의 예제
컴퓨터에서 30분 동안 90% 프로세서 사용률을 3회 초과하는 경우 경고가 필요한 시나리오를 고려해 보세요.  다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **집계 값:** 90 초과<br>
- **경고 트리거 조건:** 총 위반 2 초과<br>

이 쿼리는 각 컴퓨터에 대해 5분 간격으로 평균 값을 만듭니다.  이 쿼리는 이전 30분 동안 수집된 데이터에 대해 5분마다 실행됩니다.  세 개의 컴퓨터에 대한 샘플 데이터는 다음과 같습니다.

![샘플 쿼리 결과](media/monitor-alerts-unified-log/metrics-measurement-sample-graph.png)

이 예제에서 srv02와 srv03은 기간 동안 90% 임계값을 3회 위반했기 때문에 이들에 대해 별도의 경고가 만들어집니다.  **경고 트리거 조건**이 **연속**으로 변경된 경우 세 개의 연속 샘플에 대한 임계값을 위반했기 때문에 srv03에 대해서만 경고가 만들어집니다.

## <a name="log-search-alert-rule---firing-and-state"></a>로그 검색 경고 규칙 - 실행 및 상태
로그 검색 경고 규칙은 구성 및 사용 된 사용자 지정 분석 쿼리에 따라 사용자가 작성한 논리에서 작동합니다. 정확한 조건의 논리 또는 경고 규칙이 트리거되어야 하는 이유는 Analytics 쿼리에 캡슐화되므로 각 로그 경고 규칙마다 쿼리가 다를 수 있습니다. Azure Alerts는 로그 검색 경고 규칙의 임계값 조건을 충족하거나 초과하는 경우 특정 근본 원인의 부족한 정보를 로그 결과 내에 보유합니다. 따라서 로그 경고는 상태 비저장으로 언급되며, 로그 검색 결과가 *결과 수* 또는 *미터법* 형식 조건의 로그 경고에 지정된 임계값을 충분히 초과할 때마다 실행됩니다. 로그 경고 규칙은 다만 경고가 모두 해결되지 않고 경고 조건이 사용자 지정 분석 쿼리의 결과로 충족되는 한 지속적으로 실행을 유지합니다. 모니터링 오류의 정확한 근본 원인의 논리가 사용자가 제공한 분석 쿼리 내에서 마스킹되므로 Azure Alerts로서는 임계값을 충족하지 못하는 로그 검색 결과가 문제의 해결을 나타내는지 여부를 확정적으로 추론할 방법이 없습니다.

이제 [결과 수 형식 로그 경고에 대해 제공된 예제](#example-of-number-of-records-type-log-alert)의 구성에 따라 *Contoso-Log-Alert*라는 로그 경고 규칙이 있다고 가정합시다. 
- Contoso-Log-Alert가 Azure 경고에 의해 실행된 오후 1시 5분에 로그 검색 결과는 임계값 미만인 0개의 레코드를 생성해 경고를 실행하지 않습니다. 
- 다음 반복에서 Contoso-Log-Alert가 Azure 경고에 의해 실행된 오후 1시 10분에 로그 검색 결과는 연결된 [작업 그룹](monitoring-action-groups.md)을 트리거한 직후 임계값을 초과하는 5개의 레코드를 생성해 경고를 실행합니다. 
- Contoso-Log-Alert가 Azure 경고에 의해 실행된 오후 1시 15분에 로그 검색 결과는 연결된 [작업 그룹](monitoring-action-groups.md)을 트리거한 직후 임계값을 초과하는 2개의 레코드를 생성해 경고를 실행합니다.
- 이제 다음 반복에서 Contoso-Log-Alert가 Azure 경고에 의해 실행된 오후 1시 20분에 로그 검색 결과는 임계값 미만인 0개의 레코드를 생성하므로 경고를 실행하지 않습니다.

그러나 위에 나열된 사례에서 Azure 경고는 1시 10분에 확인된 근본 문제가 지속되는지 오후 1시 15분에 확인할 수 없습니다. 새로운 순 오류가 있는 경우에는 사용자가 제공한 쿼리가 이전 레코드를 고려할 수 있으므로 Azure 경고는 확신할 수 있습니다. 따라서 오후 1시 15분에 Contoso-Log-Alert이 실행되면 구성된 [작업 그룹](monitoring-action-groups.md)이 다시 실행됩니다. 이제 아무 레코드도 표시되지 않는 오후 1시 20분에 Azure 경고는 레코드의 원인이 해결되었는지 확신할 수 없습니다. 따라서 Contoso-Log-Alert는 Azure Alert 대시보드 및/또는 경고의 해결을 알리는 알림에서 "해결됨"으로 변경되지 않습니다.


## <a name="pricing-and-billing-of-log-alerts"></a>로그 경고의 가격 책정 및 대금 청구
로그 경고에 적용할 수 있는 가격 책정은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 나와 있습니다. Azure 청구서에서 로그 경고는 다음을 사용하여 `microsoft.insights/scheduledqueryrules` 형식으로 표현됩니다.
- 리소스 그룹 및 경고 속성과 함께 정확한 경고 이름으로 표시된 Application Insights의 로그 경고
- 리소스 그룹 및 경고 속성과 함께 경고 이름을 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`로 표시한 Log Analytics의 로그 경고

    > [!NOTE]
    > Log Analytics API를 사용하여 만든 저장된 모든 검색, 일정 및 작업의 이름은 소문자여야 합니다. `<, >, %, &, \, ?, /` 같이 잘못된 문자가 사용된 경우 청구서에서 `_`로 바꿉니다.

## <a name="next-steps"></a>다음 단계
* [Azure의 로그 경고에서 만들기](alert-log.md)에 대해 알아봅니다.
* [Azure의 로그 경고에서 웹후크](monitor-alerts-unified-log-webhook.md)를 이해합니다.
* [Azure Alerts](monitoring-overview-alerts.md)에 대해 알아봅니다.
* [Application Insights](../application-insights/app-insights-analytics.md)에 대해 자세히 알아봅니다.
* [Log Analytics](../log-analytics/log-analytics-queries.md)에 대해 자세히 알아보기    
