---
title: Azure Monitor의 로그 경고
description: Azure Alerts에 대해 지정한 분석 쿼리 조건이 충족될 경우 이메일, 알림, 웹 사이트 URL 호출(웹후크) 또는 자동화가 트리거됩니다.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427422"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Monitor의 로그 경고

이 문서에서는 [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) 내에서 지원되는 경고 형식 중 하나이며 사용자가 Azure의 분석 플랫폼을 경고의 기준으로 사용하게 하는 로그 경고의 세부 정보를 제공합니다.

로그 경고는 [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) 또는 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)에 대해 만든 로그 검색 규칙으로 이루어집니다. 로그 경고의 사용법에 대한 자세한 내용은 [Azure에서 로그 경고 만들기](../../azure-monitor/platform/alerts-log.md)를 참조

> [!NOTE]
> [Azure Monitor 로그](../../azure-monitor/learn/tutorial-viewdata.md)에서 인기 있는 로그 데이터를 이제 Azure Monitor의 메트릭 플랫폼에서도 사용할 수 있습니다. 세부 정보 보기는 [로그에 대한 메트릭 경고](../../azure-monitor/platform/alerts-metric-logs.md)를 참조하세요.


## <a name="log-search-alert-rule---definition-and-types"></a>로그 검색 경고 규칙 - 정의 및 형식

로그 검색 규칙은 일정한 간격으로 지정된 로그 쿼리를 자동으로 실행하도록 Azure Alerts에 의해 만들어집니다.  로그 쿼리 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다. 그런 다음, 규칙은 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 사용하여 하나 이상의 작업을 자동으로 실행할 수 있습니다. 경고 규칙 또는 경고 쿼리의 분석 대상에 대한 액세스 및 쿼리 실행 권한과 함께 로그 경고를 만들고, 수정하고, 업데이트할 수 있는 [Azure 모니터링 기여자](../../azure-monitor/platform/roles-permissions-security.md) 역할이 필요할 수 있습니다. 만든 사용자 경고 규칙의 경고 쿼리-모든 분석 대상에 액세스할 수 없는 경우 규칙 만들기가 실패할 수 있습니다 하거나 부분 결과 사용 하 여 로그 경고 규칙이 실행 됩니다.

로그 검색 규칙은 다음 세부 정보에 의해 정의됩니다.

- **로그 쿼리**  경고 규칙이 실행될 때마다 실행되는 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 트리거할지 여부를 결정하는 데 사용됩니다. Analytics 쿼리는 특정 Log Analytics 작업 영역 또는 Application Insights 앱에 대한 것이거나, 사용자가 모든 리소스에 대한 쿼리 권한으로 잘 액세스할 수 있는 경우 [여러 Log Analytics 및 Application Insights 리소스](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights)에 걸쳐 있을 수도 있습니다. 
    > [!IMPORTANT]
    > [리소스 간 쿼리](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) Application Insights 및 로그 경고에 대 한 로그 경고에 대 한 지원 [scheduledQueryRules API를 사용 하 여 구성 하는 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) 만 합니다.

    일부 분석 명령 및 조합은 로그 경고에서 사용하는 것과 호환되지 않습니다. 자세한 내용은 [Azure Monitor의 로그 경고 쿼리](../../azure-monitor/platform/alerts-log-query.md)를 참조하세요.

- **기간**  쿼리에 대한 시간 범위를 지정합니다. 쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다. 기간은 남용을 방지하고 쿼리 로그에 사용되는 시간 명령(ago 등)을 회피하도록 로그 쿼리에 대해 인출된 데이터를 제한합니다. <br>*예를 들어 기간을 60분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 로그 쿼리를 실행하기 위해 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다. 이제 로그 쿼리가 ago(7d)와 같은 명령을 사용하는 경우 데이터가 지난 60분 동안만 존재하는 것처럼 로그 쿼리는 오후 12시 15분에서 1시 15분 사이의 데이터에 대해서만 실행됩니다. 쿼리 로그에서 지정된 대로 7일 동안의 데이터에 대해서는 실행되지 않습니다.*

