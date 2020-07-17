---
title: Azure 자동 크기 조정 문제 해결
description: Service Fabric, Virtual Machines, Web Apps 및 클라우드 서비스에서 사용 되는 Azure 자동 크기 조정 문제를 추적 합니다.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75751340"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure 자동 크기 조정 문제 해결
 
자동 크기 조정을 사용 하면 응용 프로그램의 부하를 처리 하기 위해 적절 한 양의 리소스를 실행할 수 있습니다. Azure Monitor 이 기능을 사용 하면 부하를 처리 하는 리소스를 추가 하 고 유휴 상태의 리소스를 제거 하 여 비용을 절감할 수도 있습니다. 선택한 일정, 고정 된 날짜/시간 또는 리소스 메트릭에 따라 크기를 조정할 수 있습니다. 자세한 내용은 [자동 크기 조정 개요](autoscale-overview.md)를 참조 하세요.

자동 크기 조정 서비스는 메트릭 및 로그를 제공 하 여 발생 한 크기 조정 작업과 해당 작업을 수행 하는 조건의 평가를 이해 합니다. 다음과 같은 질문에 대 한 답을 찾을 수 있습니다.

- 내 서비스가 규모 확장 또는 축소 되는 이유는 무엇 인가요?
- 서비스가 확장 되지 않는 이유는 무엇 인가요?
- 자동 크기 조정 작업이 실패 하는 이유는 무엇 인가요?
- 크기 조정 작업을 확장 하는 데 시간이 걸리는 이유는 무엇 인가요?
  
## <a name="autoscale-metrics"></a>자동 크기 조정 메트릭

