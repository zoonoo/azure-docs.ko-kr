---
title: Azure Monitor에서 로그 경고 문제 해결
description: Azure의 로그 경고 규칙에 대한 일반적인 문제, 오류 및 해결 방법입니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5572c80879584e7f6df650263ae455a134ee4088
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283600"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Azure Monitor에서 로그 경고 문제 해결  

## <a name="overview"></a>개요
이 문서에서는 Azure Monitor 내에서 로그 경고를 설정하는 동안 발생하는 일반적인 문제를 처리합니다. 또한 로그 경고의 기능 또는 구성과 관련하여 자주 묻는 질문에 대한 솔루션을 제공합니다. **로그 경고**라는 용어는 신호가 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-analytics.md)를 기준으로 하는 사용자 지정 쿼리인 경고를 설명합니다. [로그 경고 - 개요](monitor-alerts-unified-log.md)에서 기능, 용어 및 형식에 대해 자세히 알아보세요.

> [!NOTE]
> 이 문서에서는 경고 규칙이 Azure Portal에 트리거된 것으로 표시되고, 연결된 작업 그룹을 통해 알림이 표시되는 경우를 고려하지 않습니다. 이러한 경우 [작업 그룹](monitoring-action-groups.md)의 문서에 있는 자세한 내용을 참조하세요.


## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

다음은 발생할 것으로 예상될 때 [Azure 경고](monitoring-alerts-managing-alert-states.md)에서 보면 [Azure Monitor에서 구성된 로그 경고 규칙](alert-log.md)이 트리거되지 않는 몇 가지 일반적인 이유입니다. 

### <a name="data-ingestion-time-for-logs"></a>로그에 대한 데이터 수집 시간
로그 경고는 [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) 또는 [Application Insights](../application-insights/app-insights-analytics.md)에 기반하여 고객이 제공한 쿼리를 주기적으로 실행하여 작동합니다. 둘 모두는 방대한 양의 로그 데이터를 처리하고 동일한 기능을 제공하는 분석 기능을 통해 구동됩니다. Log Analytics 서비스는 수천 명의 고객과 전 세계의 다양한 원본에서 수 테라바이트의 데이터를 처리하므로 시간 지연에 취약합니다. 자세한 내용은 [Log Analytics의 데이터 수집 시간](../log-analytics/log-analytics-data-ingestion-time.md)을 참조하세요.

Log Analytics 또는 Application Insights 로그에서 발생할 수 있는 데이터 수집 지연을 극복하려면, 로그 경고에서 경고 기간 동안 데이터가 아직 수집되지 않은 것을 확인한 후 잠시 기다렸다가 다시 시도합니다. 로그 경고에는 Log Analytics에서 데이터를 수집하는 데 필요한 시간을 기다릴 수 있도록 기하급수적으로 증가하는 대기 시간이 설정되어 있습니다. 따라서 로그 경고 규칙에 따라 쿼리된 로그가 수집 지연의 영향을 받는 경우, 로그 경고는 Log Analytics 사후 수집에서 데이터를 사용할 수 있고 중간에 여러 번 다시 시도하는 로그 경고 서비스로 인한 지수 시간 간격 후에만 트리거됩니다.

### <a name="incorrect-time-period-configured"></a>잘못된 기간이 구성됨
[로그 경고 용어](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types)의 문서에서 설명한 대로 구성에 지정된 기간은 쿼리의 시간 범위를 지정합니다. 쿼리는 이 시간 범위 내에서 만들어진 레코드만 반환합니다. 기간은 남용을 방지하고 쿼리 로그에 사용되는 시간 명령(ago 등)을 회피하도록 로그 쿼리에 대해 인출된 데이터를 제한합니다. 
*예를 들어 기간을 60분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 로그 쿼리를 실행하기 위해 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다. 이제 로그 쿼리에서 ago (1d)와 같은 명령을 사용하는 경우, 데이터가 지난 60분 동안만 존재하는 것처럼 로그 쿼리는 오후 12시 15분에서 오후 1시 15분 사이의 데이터에 대해서만 실행됩니다. 쿼리 로그에서 지정된 대로 7일 동안의 데이터에 대해서는 실행되지 않습니다.*