- **빈도**.  쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간 이하여야 합니다.  값이 기간을 초과하는 경우 레코드가 누락될 위험이 있습니다.<br>*예를 들어 30분의 기간과 60분의 빈도를 사용하는 것이 좋습니다.  쿼리가 1:00에 실행되면 오후 12:30 및 1:00 사이의 레코드를 반환합니다.  1:30 및 2:00 사이의 레코드를 반환하게 된다면 다음으로 쿼리가 실행되는 시간은 2:00입니다.  1:00 및 1:30 사이에 생성된 레코드는 평가되지 않습니다.*

- **임계값**.  로그 검색 결과를 평가하여 경고를 만들어야 하는지 여부를 결정합니다.  임계값은 다양한 유형의 로그 검색 경고 규칙에 따라 다릅니다.

로그 검색 규칙은 [Azure Monitor Logs](../../azure-monitor/learn/tutorial-viewdata.md) 또는 [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)에 적용되고 두 가지 형식일 수 있습니다. 이러한 각 유형에 대해서는 다음 섹션에서 자세히 설명합니다.

- **[결과 수](#number-of-results-alert-rules)** - 로그 검색에서 반환되는 레코드 수가 지정된 수를 초과할 때 만들어지는 단일 경고입니다.
- **[미터법](#metric-measurement-alert-rules)** -  로그 검색 결과에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 만들어지는 경고입니다.

경고 규칙 유형 간의 차이점은 다음과 같습니다.

- *결과 수* 경고 규칙은 항상 단일 경고를 만드는 반면, *미터법* 경고 규칙은 임계값을 초과하는 각 개체에 대해 경고를 만듭니다.
- *결과 수* 경고 규칙은 임계값을 한 번 초과하면 경고를 만듭니다. *미터법* 경고 규칙은 특정 시간 간격 동안 임계값이 특정 횟수를 초과하면 경고를 만들 수 있습니다.

### <a name="number-of-results-alert-rules"></a>결과 수 경고 규칙

**결과 수** 경고 규칙은 검색 쿼리에서 반환된 레코드 수가 지정된 임계값을 초과하면 단일 경고를 만듭니다. 이 형식의 경고 규칙은 Windows 이벤트 로그, Syslog, WebApp 응답 및 사용자 지정 로그와 같은 이벤트 작업에 적합합니다.  특정 오류 이벤트가 생성될 때 또는 특정 기간 내에 복수의 오류 이벤트가 생성되는 경우 경고를 만드는 것이 좋습니다.

**임계값**: 결과 수 경고 규칙의 임계값은 특정 값보다 크거나 작습니다.  로그 검색에서 반환된 레코드 수가 이 조건과 일치하면 경고를 만듭니다.

단일 이벤트에 대해 경고하려면 결과 수를 0보다 크게 설정하고, 마지막 쿼리 실행 이후 생성된 단일 이벤트 발생을 확인합니다. 일부 애플리케이션은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 애플리케이션이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 임계값을 **1보다 작게**로 설정할 수 있습니다.

#### <a name="example-of-number-of-records-type-log-alert"></a>레코드 수 형식 로그 경고의 예제

웹 기반 앱이 코드 500(즉) 내부 서버 오류로 사용자에게 응답하는 경우를 알아야 하는 시나리오를 고려해보세요. 다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

- **쿼리:** requests | resultCode == "500"<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **임계값:** 0 초과<br>

그런 후 경고가 30분 동안의 데이터로 5분 간격으로 쿼리를 실행하여 결과 코드가 500인 레코드를 찾습니다. 이러한 레코드가 하나라도 있으면 경고가 발생하고, 구성된 작업이 트리거됩니다.

### <a name="metric-measurement-alert-rules"></a>미터법 경고 규칙

**미터법** 경고 규칙에 지정 된 임계값 초과 및 트리거 조건을 지정 하는 값을 사용 하 여 쿼리를 각 개체에 대 한 경고를 만듭니다. 와 달리 **결과 수가** 경고 규칙을 **미터법** 분석 결과 시계열을 제공 하는 경우 경고 규칙이 작동 합니다. **결과 수** 경고 규칙과는 다음과 같이 분명하게 구별됩니다.

- **집계 함수**: 수행되는 계산과 잠재적으로 집계할 숫자 필드를 결정합니다.  예를 들어 **count()** 는 쿼리의 레코드 수를 반환하고, **avg(CounterValue)** 는 해당 간격 동안 CounterValue 필드의 평균을 반환합니다. 쿼리의 집계 함수는 AggregatedValue로 명명되고 숫자 값을 제공해야 합니다. 

- **그룹 필드**: 이 필드의 각 인스턴스에 대해 집계된 값이 있는 레코드가 만들어지며 각각에 대해 경고가 생성될 수 있습니다.  예를 들어 각 컴퓨터에 대해 경고를 생성하려면 **컴퓨터별**을 사용합니다. 경고 쿼리에 여러 개의 그룹 필드가 지정된 경우 사용자는 **Aggregate On**(metricColumn) 매개 변수를 사용하여 결과를 정렬하는 데 사용할 필드를 지정할 수 있습니다.

    > [!NOTE]
    > *Agregate On*(metricColumn) 옵션은 Application Insight에 대한 메트릭 측정값 형식 로그 경고 및 [scheduledQueryRules API를 사용하여 구성된 Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md)에 대한 로그 경고에만 사용할 수 있습니다.

- **간격**:  데이터가 집계되는 시간 간격을 정의합니다.  예를 들어 **5분**을 지정한 경우 경고에 대해 지정한 기간 동안 5분 간격으로 집계된 그룹 필드의 각 인스턴스에 대한 레코드가 만들어집니다.

    > [!NOTE]
    > 쿼리에서 bin 함수를 사용하여 간격을 지정해야 합니다. bin()이 결국 같지 않은 시간 간격이 될 수 있으므로, 경고는 런타임 시 적절한 시간을 사용하여 bin 명령을 bin_at 명령으로 자동 변환함으로써 고정 시점이 포함된 결과를 보장합니다. 로그 경고의 메트릭 측정값 형식은 최대 3개의 bin() 명령 인스턴스가 있는 쿼리에서 작동하도록 설계되었습니다.
    
- **임계값**: 미터법 경고 규칙의 임계값은 집계 값 및 위반 횟수로 정의됩니다.  로그 검색에서 데이터 요소가 이 값을 초과하면 위반으로 간주됩니다.  결과의 개체에서 위반 횟수가 지정된 값을 초과하면 해당 개체에 대한 경고가 만들어집니다.

*Aggregate On* 또는 *metricColumn* 옵션이 제대로 구성되지 않으면 경고 규칙이 잘못 실행될 수 있습니다. 자세한 내용은 [메트릭 측정 경고 규칙이 잘못된 경우의 문제 해결](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect)을 참조하세요.

#### <a name="example-of-metric-measurement-type-log-alert"></a>미터법 형식 로그 경고의 예제

컴퓨터에서 30분 동안 90% 프로세서 사용률을 3회 초과하는 경우 경고가 필요한 시나리오를 고려해 보세요.  다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

- **쿼리:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer<br>
- **기간:** 30분<br>
- **경고 빈도:** 5분<br>
- **경고 논리 - 조건 및 임계값:** 90 초과<br>
- **그룹 필드(Aggregate-on):** Computer
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
경고는 총 위반 횟수가 2를 초과하는 경우를 기준으로 트리거하도록 구성되었으므로 srv02 및 srv03만 기준을 충족하는 것으로 나타납니다. 따라서 srv02와 srv03은 여러 시간 bin에서 90% 임계값을 2회 위반했기 때문에 별도의 경고가 만들어집니다.  *Trigger alert based on:* 매개 변수가 *Continuous breaches* 옵션으로 대신 구성된 경우에는 srv03에 대해서**만** 경고가 발생합니다. 1:10 ~ 1:20 범위에서 3번 연속으로 임계값을 위반했기 때문입니다. srv02는 1:10 ~ 1:15 범위에서 2번 연속 시간 bin에 대해 임계값을 위반했으므로 경고가 발생하지 **않습니다**.

## <a name="log-search-alert-rule---firing-and-state"></a>로그 검색 경고 규칙 - 실행 및 상태

로그 검색 경고 규칙은 구성 및 사용 된 사용자 지정 분석 쿼리에 따라 사용자가 작성한 논리에서 작동합니다. 모니터링 논리 이후 설명 이유는 경고 규칙을 트리거할지 또는 정확한 조건을 비롯 한 각 로그 경고 규칙에서 다를 수 있는 분석 쿼리에서-에 캡슐화 됩니다. Azure 경고에 기본 특정 근본 원인 (또는) 로그 검색 경고 규칙의 임계값 조건을 충족 되었거나 초과 시 계산 중 이었던 시나리오의 부족 한 정보입니다. 따라서 로그 경고 상태 없이 이라고 합니다. 및 제공 하는 사용자 지정 분석 쿼리의 결과로 경고 조건이 충족 될 만큼 로그 경고 규칙 발생 유지 됩니다. 경고 없이 모든 시작으로, 해결 사용자가 제공한 분석 쿼리 내에서 실패를 모니터링 합니다. 정확한 근본 원인의 논리는 마스크입니다. 현재을 최종적으로 근본 원인을 해결 하는 추론할 수 있다면 Azure Monitor 경고에 대 한 메커니즘이 되 고 있습니다.

실제 예제를 사용 하 여 동일한 할 수 있습니다. 호출 로그 경고 규칙을 가정해 *Contoso-로그-경고*에서 구성을 기준으로 합니다 [수의 결과 형식 로그 경고에 대 한 제공 된 예제](#example-of-number-of-records-type-log-alert) 500을 찾도록 설계 된 사용자 지정 경고 쿼리의- 로그의 결과 코드입니다.

- 오후 1 시 05 분 Contoso-로그-경고가 Azure 경고에 의해 실행 된 시간, 로그 검색 결과 500 결과 코드를 사용 하 여 0 개 레코드를 생성 합니다. 임계값 및 경고 아래 0 이므로 발생 하지 않습니다.
- 오후 1 시 10 분 Contoso-로그-경고가 Azure 경고에 의해 실행 된 시간을 다음 반복에서 로그 검색 결과 500으로 결과 코드를 사용 하 여 5 개의 레코드를 제공 합니다. 5 개의 임계값을 초과 하 고 관련된 작업을 사용 하 여 경고 발생 하므로 트리거됩니다.
- 오후 1 시 15 분 Contoso-로그-경고가 Azure 경고에 의해 실행 된 시간, 로그 검색 결과 500 결과 코드를 사용 하 여 두 개의 레코드를 제공 합니다. 두 임계값을 초과 하 고 관련된 작업을 사용 하 여 경고 발생 하므로 트리거됩니다.
- 이제 오후 1 시 20 Contoso-로그-경고가 Azure 경고에 의해 실행 된 시간을 다음 반복에서 로그 검색 결과 500 결과 코드를 사용 하 여 0 개 레코드 다시를 제공 합니다. 임계값 및 경고 아래 0 이므로 발생 하지 않습니다.

하지만 위에 나열 된 경우 오후 1 시 15 분-Azure 경고를 확인할 수 없습니다 확인 1시 10분 시간 기본 문제를 유지 하 고 net 새로운 오류 인지 합니다. 사용자가 제공한 쿼리를 이전 레코드 고려 될 수 있습니다 Azure 경고 있는지 될 수 있습니다. 경고에 대 한 논리는 경고 쿼리에서에 캡슐화 되어 있으므로 따라서 오후 1 시 15 분 표시 500 결과 코드를 사용 하 여 두 레코드 수도 있고 오후 1 시 10 분 이미 표시 되지 않을 수 있습니다. 따라서 Contoso-로그-경고 1:15 PM에 실행 될 때에 주의 기울여에 구성 된 작업을 트리거합니다 다시 합니다. 이제 오후 1 시 20 경우 레코드 0 개 표시 되 500 결과 코드를 사용 하 여 Azure 경고를 확신할 수 없습니다는 오후 1 시 10 분 및 1:15 PM 500 결과 코드의 원인이 끝났습니다를 Azure Monitor 경고 자신을 추론할 수 500 오류 문제가 발생 하지 않습니다 동일한 이유로 s 다시 합니다. 따라서 Contoso-로그-경고에서 Azure 경고 대시보드 및/또는 전송 되었다는 경고를 해결 하는 알림이 "해결 됨"로 변경 되지 것입니다. 대신 정확한 조건을 또는 분석 쿼리에 포함 된 논리에 대 한 이유를 이해 하 고 사용자 수 [닫힘으로 경고 표시](alerts-managing-alert-states.md) 필요에 따라 합니다.

## <a name="pricing-and-billing-of-log-alerts"></a>로그 경고의 가격 책정 및 대금 청구

로그 경고에 적용할 수 있는 가격 책정은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 나와 있습니다. Azure 청구서에서 로그 경고는 다음을 사용하여 `microsoft.insights/scheduledqueryrules` 형식으로 표현됩니다.

- 리소스 그룹 및 경고 속성과 함께 정확한 경고 이름으로 표시된 Application Insights의 로그 경고
- 리소스 그룹 및 경고 속성과 함께 정확한 경고 이름으로 표시된 Log Analytics의 경고 로그([scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하여 만든 경우)

[레거시 Log Analytics API](../../azure-monitor/platform/api-alerts.md)에는 적절한 [Azure 리소스](../../azure-resource-manager/resource-group-overview.md)가 아닌 Log Analytics 저장된 검색의 일부로 경고 작업 및 일정이 있습니다. 따라서 [새로운 API로 전환](../../azure-monitor/platform/alerts-log-api-switch.md)하지 **않고** 또는 [레거시 Log Analytics API](../../azure-monitor/platform/api-alerts.md)를 통해 Azure Portal을 사용하여 Log Analytics용으로 생성된 레거시 로그 경고에 대한 청구가 가능하도록 - Azure에서 청구를 위해 `microsoft.insights/scheduledqueryrules`에 숨겨진 의사 경고 규칙이 만들어집니다. `microsoft.insights/scheduledqueryrules`에서 청구를 위해 생성된 숨겨진 의사 경고 규칙은 리소스 그룹 및 경고 속성과 함께 `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>`로 표시됩니다.

> [!NOTE]
> `<, >, %, &, \, ?, /`와 같은 잘못된 문자가 있으면 숨겨진 의사 경고 규칙 이름에서 `_`로 바뀌기 때문에 Azure 청구서에서도 그렇게 바뀝니다.

[레거시 Log Analytics API](api-alerts.md)를 사용하여 경고 규칙에 대한 청구를 위해 생성된 숨겨진 scheduleQueryRules 리소스를 제거하려면 사용자가 다음 중 하나를 수행하면 됩니다.

- 사용자가 [Log Analytics 작업 영역에서 경고 규칙에 대한 API 기본 설정을 전환](../../azure-monitor/platform/alerts-log-api-switch.md)할 수 있으며 경고 규칙이 손실되거나 Azure Resource Manager 호환 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)로 모니터링이 이동하지 않습니다. 따라서 청구를 위해 숨겨진 의사 경고 규칙을 만들 필요가 없습니다.
- API 기본 설정을 전환하지 않으려면 사용자가 [레거시 Log Analytics API](api-alerts.md)를 사용하여 원래 일정과 경고 작업을 **삭제**하거나 [Azure Portal에서 원래 로그 경고 규칙](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)을 제거해야 합니다.

또한 사용 하 여 경고 규칙의 요금 청구에 대 한 생성 된 숨겨진된 scheduleQueryRules 리소스에 대 한 [레거시 Log Analytics API](api-alerts.md), PUT 실패와 같은 수정 작업을 수행 합니다. 로 `microsoft.insights/scheduledqueryrules` 청구를 사용 하 여 만든 경고 규칙의 용도 대 한 형식 의사 규칙은 [레거시 Log Analytics API](api-alerts.md)합니다. 경고 규칙 수정 해야 사용 하 여 [레거시 Log Analytics API](api-alerts.md) (또는) 할 수 있습니다 [경고 규칙에 대 한 API 기본 설정 전환](../../azure-monitor/platform/alerts-log-api-switch.md) 사용 하도록 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) 대신 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure의 로그 경고에서 만들기](../../azure-monitor/platform/alerts-log.md)에 대해 알아봅니다.
* [Azure의 로그 경고에서 웹후크](alerts-log-webhook.md)를 이해합니다.
* [Azure Alerts](../../azure-monitor/platform/alerts-overview.md)에 대해 알아봅니다.
* [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아봅니다.
* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)에 대해 자세히 알아보기