자동 크기 조정은 [4 개의 메트릭을](metrics-supported.md#microsoftinsightsautoscalesettings) 제공 하 여 해당 작업을 이해 합니다. 

- **관찰 된 메트릭 값** -자동 크기 조정 엔진에서 표시 하거나 계산 된 대로 크기 조정 작업을 수행 하기 위해 선택한 메트릭의 값입니다. 단일 자동 크기 조정 설정에는 여러 규칙이 있을 수 있으므로 여러 메트릭 소스가 있으므로 "메트릭 원본"을 차원으로 사용 하 여 필터링 할 수 있습니다.
- **메트릭 임계값** -크기 조정 작업을 수행 하기 위해 설정 하는 임계값입니다. 단일 자동 크기 조정 설정에는 여러 규칙이 있을 수 있으므로 여러 메트릭 소스가 있으므로 "메트릭 규칙"을 차원으로 사용 하 여 필터링 할 수 있습니다.
- **관찰 된 용량** -자동 크기 조정 엔진에 표시 되는 대상 리소스의 활성 인스턴스 수입니다.
- **시작된 크기 조정 작업** - 자동 크기 조정 엔진에서 시작된 확장 및 축소 작업의 수입니다. 작업에서 규모 확장 및 규모 확장을 기준으로 필터링 할 수 있습니다.

[메트릭 탐색기](metrics-getting-started.md) 를 사용 하 여 위의 메트릭을 모두 한 곳에서 차트로 만들 수 있습니다. 차트에 다음이 표시 됩니다.

  - 실제 메트릭
  - 자동 크기 조정 엔진에서 표시/계산 되는 메트릭입니다.
  - 크기 조정 작업에 대 한 임계값입니다.
  - 용량 변경 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>예제 1-단순 자동 크기 조정 규칙 분석 

가상 머신 확장 집합에 대해 다음과 같은 간단한 자동 크기 조정 설정이 있습니다.

- 집합의 평균 CPU 비율이 10 분 동안 70% 보다 큰 경우 규모를 확장 합니다. 
- 집합의 CPU 백분율이 10 분 넘게 5% 미만인 경우 규모를 확장 합니다. 

자동 크기 조정 서비스에서 메트릭을 검토해 보겠습니다.
 
![가상 머신 확장 집합 백분율 CPU 예](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![가상 머신 확장 집합 백분율 CPU 예](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***그림 1a-자동 크기 조정 설정에 대 한 가상 머신 확장 집합 및 관찰 된 메트릭 값 메트릭에 대 한 백분율 CPU 메트릭***

![메트릭 임계값 및 관찰 된 용량](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***그림 1b-메트릭 임계값 및 관찰 된 용량***

그림 1b에서 스케일 아웃 규칙의 **메트릭 임계값** (연한 파란색 선)은 70입니다.  **관찰 된 용량** (진한 파란색 선)은 현재 3 인 활성 인스턴스의 수를 표시 합니다. 

> [!NOTE]
> 규모 확장 임계값을 확인 하 고 규칙 크기 조정 (감소)을 수행 하려면 메트릭 트리거 규칙 차원 확장 (증가) 규칙에 따라 **메트릭 임계값** 을 필터링 해야 합니다. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>예제 2-가상 머신 확장 집합에 대 한 고급 자동 크기 조정

가상 머신 확장 집합 리소스를 자체 메트릭 **아웃 바운드 흐름**에 따라 확장할 수 있는 자동 크기 조정 설정이 있습니다. 메트릭 임계값에 대 한 **인스턴스 수로 메트릭 나누기** 옵션이 선택 되어 있는지 확인 합니다. 

크기 조정 작업 규칙은 다음과 같습니다. 

**인스턴스당 아웃 바운드 흐름** 의 값이 10 보다 큰 경우 자동 크기 조정 서비스는 인스턴스 1 개를 확장 해야 합니다. 

이 경우 자동 크기 조정 엔진의 관찰 된 메트릭 값은 인스턴스 수로 나눈 실제 메트릭 값으로 계산 됩니다. 관찰 된 메트릭 값이 임계값 보다 낮으면 확장 작업을 시작 하지 않습니다. 
 
![가상 머신 확장 집합 자동 크기 조정 메트릭 차트 예](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![가상 머신 확장 집합 자동 크기 조정 메트릭 차트 예](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***그림 2-가상 머신 확장 집합 자동 크기 조정 메트릭 차트 예***

그림 2에서는 두 개의 메트릭 차트를 볼 수 있습니다. 

위쪽의 차트에는 **아웃 바운드 흐름** 메트릭의 실제 값이 표시 됩니다. 실제 값은 6입니다. 

아래쪽의 차트에서는 몇 가지 값을 보여 줍니다. 
 - **관찰 된 메트릭 값** (연한 파란색)은 2 개의 활성 인스턴스와 6이 2로 나뉘어 3 이기 때문에 3입니다. 
 - **관찰 된 용량** (자주)은 자동 크기 조정 엔진에서 표시 하는 인스턴스 수를 표시 합니다. 
 - **메트릭 임계값** (연한 녹색)은 10으로 설정 되어 있습니다. 

여러 크기 조정 작업 규칙이 있는 경우 메트릭 탐색기 차트의 분할 또는 **필터 추가** 옵션을 사용 하 여 특정 원본 또는 규칙에 따라 메트릭을 확인할 수 있습니다. 메트릭 차트를 분할 하는 방법에 대 한 자세한 내용은 [메트릭 차트의 고급 기능-분할](metrics-charts.md#apply-splitting-to-a-chart) 을 참조 하세요.

## <a name="example-3---understanding-autoscale-events"></a>예제 3-자동 크기 조정 이벤트 이해

자동 크기 조정 설정 화면에서 **실행 기록** 탭으로 이동 하 여 가장 최근의 크기 조정 작업을 표시 합니다. 또한이 탭에서는 시간이 지남에 따라 **관찰 된 용량의** 변화를 보여 줍니다. 자동 크기 조정 설정 업데이트/삭제와 같은 작업을 비롯 한 모든 자동 크기 조정 작업에 대 한 자세한 내용은 활동 로그 보기 및 자동 크기 조정 작업으로 필터링을 참조 하세요.

![자동 크기 조정 설정 실행 기록](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>자동 크기 조정 리소스 로그

다른 Azure 리소스와 마찬가지로 자동 크기 조정 서비스에서 [리소스 로그](platform-logs-overview.md)를 제공 합니다. 로그에는 두 가지 범주가 있습니다.

- **자동 크기 조정 평가** -자동 크기 조정 엔진은 검사할 때마다 모든 단일 조건 평가에 대 한 로그 항목을 기록 합니다.  항목에는 관찰 된 메트릭 값에 대 한 세부 정보, 평가 된 규칙 및 평가 결과 크기 조정 작업이 발생 하는 경우가 포함 됩니다.

- **자동 크기 조정 작업** -엔진은 자동 크기 조정 서비스에서 시작 된 크기 조정 작업 이벤트와 이러한 크기 조정 작업의 결과 (성공, 실패, 자동 크기 조정 서비스에서 볼 때 발생 한 크기 조정)를 기록 합니다.

Azure Monitor 지원 되는 서비스와 마찬가지로 [진단 설정을](diagnostic-settings.md) 사용 하 여 이러한 로그를 라우팅할 수 있습니다.

- 자세한 분석을 위해 Log Analytics 작업 영역으로
- Event Hubs 하 고 Azure 이외의 도구에
- 보관을 위해 Azure 저장소 계정으로  

![자동 크기 조정 진단 설정](media/autoscale-troubleshoot/diagnostic-settings.png)

위의 그림은 Azure Portal 자동 크기 조정 진단 설정을 보여 줍니다. 여기에서 진단/리소스 로그 탭을 선택 하 고 로그 수집 및 라우팅을 사용 하도록 설정할 수 있습니다. *AutoscaleSettings/* 으로 리소스 종류를 선택 하 여 진단 설정에 대해 REST API, CLI, PowerShell, 리소스 관리자 템플릿을 사용 하 여 동일한 작업을 수행할 수도 있습니다. 

## <a name="troubleshooting-using-autoscale-logs"></a>자동 크기 조정 로그를 사용 하 여 문제 해결 

최상의 문제 해결 환경을 위해 자동 크기 조정 설정을 만들 때 작업 영역을 통해 로그를 Azure Monitor 로그 (Log Analytics)로 라우팅하는 것이 좋습니다. 이 프로세스는 이전 섹션의 그림에 표시 되어 있습니다. Log Analytics를 사용 하 여 평가의 유효성을 검사 하 고 작업을 더 효율적으로 확장할 수 있습니다.

자동 크기 조정 로그가 Log Analytics 작업 영역으로 전송 되도록 구성 했으면 다음 쿼리를 실행 하 여 로그를 확인할 수 있습니다. 

시작 하려면 다음 쿼리를 시도 하 여 가장 최근의 자동 크기 조정 평가 로그를 확인 합니다.

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

또는 다음 쿼리를 시도 하 여 가장 최근의 크기 조정 작업 로그를 확인 합니다.

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

다음 섹션에서 이러한 질문을 사용할 수 있습니다. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>예상치 못한 크기 조정 작업이 발생 했습니다.

먼저 크기 조정 작업에 대 한 쿼리를 실행 하 여 관심 있는 크기 조정 작업을 찾습니다. 최신 크기 조정 작업 인 경우 다음 쿼리를 사용 합니다.

```Kusto
AutoscaleScaleActionsLog
| take 1
```

크기 조정 작업 로그에서 CorrelationId 필드를 선택 합니다. CorrelationId를 사용 하 여 올바른 평가 로그를 찾습니다. 아래 쿼리를 실행 하면 해당 크기 조정 작업으로 평가 된 모든 규칙 및 조건이 표시 됩니다.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>크기 조정 작업을 일으킨 프로필은 무엇 인가요?

크기 조정 된 작업이 발생 했지만 규칙 및 프로필을 겹치게 하 고 작업을 일으킨 작업을 추적 해야 합니다. 

크기 조정 작업의 correlationId (예 1에 설명 된 대로)를 찾은 다음 평가 로그에 대해 쿼리를 실행 하 여 프로필에 대해 자세히 알아보세요.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

다음 쿼리를 사용 하 여 전체 프로필 평가를 더 잘 이해할 수도 있습니다.

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>크기 조정 작업이 발생 하지 않았습니다.

크기 조정 작업이 필요 하지만 발생 하지 않았습니다. 크기 조정 작업 이벤트 또는 로그가 없을 수 있습니다.

메트릭 기반 크기 조정 규칙을 사용 하는 경우 자동 크기 조정 메트릭을 검토 합니다. **관찰 된 메트릭 값** 또는 **관찰 된 용량이** 예상한 것과 같을 수 없으므로 크기 조정 규칙이 발생 하지 않습니다. 평가는 계속 표시 되지만 스케일 아웃 규칙은 표시 되지 않습니다. 쿨 다운 시간으로 크기 조정 작업이 발생 하지 않도록 할 수도 있습니다. 

 크기 조정 작업이 수행 되어야 하는 기간 동안 자동 크기 조정 평가 로그를 검토 합니다. 수행 된 모든 평가 및 크기 조정 작업을 트리거하지 않도록 결정 한 이유를 검토 합니다.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>크기 조정 작업이 실패 했습니다.

자동 크기 조정 서비스에서 크기 조정 작업을 수행 했지만 시스템에서 크기 조정 작업을 완료 하지 못했거나 크기 조정 작업을 완료 하지 않기로 결정 한 경우를 들 수 있습니다. 이 쿼리를 사용 하 여 실패 한 크기 조정 작업을 찾을 수 있습니다.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

자동 크기 조정 작업 또는 실패에 대 한 알림을 받을 수 있는 경고 규칙을 만듭니다. 자동 크기 조정 이벤트에 대 한 알림을 받을 수 있는 경고 규칙을 만들 수도 있습니다.

## <a name="schema-of-autoscale-resource-logs"></a>자동 크기 조정 리소스 로그의 스키마

자세한 내용은 [자동 크기 조정 리소스 로그](autoscale-resource-log-schema.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
[자동 크기 조정 모범 사례](autoscale-best-practices.md)에 대 한 정보를 읽습니다. 
