---
title: "Azure Monitor의 로그 경고 - 경고(미리 보기) | Microsoft Docs"
description: "Azure Alerts(미리 보기)에 대한 지정한 복잡한 쿼리 조건이 충족될 경우 이메일, 알림, 웹 사이트 URL 호출(webhook) 또는 자동화를 트리거합니다."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: f6072e4e8a9ab72f677c35e498e31b5218579f1b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Azure Monitor의 로그 경고 - 경고(미리 보기)
이 문서에서는 Azure Alerts(미리 보기)에서 Analytics 쿼리의 경고 규칙이 작동하는 방법 및 다양한 종류의 로그 경고 규칙 간의 차이점에 대해 자세히 설명합니다.

현재 Azure Alerts(미리 보기)는 [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md)와 [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)에서 쿼리에 대한 로그 경고를 지원합니다.

> [!WARNING]

> 현재 Azure Alerts(미리 보기)의 로그 경고는 작업 영역 간 또는 앱 간 쿼리를 지원하지 않습니다.

## <a name="log-alert-rules"></a>로그 경고 규칙

경고는 일정한 간격으로 로그 쿼리를 자동으로 실행하는 Azure Alerts(미리 보기)에 의해 만들어집니다.  로그 쿼리 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다. 그런 다음 규칙에 따라 하나 이상의 작업이 자동으로 실행되어 경고를 미리 알리거나 [작업 그룹](monitoring-action-groups.md)을 사용하여 Runbook 실행과 같은 다른 프로세스를 호출할 수 있습니다.  이 분석을 수행하기 위해 다양한 유형의 경고 규칙에서 별도의 논리를 사용합니다.

경고 규칙은 다음 세부 정보에 의해 정의됩니다.

- **로그 쿼리**  경고 규칙이 실행될 때마다 실행되는 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 생성할지 여부를 결정하는 데 사용됩니다.
- **기간**.  쿼리에 대한 시간 범위를 지정합니다.  쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다.  시간 창은 5분에서 1440분 또는 24시간 사이의 임의의 값일 수 있습니다. 예를 들어 기간을 60분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 생성됩니다.
- **빈도**.  쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간보다 작거나 같아야 합니다.  값이 기간보다 큰 경우 레코드가 누락될 위험이 있습니다.<br>예를 들어 30분의 기간과 60분의 빈도를 사용하는 것이 좋습니다.  쿼리가 1:00에 실행되면 오후 12:30 및 1:00 사이의 레코드를 반환합니다.  1:30 및 2:00 사이의 레코드를 반환하게 된다면 다음으로 쿼리가 실행되는 시간은 2:00입니다.  1:00 및 1:30 사이에 생성된 레코드는 평가되지 않습니다.
- **임계값**.  로그 검색 결과를 평가하여 경고를 만들어야 하는지 여부를 결정합니다.  임계값은 다양한 유형의 경고 규칙에 따라 다릅니다.

Log Analytics의 각 경고 규칙은 두 가지 형식 중 하나입니다.  이러한 각 유형에 대해서는 다음 섹션에서 자세히 설명합니다.

