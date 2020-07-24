---
title: Azure Monitor의 로그 경고
description: Azure Alerts에 대해 지정한 분석 쿼리 조건이 충족될 경우 이메일, 알림, 웹 사이트 URL 호출(웹후크) 또는 자동화가 트리거됩니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 5/31/2019
ms.subservice: alerts
ms.openlocfilehash: 0d080c18a1af9549373750b787093fec03b32006
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073603"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고

로그 경고는 [Azure 경고](../../azure-monitor/platform/alerts-overview.md)에서 지원 되는 경고 유형 중 하나입니다. 로그 경고를 통해 사용자는 경고의 기반으로 Azure 분석 플랫폼을 사용할 수 있습니다.

로그 경고는 [Azure Monitor Logs](../log-query/get-started-portal.md) 또는 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)에 대해 만든 로그 검색 규칙으로 이루어집니다. 로그 경고의 사용법에 대한 자세한 내용은 [Azure에서 로그 경고 만들기](../../azure-monitor/platform/alerts-log.md)를 참조

> [!NOTE]
> [Azure Monitor 로그](../log-query/get-started-portal.md)에서 인기 있는 로그 데이터를 이제 Azure Monitor의 메트릭 플랫폼에서도 사용할 수 있습니다. 세부 정보 보기는 [로그에 대한 메트릭 경고](../../azure-monitor/platform/alerts-metric-logs.md)를 참조하세요.


## <a name="log-search-alert-rule---definition-and-types"></a>로그 검색 경고 규칙 - 정의 및 형식

로그 검색 규칙은 일정한 간격으로 지정된 로그 쿼리를 자동으로 실행하도록 Azure Alerts에 의해 만들어집니다.  로그 쿼리 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다. 그런 다음, 규칙은 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 사용하여 하나 이상의 작업을 자동으로 실행할 수 있습니다. 경고 규칙 또는 경고 쿼리의 분석 대상에 대한 액세스 및 쿼리 실행 권한과 함께 로그 경고를 만들고, 수정하고, 업데이트할 수 있는 [Azure 모니터링 기여자](../../azure-monitor/platform/roles-permissions-security.md) 역할이 필요할 수 있습니다. 사용자를 만드는 경우 경고 규칙 또는 경고 쿼리의 모든 분석 대상에 액세스할 수 없는 경우 규칙 만들기가 실패할 수 있습니다. 그렇지 않으면 로그 경고 규칙이 부분적인 결과로 실행 됩니다.

로그 검색 규칙은 다음 세부 정보에 의해 정의됩니다.

- **로그 쿼리입니다**.  경고 규칙이 실행될 때마다 실행되는 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 트리거할지 여부를 결정하는 데 사용됩니다. Analytics 쿼리는 특정 Log Analytics 작업 영역 또는 Application Insights 앱에 대한 것이거나, 사용자가 모든 리소스에 대한 쿼리 권한으로 잘 액세스할 수 있는 경우 [여러 Log Analytics 및 Application Insights 리소스](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)에 걸쳐 있을 수도 있습니다. 
    > [!IMPORTANT]
    > [SCHEDULEDQUERYRULES API를 사용 하 여 구성 된 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) 에 대 한 Application Insights 및 로그 경고에 대 한 로그 경고에서 [리소스 간 쿼리](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) 를 지원 합니다.

    일부 분석 명령 및 조합은 로그 경고에서 사용하는 것과 호환되지 않습니다. 자세한 내용은 [Azure Monitor의 로그 경고 쿼리](../../azure-monitor/platform/alerts-log-query.md)를 참조하세요.

- **기간**  쿼리에 대한 시간 범위를 지정합니다. 쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다. 기간은 남용을 방지하고 쿼리 로그에 사용되는 시간 명령(ago 등)을 회피하도록 로그 쿼리에 대해 인출된 데이터를 제한합니다. <br>*예를 들어 기간이 60 분으로 설정 되 고 쿼리가 1:15 PM에 실행 되는 경우 12:15 PM과 1:15 PM 사이에 생성 된 레코드만 로그 쿼리를 실행 하기 위해 반환 됩니다. 이제 로그 쿼리가 전 (7d)와 같은 시간 명령을 사용 하면 지난 60 분 동안만 데이터가 존재 하는 것 처럼 12:15 PM 및 1:15 PM 사이의 데이터에 대해서만 로그 쿼리가 실행 됩니다. 로그 쿼리에 지정 된 7 일 동안의 데이터에는 그렇지 않습니다.*