쿼리 논리에 따라 구성에 적절한 기간이 제공되었는지 확인합니다. 앞에서 설명한 예의 경우 로그 쿼리에서 녹색 표식과 같이 ago (1d)를 사용하는 경우, 제공된 쿼리가 예상대로 올바르게 실행되도록 하려면 기간을 24시간 또는 1440분(빨간색으로 표시)으로 설정해야 합니다.
    ![기간](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>경고 표시 안 함 옵션이 설정됨
[Azure Portal에서 로그 경고 규칙 만들기](alert-log.md#managing-log-alerts-from-the-azure-portal) 문서의 8단계에서 설명한 대로, 로그 경고는 경고 규칙의 자동 표시 안 함을 구성하고 지정된 시간 동안 알림/트리거를 방지하는 옵션을 제공합니다. 경고 표시 안 함 옵션을 사용하면 **경고 표시 안 함** 옵션에 지정된 시간 동안 작업 그룹을 트리거하지 않고 로그 경고가 실행됩니다. 이에 따라 사용자는 구성된 대로 경고가 실제로 표시되지 않는 동안 발생하지 않았다고 느낄 수 있습니다.
    ![경고 표시 안 함](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>메트릭 측정 경고 규칙이 잘못됨
로그 경고 규칙의 메트릭 측정 유형은 특수 기능을 갖추고 있지만 경고 쿼리 구문에 대한 제한을 사용하는 로그 경고의 하위 유형입니다. 메트릭 측정 로그 경고 규칙은 메트릭 시계열을 제공하기 위해 경고 쿼리의 출력을 필요로 합니다. 이 테이블은 서로 동일한 크기의 기간과 계산된 AggregatedValue의 해당 값을 포함합니다. 또한 사용자는 테이블의 데이터를 정렬할 수 있는 AggregatedValue(예: Computer, Node 등)와 함께 추가 변수를 테이블에 포함하도록 선택할 수 있습니다.

예를 들어 메트릭 측정 로그 경고 규칙이 다음과 같이 구성되었다고 가정합니다.
- 쿼리: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 기간: 6시간
- 임계값: 50
- 경고 논리: 3회 연속 위반
- 다음에 대한 집계: $table로 선택됨

이제부터 명령에 summarize … by를 사용하고 두 변수, 즉 timestamp 및 $table을 제공했습니다. 경고 서비스는 "다음에 대한 집계"에서 $table을 선택합니다. 아래와 같이 기본적으로 $table 필드를 기준으로 결과 테이블을 정렬한 다음, 각 테이블 유형(예: availabilityResults)에 대한 여러 개의 AggregatedValue를 조사하여 3회 이상의 연속 위반이 있었는지 확인합니다.

   ![여러 값을 사용한 메트릭 측정 쿼리 실행](./media/monitor-alerts-unified/LogMMQuery.png)

"다음에 대한 집계"는 $table이므로 $table 열(빨간색으로 표시됨)의 데이터가 정렬됩니다. 그러면 "다음에 대한 집계" 필드, 즉 $table의 형식을 그룹화하고 살펴봅니다. 예를 들어 availabilityResults 값은 하나의 그림/엔터티(주황색으로 강조 표시됨)로 간주됩니다. 이 값 그림/엔터티에서 경고 서비스는 'availabilityResults' 테이블 값에 대해 경고가 트리거되는 3회 연속 위반(녹색으로 표시됨)이 발생했는지 확인합니다. 마찬가지로, $table의 다른 값에 대해 3회 연속 위반이 나타나면 동일한 또 다른 경고 알림이 트리거됩니다. 경보 서비스는 시간별로 하나의 그림/엔터티(주황색으로 표시됨)의 값을 자동으로 정렬합니다.

이제 메트릭 측정 로그 경고 규칙이 수정되었고, 쿼리는 3회 연속 위반에 대한 경고 논리를 포함하여 이전 구성과 동일하게 유지되는 구성의 나머지 부분이 있는 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`였다고 가정합니다. 이 경우 "다음에 대한 집계" 옵션은 기본적으로 timestamp가 됩니다. summarize…by, 즉 timestamp에 대한 쿼리에는 하나의 값만 제공되므로 앞의 예제와 마찬가지로 실행 종료 시의 출력은 아래 그림과 같습니다. 

   ![단일 값을 사용한 메트릭 측정 쿼리 실행](./media/monitor-alerts-unified/LogMMtimestamp.png)

"다음에 대한 집계"는 timestamp이므로 데이터가 timestamp 열(빨간색)의 데이터가 정렬됩니다. 그러면 timestamp를 기준으로 그룹화합니다. 예를 들어 `2018-10-17T06:00:00Z`에 대한 값은 하나의 그림/엔터티(주황색으로 강조 표시됨)로 간주됩니다. 이 값 그림/엔터티에서 경고 서비스는 연속 위반이 발생하지 않으며(각 timestamp 값에 하나의 항목만 있음), 경고가 트리거되지 않습니다. 따라서 이러한 경우 사용자는 다음 중 하나를 수행해야 합니다.
- 구성된 "다음에 대한 집계" 필드를 사용하여 올바르게 정렬하도록 더미 변수 또는 기존 변수(예: $table)를 추가합니다.
- (또는) *총 위반 횟수*를 기반으로 하는 경고 논리를 적절하게 대신 사용하도록 경고 규칙을 재구성합니다.
 
## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함
다음은 발생할 것으로 예상되지 않을 때 [Azure 경고](monitoring-alerts-managing-alert-states.md)에서 보면 [Azure Monitor에서 구성된 로그 경고 규칙](alert-log.md)이 트리거될 수 있는 몇 가지 일반적인 이유입니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨
Log Analytics 및 Application Insights를 구동하는 분석은 수집 지연 및 처리의 영향을 받을 수 있습니다. 이로 인해 제공된 로그 경고 쿼리가 실행될 때 데이터를 사용할 수 없거나 일부 데이터만 사용할 수 있는 경우가 있을 수 있습니다. 자세한 내용은 [Log Analytics의 데이터 수집 시간](../log-analytics/log-analytics-data-ingestion-time.md)을 참조하세요.

경고 규칙이 구성된 방식에 따라 경고 실행 시 로그에 데이터가 없거나 일부가 없는 경우 잘못 발생하는 것일 수 있습니다. 이러한 경우 경고 쿼리 또는 구성을 변경하는 것이 좋습니다. *예를 들어 분석 쿼리의 결과 수가 5보다 적을 때 트리거하도록 로그 경고 규칙이 구성된 경우 데이터가 없거나(0개 레코드) 부분 결과가 없으면(1개 레코드) 경고 규칙이 트리거됩니다. 수집 지연 후 Analytics에서 동일한 쿼리를 실행하면 전체 데이터가 포함된 쿼리에서 결과를 10개의 레코드로 제공할 수 있습니다.*

### <a name="alert-query-output-misunderstood"></a>경고 쿼리 출력이 잘못 이해됨
로그 경고의 경우 사용자는 분석 쿼리를 통해 경고에 대한 논리를 제공합니다. 제공된 분석 쿼리는 다양한 빅 데이터 및 수학 함수를 사용하여 특정 구문을 만들 수 있습니다. 경고 서비스는 지정된 기간 동안 데이터를 사용하여 지정된 간격으로 고객이 제공한 쿼리를 실행합니다. 경고 서비스는 선택한 경고 유형에 따라 제공된 쿼리를 미세하게 변경하며, 아래 그림과 같이 '신호 논리 구성' 화면의 "실행할 쿼리" 섹션에서 동일한 내용을 확인할 수 있습니다. ![실행할 쿼리](./media/monitor-alerts-unified/LogAlertPreview.png)
 
**실행할 쿼리** 섹션에 표시된 내용은 로그 경고 서비스를 실행하는 것입니다. 사용자는 [Analytics 포털](../log-analytics/log-analytics-log-search-portals.md) 또는 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/)를 통해 명시된 쿼리와 시간 간격을 실행할 수 있습니다. 경고를 만들기 전에 이해하려는 경우 어떤 쿼리 출력이 될 수 있습니다.
 
## <a name="next-steps"></a>다음 단계

* [Azure Alerts의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아보기
* [Application Insights](../application-insights/app-insights-analytics.md)에 대해 자세히 알아보기
* [Log Analytics](../log-analytics/log-analytics-queries.md)에 대해 자세히 알아보기 