- **[결과 수](#number-of-results-alert-rules)** - 로그 검색에서 반환되는 레코드 수가 지정된 수를 초과할 때 만들어지는 단일 경고입니다.
- **[미터법](#metric-measurement-alert-rules)** -  로그 검색 결과에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 만들어지는 경고입니다.

경고 규칙 유형 간의 차이점은 다음과 같습니다.

- **결과 수** 경고 규칙은 항상 단일 경고를 만드는 반면, **미터법** 경고 규칙은 임계값을 초과하는 각 개체에 대해 경고를 만듭니다.
- **결과 수** 경고 규칙은 임계값을 한 번 초과하면 경고를 만듭니다. **미터법** 경고 규칙은 특정 시간 간격 동안 임계값이 특정 횟수를 초과하면 경고를 만들 수 있습니다.

## <a name="number-of-results-alert-rules"></a>결과 수 경고 규칙
**결과 수** 경고 규칙은 검색 쿼리에서 반환된 레코드 수가 지정된 임계값을 초과하면 단일 경고를 만듭니다.

**임계값**: **결과 수** 경고 규칙의 임계값은 특정 값보다 크거나 작습니다.  로그 검색에서 반환된 레코드 수가 이 조건과 일치하면 경고를 만듭니다.

### <a name="scenarios"></a>시나리오

#### <a name="events"></a>이벤트
이 형식의 경고 규칙은 Windows 이벤트 로그, Syslog 및 사용자 지정 로그와 같은 이벤트 작업에 적합합니다.  특정 오류 이벤트가 생성될 때 또는 특정 기간 내에 복수의 오류 이벤트가 생성되는 경우 경고를 만드는 것이 좋습니다.

단일 이벤트에 대해 경고하려면 결과 수를 0보다 큰 수로 설정하고 빈도와 기간을 모두 5분으로 설정합니다.  그러면 5분마다 쿼리를 실행하고 마지막 쿼리 실행 이후 생성된 단일 이벤트 발생을 확인합니다.  빈도가 더 길면 수집 중인 이벤트와 생성 중인 경고 사이의 시간이 지연될 수 있습니다.

일부 응용 프로그램은 경고를 발생시키지 않아야 할 가끔씩 발생하는 오류를 기록할 수 있습니다.  예를 들어 응용 프로그램이 오류 이벤트를 생성한 프로세스를 재시도한 후 다음에 성공할 수 있습니다.  이 경우 특정 기간 내에 복수의 이벤트가 생성되지 않은 한 경고를 만들지 않는 것이 좋습니다.  

경우에 따라 이벤트가 없을 때 경고를 만드는 것이 좋습니다.  예를 들어 프로세스에서 일반 이벤트가 올바르게 작동 중임을 나타내기 위해 일반 이벤트를 기록할 수 있습니다.  특정 기간 내에 이러한 이벤트 중 하나가 기록되지 않으면 경고를 만들어야 합니다.  이 경우 임계값을 **1보다 작게**로 설정할 수 있습니다.

## <a name="metric-measurement-alert-rules"></a>미터법 경고 규칙

**미터법** 경고 규칙은 쿼리에서 지정된 임계값을 초과하는 값을 포함한 각 개체에 대해 경고를 만듭니다.  **결과 수** 경고 규칙과는 다음과 같이 분명하게 구별됩니다.

**집계 함수**: 수행되는 계산과 잠재적으로 집계할 숫자 필드를 결정합니다.  예를 들어 **count()**는 쿼리의 레코드 수를 반환하고, **avg(CounterValue)**는 해당 간격 동안 CounterValue 필드의 평균을 반환합니다.

> [!NOTE]

> 쿼리의 집계 함수는 AggregatedValue로 명명되어야 하며 숫자 값을 제공해야 합니다.다.


**그룹 필드**: 이 필드의 각 인스턴스에 대해 집계된 값이 있는 레코드가 만들어지며 각각에 대해 경고가 생성될 수 있습니다.  예를 들어 각 컴퓨터에 대해 경고를 생성하려면 **컴퓨터별**을 사용합니다.   

> [!NOTE]

> Application Insights를 기반으로 하는 메트릭 측정 경고 규칙에 대해서는 데이터를 그룹화하기 위한 필드를 지정할 수 있습니다. 이를 위해 규칙 정의의 **집계** 옵션을 사용합니다.   

**시간 간격**: 데이터가 집계되는 시간 간격을 정의합니다.  예를 들어 **5분**을 지정한 경우 경고에 대해 지정한 기간 동안 5분 간격으로 집계된 그룹 필드의 각 인스턴스에 대한 레코드가 만들어집니다.

**임계값**: 미터법 경고 규칙의 임계값은 집계 값 및 위반 횟수로 정의됩니다.  로그 검색에서 데이터 요소가 이 값을 초과하면 위반으로 간주됩니다.  결과의 개체에서 위반 횟수가 지정된 값을 초과하면 해당 개체에 대한 경고가 만들어집니다.

#### <a name="example"></a>예
컴퓨터에서 30분 동안 90% 프로세서 사용률을 3회 초과하는 경우 경고가 필요한 시나리오를 고려해 보세요.  다음 세부 정보로 경고 규칙을 만들 수 있습니다.  

**Query:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
**기간:** 30분<br>
**경고 빈도:** 5분<br>
**집계 값:** 90 초과<br>
**경고 트리거 조건:** 총 위반 5 초과<br>

이 쿼리는 각 컴퓨터에 대해 5분 간격으로 평균 값을 만듭니다.  이 쿼리는 이전 30분 동안 수집된 데이터에 대해 5분마다 실행됩니다.  세 개의 컴퓨터에 대한 샘플 데이터는 다음과 같습니다.

![샘플 쿼리 결과](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

이 예제에서 srv02와 srv03은 기간 동안 90% 임계값을 3회 위반했기 때문에 별도의 경고가 만들어집니다.  **경고 트리거 조건**이 **연속**으로 변경된 경우 세 개의 연속 샘플에 대한 임계값을 위반했기 때문에 srv03에 대해서만 경고가 만들어집니다.


## <a name="next-steps"></a>다음 단계
* [Azure Alerts(미리 보기) 개요 보기](monitoring-overview-unified-alerts.md)
* [Azure Alerts(미리 보기) 사용](monitor-alerts-unified-usage.md)에 대해 알아보기
* [Log Analytics](../log-analytics/log-analytics-overview.md)에 대해 자세히 알아보기    