- **빈도**.  쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간 이하여야 합니다.  값이 기간을 초과하는 경우 레코드가 누락될 위험이 있습니다.<br>*예를 들어 30 분의 기간 및 60 분의 빈도를 고려 합니다.  쿼리가 1:00에서 실행 되는 경우 12:30과 1:00 PM 사이의 레코드를 반환 합니다.  다음 번에 쿼리를 실행할 때 1:30과 2:00 간에 레코드를 반환 하는 경우 2:00입니다.  1:00에서 1:30 사이에 만들어진 모든 레코드는 평가 되지 않습니다.*

- **임계값**.  로그 검색 결과를 평가하여 경고를 만들어야 하는지 여부를 결정합니다.  임계값은 다양한 유형의 로그 검색 경고 규칙에 따라 다릅니다.

로그 검색 규칙은 [Azure Monitor Logs](../log-query/get-started-portal.md) 또는 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)에 적용되고 두 가지 형식일 수 있습니다. 이러한 각 유형에 대해서는 다음 섹션에서 자세히 설명합니다.

- **[결과의 수](#number-of-results-alert-rules)** 입니다. 로그 검색에서 반환되는 레코드 수가 지정된 수를 초과할 때 만들어지는 단일 경고입니다.
- **[메트릭 측정](#metric-measurement-alert-rules)**.  로그 검색 결과에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 만들어지는 경고입니다.

경고 규칙 유형 간의 차이점은 다음과 같습니다.

- *결과 수* 경고 규칙은 항상 단일 경고를 만드는 반면, *미터법* 경고 규칙은 임계값을 초과하는 각 개체에 대해 경고를 만듭니다.
- *결과 수* 경고 규칙은 임계값을 한 번 초과하면 경고를 만듭니다. *미터법* 경고 규칙은 특정 시간 간격 동안 임계값이 특정 횟수를 초과하면 경고를 만들 수 있습니다.

### <a name="number-of-results-alert-rules"></a>결과 수 경고 규칙

**결과 수** 경고 규칙은 검색 쿼리에서 반환된 레코드 수가 지정된 임계값을 초과하면 단일 경고를 만듭니다. 이 형식의 경고 규칙은 Windows 이벤트 로그, Syslog, WebApp 응답 및 사용자 지정 로그와 같은 이벤트 작업에 적합합니다.  특정 오류 이벤트가 생성될 때 또는 특정 기간 내에 복수의 오류 이벤트가 생성되는 경우 경고를 만드는 것이 좋습니다.

**임계값**: 결과 수 경고 규칙의 임계값은 특정 값보다 크거나 작습니다.  로그 검색에서 반환된 레코드 수가 이 조건과 일치하면 경고를 만듭니다.

단일 이벤트에 대해 경고하려면 결과 수를 0보다 크게 설정하고, 마지막 쿼리 실행 이후 생성된 단일 이벤트 발생을 확인합니다. 일부 애플리케이션은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 애플리케이션이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 임계값을 **1 보다 작게**설정 합니다.

#### <a name="example-of-number-of-records-type-log-alert"></a>레코드 수 형식 로그 경고의 예제

웹 기반 앱이 코드 500(즉) 내부 서버 오류로 사용자에게 응답하는 경우를 알아야 하는 시나리오를 고려해보세요. 다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

- **쿼리:** requests | resultCode == "500"<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **임계값:** 0 초과<br>

그런 후 경고가 30분 동안의 데이터로 5분 간격으로 쿼리를 실행하여 결과 코드가 500인 레코드를 찾습니다. 이러한 레코드가 하나라도 있으면 경고가 발생하고, 구성된 작업이 트리거됩니다.

### <a name="metric-measurement-alert-rules"></a>미터법 경고 규칙

**미터법** 경고 규칙은 지정 된 임계값 및 지정 된 트리거 조건을 초과 하는 값을 사용 하 여 쿼리에 있는 각 개체에 대 한 경고를 만듭니다. **결과 수** 경고 규칙과 달리 **메트릭 측정** 경고 규칙은 분석 결과에서 시계열을 제공할 때 작동 합니다. **결과 수** 경고 규칙과는 다음과 같이 분명하게 구별됩니다.

- **집계 함수**: 수행되는 계산과 잠재적으로 집계할 숫자 필드를 결정합니다.  예를 들어 **count()** 는 쿼리의 레코드 수를 반환하고, **avg(CounterValue)** 는 해당 간격 동안 CounterValue 필드의 평균을 반환합니다. 쿼리의 집계 함수는 AggregatedValue로 명명되어야 하며 숫자 값을 제공해야 합니다.다. 

- **그룹 필드**: 이 필드의 각 인스턴스에 대해 집계된 값이 있는 레코드가 만들어지며 각각에 대해 경고가 생성될 수 있습니다.  예를 들어 각 컴퓨터에 대해 경고를 생성하려면 **컴퓨터별**을 사용합니다. 경고 쿼리에 여러 개의 그룹 필드가 지정된 경우 사용자는 **Aggregate On**(metricColumn) 매개 변수를 사용하여 결과를 정렬하는 데 사용할 필드를 지정할 수 있습니다.

    > [!NOTE]
    > *Agregate On*(metricColumn) 옵션은 Application Insight에 대한 메트릭 측정값 형식 로그 경고 및 [scheduledQueryRules API를 사용하여 구성된 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md)에 대한 로그 경고에만 사용할 수 있습니다.

- **시간 간격**: 데이터가 집계되는 시간 간격을 정의합니다.  예를 들어 **5분**을 지정한 경우 경고에 대해 지정한 기간 동안 5분 간격으로 집계된 그룹 필드의 각 인스턴스에 대한 레코드가 만들어집니다.

    > [!NOTE]
    > 쿼리에서 bin 함수를 사용하여 간격을 지정해야 합니다. bin()이 결국 같지 않은 시간 간격이 될 수 있으므로, 경고는 런타임 시 적절한 시간을 사용하여 bin 명령을 bin_at 명령으로 자동 변환함으로써 고정 시점이 포함된 결과를 보장합니다. 로그 경고의 메트릭 측정값 형식은 최대 3개의 bin() 명령 인스턴스가 있는 쿼리에서 작동하도록 설계되었습니다.
    
- **임계값**: 미터법 경고 규칙의 임계값은 집계 값 및 위반 횟수로 정의됩니다.  로그 검색에서 데이터 요소가 이 값을 초과하면 위반으로 간주됩니다.  결과의 개체에서 위반 횟수가 지정된 값을 초과하면 해당 개체에 대한 경고가 만들어집니다.

*Aggregate On* 또는 *metricColumn* 옵션이 제대로 구성되지 않으면 경고 규칙이 잘못 실행될 수 있습니다. 자세한 내용은 [메트릭 측정 경고 규칙이 잘못된 경우의 문제 해결](./alerts-troubleshoot-log.md#metric-measurement-alert-rule-is-incorrect)을 참조하세요.

#### <a name="example-of-metric-measurement-type-log-alert"></a>미터법 형식 로그 경고의 예제

컴퓨터에서 30분 동안 90% 프로세서 사용률을 3회 초과하는 경우 경고가 필요한 시나리오를 고려해 보세요.  다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

- **Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **경고 논리-조건 & 임계값:** 90 보다 큼<br>
- **그룹 필드 (집계):** 컴퓨터별
- **경고 트리거 조건:** 총 위반 2 초과<br>

이 쿼리는 각 컴퓨터에 대해 5분 간격으로 평균 값을 만듭니다.  이 쿼리는 이전 30분 동안 수집된 데이터에 대해 5분마다 실행됩니다. 선택한 그룹 필드(Aggregate-on)가 열 형식의 'Computer'이기 때문에 AggregatedValue가 'Computer'의 다양한 값으로 분할되고 각 컴퓨터의 평균 프로세서 사용률이 5분의 시간 bin에 대해 결정됩니다.  3대의 컴퓨터에 대한 샘플 쿼리 결과는 아래와 같습니다.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

쿼리 결과를 플롯으로 표현하면 다음과 같습니다.

![샘플 쿼리 결과](media/alerts-unified-log/metrics-measurement-sample-graph.png)

이 예에서는 3대의 컴퓨터 각각에 대해 5분의 bin으로 5분 동안의 평균 프로세서 사용률이 계산된 것을 볼 수 있습니다. srv01은 1:25 bin에서 임계값 90을 한 번만 위반했습니다. 이에 비해 srv02는 1:10, 1:15 및 1:25 bin에서 임계값 90을 초과했으며 srv03은 1:10, 1:15, 1:20 및 1:30에서 임계값 90을 초과했습니다.
경고는 총 위반 횟수가 2를 초과하는 경우를 기준으로 트리거하도록 구성되었으므로 srv02 및 srv03만 기준을 충족하는 것으로 나타납니다. 따라서 srv02 및 sr-srv03에 대 한 별도의 경고가 생성 됩니다 .이는 여러 시간 bin에서 90% 임계값을 두 번 위반 하기 때문  다음을 *기반으로 하는 트리거 경고:* 매개 변수가 *연속 위반* 옵션에 대해 대신 구성 된 경우 1:20 1:10 sr-srv03에 대해서 **만** 경고가 발생 합니다. srv02는 1:10 ~ 1:15 범위에서 2번 연속 시간 bin에 대해 임계값을 위반했으므로 경고가 발생하지 **않습니다**.

## <a name="log-search-alert-rule---firing-and-state"></a>로그 검색 경고 규칙 - 실행 및 상태

로그 검색 경고 규칙은 쿼리에 작성 하는 논리에 대해서만 작동 합니다. 경고 시스템에는 시스템 상태, 의도 또는 쿼리에 포함 된 근본 원인의 다른 컨텍스트가 없습니다. 따라서 로그 경고는 상태 없음 이라고 합니다. 조건은 실행할 때마다 "TRUE" 또는 "FALSE"로 평가 됩니다.  경고 조건에 대 한 평가가 이전에 발생 하는 것과 관계 없이 "TRUE" 일 때마다 경고가 발생 합니다.    

실제 예제에서이 동작을 살펴보겠습니다. [결과 유형에 대 한](#example-of-number-of-records-type-log-alert)로그 경고를 제공 하는 예제에 표시 된 것 처럼 구성 된 *Contoso-log-alert*라는 로그 경고 규칙이 있다고 가정 합니다. 조건은 로그에서 500 결과 코드를 찾기 위해 설계 된 사용자 지정 경고 쿼리입니다. 로그에서 500 결과 코드를 하나 이상 찾은 경우 경고의 조건은 true입니다. 

아래 간격 마다 Azure alerts 시스템은 *Contoso-로그 경고*의 조건을 평가 합니다.


| Time    | 로그 검색 쿼리에서 반환 된 레코드의 개수 | 로그 조건 평가 | 결과 
| ------- | ----------| ----------| ------- 
| 오후 1:05 | 0 개 레코드 | 0은 0 > 하지 않으므로 FALSE입니다. |  경고가 발생 하지 않습니다. 작업은 호출 되지 않습니다.
| 오후 1:10 | 2 개 레코드 | 2 > 0)  | 경고가 발생 하 고 라는 동작 그룹이 있습니다. 경고 상태가 활성입니다.
| 오후 1:15 | 5 개 레코드 | 5 > 0  | 경고가 발생 하 고 라는 동작 그룹이 있습니다. 경고 상태가 활성입니다.
| 오후 1:20 | 0 개 레코드 | 0은 0 > 하지 않으므로 FALSE입니다. |  경고가 발생 하지 않습니다. 작업은 호출 되지 않습니다. 경고 상태가 활성 상태로 남아 있습니다.

위의 경우를 예로 사용 합니다.

오후 1:15 시에 Azure 경고는 1:10에 표시 되는 기본 문제를 확인 하 고, 레코드가 net new 오류 인지, 아니면 오후 10 시에 이전 실패를 반복 하는지를 확인할 수 없습니다. 사용자가 제공한 쿼리가 이전 레코드를 고려 중이거나 시스템이 인식 하지 못할 수 있습니다. Azure alerts 시스템은 주의 하 여 오류를 발생 시키고 경고와 연결 된 작업을 다시 1:15 PM에 실행 합니다. 

오후 1:20 시 500 결과 코드를 사용 하 여 레코드 0이 표시 되는 경우 Azure 경고는 1:10 PM 및 1:15 PM에 표시 된 500 결과 코드의 원인이 이제 해결 되었는지 확인할 수 없습니다. 동일한 이유로 다시 500 오류 문제가 발생 하는지 알 수 없습니다. 따라서 *Contoso-로그 경고* 는 Azure 경고 대시보드에서 **해결 됨** 으로 변경 되지 않으며 경고가 해결 되었다는 알림이 전송 되지 않습니다. 분석 쿼리에 포함 된 논리의 정확한 조건이 나 원인을 이해 하는 사용자만이 경고를 필요에 따라 [닫힘으로 표시할](alerts-managing-alert-states.md) 수 있습니다.

## <a name="pricing-and-billing-of-log-alerts"></a>로그 경고의 가격 책정 및 대금 청구

로그 경고에 적용할 수 있는 가격 책정은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 나와 있습니다. Azure 청구서에서 로그 경고는 다음을 사용하여 `microsoft.insights/scheduledqueryrules` 형식으로 표현됩니다.

- 리소스 그룹 및 경고 속성과 함께 정확한 경고 이름으로 표시된 Application Insights의 로그 경고
- 리소스 그룹 및 경고 속성과 함께 정확한 경고 이름으로 표시된 Log Analytics의 경고 로그([scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)를 사용하여 만든 경우)

[레거시 Log Analytics API](../../azure-monitor/platform/api-alerts.md)에는 적절한 [Azure 리소스](../../azure-resource-manager/management/overview.md)가 아닌 Log Analytics 저장된 검색의 일부로 경고 작업 및 일정이 있습니다. 따라서 [새 api로 전환](../../azure-monitor/platform/alerts-log-api-switch.md) 하거나 [레거시 Log Analytics api](../../azure-monitor/platform/api-alerts.md) 를 사용 **하지 않고** Azure Portal를 사용 하 여 Log Analytics에 대해 만들어진 이러한 레거시 로그 경고에 대 한 청구를 사용 하도록 설정 하려면 `microsoft.insights/scheduledqueryrules` Azure에서 청구 하기 위해에서 숨겨진 의사 경고 규칙을 만듭니다. `microsoft.insights/scheduledqueryrules`에서 청구를 위해 생성된 숨겨진 의사 경고 규칙은 리소스 그룹 및 경고 속성과 함께 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`로 표시됩니다.

> [!NOTE]
> `<, >, %, &, \, ?, /`와 같은 잘못된 문자가 있으면 숨겨진 의사 경고 규칙 이름에서 `_`로 바뀌기 때문에 Azure 청구서에서도 그렇게 바뀝니다.

[레거시 Log Analytics API](api-alerts.md)를 사용하여 경고 규칙에 대한 청구를 위해 생성된 숨겨진 scheduleQueryRules 리소스를 제거하려면 사용자가 다음 중 하나를 수행하면 됩니다.

- 사용자가 [Log Analytics 작업 영역에서 경고 규칙에 대한 API 기본 설정을 전환](../../azure-monitor/platform/alerts-log-api-switch.md)할 수 있으며 경고 규칙이 손실되거나 Azure Resource Manager 호환 [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)로 모니터링이 이동하지 않습니다. 따라서 청구를 위해 숨겨진 의사 경고 규칙을 만들 필요가 없습니다.
- API 기본 설정을 전환하지 않으려면 사용자가 [레거시 Log Analytics API](api-alerts.md)를 사용하여 원래 일정과 경고 작업을 **삭제**하거나 [Azure Portal에서 원래 로그 경고 규칙](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)을 제거해야 합니다.

또한 [레거시 LOG ANALYTICS API](api-alerts.md)를 사용 하 여 경고 규칙을 청구 하기 위해 만든 숨겨진 scheduleQueryRules 리소스의 경우 PUT과 같은 수정 작업은 실패 합니다. `microsoft.insights/scheduledqueryrules`형식 의사 규칙은 [레거시 Log Analytics API](api-alerts.md)를 사용 하 여 만든 경고 규칙의 요금을 청구 하기 위한 것입니다. 모든 경고 규칙 수정은 [레거시 LOG ANALYTICS api](api-alerts.md) 를 사용 하 여 수행 해야 합니다. 또는 사용자가 대신 [scheduledQueryRules api](/rest/api/monitor/scheduledqueryrules) 를 사용 하도록 [경고 규칙에 대 한 API 기본 설정을 전환할](../../azure-monitor/platform/alerts-log-api-switch.md) 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure의 로그 경고에서 만들기](../../azure-monitor/platform/alerts-log.md)에 대해 알아봅니다.
* [Azure의 로그 경고에서 웹후크](alerts-log-webhook.md)를 이해합니다.
* [Azure Alerts](../../azure-monitor/platform/alerts-overview.md)에 대해 알아봅니다.
* [Application Insights](../log-query/log-query-overview.md)에 대해 자세히 알아봅니다.
* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)에 대해 자세히 알아보세요.
