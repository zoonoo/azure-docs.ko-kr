---
title: Azure 자동 크기 조정 문제 해결
description: Service Fabric, Virtual Machines, Web Apps 및 클라우드 서비스에서 사용되는 Azure 자동 크기 조정의 문제를 추적합니다.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a0fed6c2d06edcb2c9eb8d715feb0ef6c6ade46f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711402"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure 자동 크기 조정 문제 해결
 
Azure Monitor 자동 크기 조정을 사용하면 애플리케이션의 부하를 처리하기 위해 적절한 양의 리소스가 실행되도록 할 수 있습니다. 그러면 늘어난 부하를 처리하기 위해 리소스를 추가하고 유휴 상태의 리소스를 제거하여 비용을 절약할 수 있습니다. 예약, 고정 날짜-시간 또는 선택한 리소스 메트릭에 따라 스케일링할 수 있습니다. 자세한 내용은 [자동 크기 조정 개요](autoscale-overview.md)를 참조하세요.

자동 크기 조정 서비스는 어떤 스케일링 작업이 발생했고 그러한 작업이 유발된 조건 평가는 무엇인지 해석할 수 있는 메트릭과 로그를 제공합니다. 다음과 같은 질문에 대한 답을 찾을 수 있습니다.

- 서비스가 스케일 아웃 또는 스케일 인되는 이유는 무엇인가요?
- 서비스가 스케일링되지 않는 이유는 무엇인가요?
- 자동 크기 조정 작업이 실패하는 이유는 무엇인가요?
- 자동 크기 조정 작업의 스케일링에 시간이 걸리는 이유는 무엇인가요?
  
## <a name="autoscale-metrics"></a>자동 크기 조정 메트릭

자동 크기 조정은 작업을 해석할 수 있는 [4가지 메트릭](../essentials/metrics-supported.md#microsoftinsightsautoscalesettings)을 제공합니다. 

- **관찰된 메트릭 값** - 자동 크기 조정 엔진에서 표시 또는 계산한 대로 스케일링 작업을 수행하기 위해 사용자가 선택한 메트릭의 값입니다. 단일 자동 크기 조정 설정에 여러 규칙이 있고 그에 따라 메트릭 원본이 여럿일 수 있으므로 "메트릭 원본"을 차원으로 사용하여 필터링할 수 있습니다.
- **메트릭 임계값** - 스케일링 작업을 수행하기 위해 사용자가 설정한 임계값입니다. 단일 자동 크기 조정 설정에 여러 규칙이 있고 그에 따라 메트릭 원본이 여럿일 수 있으므로 "메트릭 규칙"을 차원으로 사용하여 필터링할 수 있습니다.
- **관찰된 용량** - 자동 크기 조정 엔진에서 표시하는 대상 리소스의 활성 인스턴스 수입니다.
- **시작된 크기 조정 작업** - 자동 크기 조정 엔진에서 시작된 확장 및 축소 작업의 수입니다. 작업의 스케일 인 또는 스케일 아웃 작업을 기준으로 필터링할 수 있습니다.

[메트릭 탐색기](../essentials/metrics-getting-started.md)를 사용하여 위의 메트릭을 모두 한 곳에서 차트로 만들 수 있습니다. 차트에는 다음이 표시됩니다.

  - 실제 메트릭
  - 자동 크기 조정 엔진에서 표시/계산한 메트릭
  - 스케일링 작업에 대한 임계값
  - 용량 변경 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>예제 1 - 단순 자동 크기 조정 규칙 분석 

가상 머신 확장 집합에 대해 다음과 같은 간단한 자동 크기 조정 설정이 있습니다.

- 집합의 평균 CPU 백분율이 10분 동안 70%를 넘으면 스케일 아웃 
- 집합의 평균 CPU 백분율이 10분 넘게 5% 미만이면 스케일 인 

자동 크기 조정 서비스의 메트릭을 살펴보겠습니다.
 
![가상 머신 확장 집합 백분율 CPU 예제를 보여주는 스크린샷.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![가상 머신 확장 집합 백분율 CPU 예제](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***그림 1a - 자동 크기 조정 설정에 대한 가상 머신 확장 집합 및 관찰된 메트릭 값의 백분율 CPU 메트릭***

![메트릭 임계값 및 관찰된 용량](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***그림 1b - 메트릭 임계값 및 관찰된 용량***

그림 1b에서 스케일 아웃 규칙의 **메트릭 임계값**(연한 파랑 선)은 70입니다.  **관찰된 용량**(진한 파랑 선)은 활성 인스턴스의 수를 표시하며 현재 3개입니다. 

> [!NOTE]
> 스케일 아웃 임계값을 확인하기 위한 메트릭 트리거 규칙 차원 스케일 아웃(증가) 규칙과 스케일 인(감소) 규칙을 기준으로 **메트릭 임계값** 을 필터링해야 합니다. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>예제 2 - 가상 머신 확장 집합에 대한 고급 자동 크기 조정

가상 머신 확장 집합 리소스가 자체 메트릭 **아웃바운드 흐름** 에 따라 스케일 아웃할 수 있게 하는 자동 크기 조정 설정이 있습니다. 메트릭 임계값에 대해 **인스턴스 수로 메트릭 나누기** 옵션이 선택되어 있는지 확인합니다. 

스케일링 작업 규칙은 다음과 같습니다. 

**인스턴스당 아웃바운드 흐름** 의 값이 10보다 크면 자동 크기 조정 서비스에서 1개의 인스턴스를 스케일 아웃해야 합니다. 

이 경우 자동 크기 조정 엔진의 관찰된 메트릭 값은 실제 메트릭 값을 인스턴스 수로 나누어 계산합니다. 관찰된 메트릭 값이 임계값보다 작으면 스케일 아웃 작업을 시작하지 않습니다. 
 
![가상 머신 확장 집합 자동 크기 조정 메트릭 차트의 예제가 있는 평균 아웃바운드 흐름 페이지를 보여주는 스크린샷.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![가상 머신 확장 집합 자동 크기 조정 메트릭 차트 예제](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***그림 2 - 가상 머신 확장 집합 자동 크기 조정 메트릭 차트 예제***

그림 2에는 두 메트릭 차트가 있습니다. 

위의 차트에는 **아웃바운드 흐름** 메트릭의 실제 값이 표시됩니다. 실제 값은 6입니다. 

아래의 차트에는 몇 가지 값이 있습니다. 
 - 활성 인스턴스 2개가 있고 6을 2로 나누면 3이므로 **관찰된 메트릭 값**(연한 파랑)은 3입니다. 
 - **관찰된 용량**(자주)은 자동 크기 조정 엔진에서 표시하는 인스턴스 수를 나타냅니다. 
 - **메트릭 임계값**(연한 녹색)은 10으로 설정되어 있습니다. 

여러 스케일링 작업 규칙이 있으면 메트릭 탐색기 차트에서 분할 또는 **필터 추가** 옵션을 사용하여 특정 원본 또는 규칙별로 메트릭을 확인할 수 있습니다. 메트릭 차트 분할에 대한 자세한 내용은 [메트릭 차트의 고급 기능 - 분할](../essentials/metrics-charts.md#apply-splitting)을 참조하세요.

## <a name="example-3---understanding-autoscale-events"></a>예제 3 - 자동 크기 조정 이벤트 해석

자동 크기 조정 설정 화면에서 **실행 기록** 탭으로 이동하여 가장 최근의 스케일링 작업을 확인합니다. 이 탭에서는 시간 경과에 따른 **관찰된 용량** 변화도 보여줍니다. 자동 크기 조정 설정 업데이트/삭제 등과 같은 작업을 포함하여 자동 크기 조정 작업에 대한 세부 정보는 자동 크기 조정 작업별 활동 로그와 필터를 확인하세요.

![자동 크기 조정 설정 실행 기록](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>자동 크기 조정 리소스 로그

다른 Azure 리소스와 마찬가지로 자동 크기 조정 서비스는 [리소스 로그](../essentials/platform-logs-overview.md)를 제공합니다. 로그 범주는 두 가지입니다.

- **자동 크기 조정 평가** - 자동 크기 조정 엔진은 검사를 수행할 때마다 모든 단일 조건 평가에 대한 로그 항목을 기록합니다.  항목에는 관찰된 메트릭 값에 대한 세부 정보, 평가된 규칙, 평가 결과가 스케일링 작업인지 여부가 포함됩니다.

- **자동 크기 조정 작업** - 엔진은 자동 크기 조정 서비스가 시작한 스케일링 작업 이벤트와 그러한 스케일링 작업의 결과(성공, 실패, 자동 크기 조정 서비스에서 확인한 스케일링 크기)를 기록합니다.

모든 Azure Monitor 지원 서비스에서처럼 [진단 설정](../essentials/diagnostic-settings.md)을 사용하여 이 로그를 다음 위치로 라우팅할 수 있습니다.

- 자세한 분석을 위해 Log Analytics 작업 영역으로
- Event Hubs를 거쳐 비 Azure 도구로
- 보관을 위해 Azure 스토리지 계정으로  

![자동 크기 조정 진단 설정](media/autoscale-troubleshoot/diagnostic-settings.png)

위의 그림은 Azure Portal 자동 크기 조정 진단 설정을 보여줍니다. 여기에서 진단/리소스 로그 탭을 선택하고 로그 수집 및 라우팅을 사용하도록 설정할 수 있습니다. 진단 설정에 대한 REST API, CLI, PowerShell, Resource Manager를 통해서도 리소스 형식을 *Microsoft.Insights/AutoscaleSettings* 로 선택하여 같은 작업을 수행할 수 있습니다. 

## <a name="troubleshooting-using-autoscale-logs"></a>자동 크기 조정 로그를 사용한 문제 해결 

최상의 문제 해결 환경을 위해 자동 크기 조정 설정을 만들 때 작업 영역을 통해 로그를 Azure Monitor 로그(Log Analytics)에 라우팅하는 것이 좋습니다. 이 프로세스는 이전 섹션의 그림에 나와 있습니다. Log Analytics를 사용하여 평가 및 스케일링 작업의 유효성을 더 나은 방식으로 검사할 수 있습니다.

자동 크기 조정 로그를 Log Analytics 작업 영역으로 보내도록 구성하고 나면 다음 쿼리를 실행하여 로그를 확인할 수 있습니다. 

시작하려면 이 쿼리를 사용하여 가장 최근의 자동 크기 조정 평가 로그를 확인합니다.

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

또는 다음 쿼리로 가장 최근의 스케일링 작업 로그를 확인합니다.

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

이 질문에 대한 다음 섹션을 참조하세요. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>예상치 못한 스케일링 작업 발생

먼저 스케일링 작업에 대해 쿼리를 실행하여 문제의 스케일링 작업을 찾습니다. 최신 스케일링 작업인 경우 다음 쿼리를 사용합니다.

```Kusto
AutoscaleScaleActionsLog
| take 1
```

스케일링 작업 로그에서 CorrelationId 필드를 선택합니다. CorrelationId를 사용하여 올바른 평가 로그를 찾습니다. 아래 쿼리를 실행하면 해당 스케일링 작업으로 평가된 모든 규칙 및 조건이 표시됩니다.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>어떤 프로필 때문에 스케일링 작업이 발생하나요?

스케일링 작업이 발생했으나 겹치는 규칙과 프로필이 있어 어떤 것이 작업을 초래했는지 추적해야 합니다. 

스케일링 작업의 correlationId(예제 1에서 설명)를 찾고 평가 로그에서 쿼리를 실행하여 프로필에 대한 상세 정보를 파악합니다.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

다음 쿼리를 사용한 전체 프로필 평가가 더 해석하기 쉬울 수 있습니다.

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>스케일링 작업이 일어나지 않음

스케일링 작업을 예상했으나 발생하지 않았습니다. 스케일링 작업 이벤트 또는 로그가 없는 것 같습니다.

메트릭 기반 스케일링 규칙을 사용하는 경우 자동 크기 조정 메트릭을 검토합니다. **관찰된 메트릭 값** 또는 **관찰된 용량** 이 사용자의 예상과 달라 크기 조정 규칙이 실행되지 않았을 수 있습니다. 평가는 계속 표시되지만 스케일 아웃 규칙은 표시되지 않습니다. 휴지 시간에 스케일링 작업이 발생하지 않도록 할 수도 있습니다. 

 스케일링 작업이 발생될 것으로 예상하는 기간 중의 자동 크기 조정 평가 로그를 검토합니다. 모든 평가를 검토하여 스케일링 작업을 트리거하지 않도록 결정하게 된 이유를 파악합니다.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>스케일링 작업 실패

자동 크기 조정 서비스가 스케일링 작업을 취했으나 시스템이 스케일링을 하지 않게 결정했거나 스케일링 작업을 완료하지 못했을 수 있습니다. 이 쿼리를 사용하여 실패한 스케일링 작업을 확인할 수 있습니다.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

자동 크기 조정 작업 또는 실패에 대한 알림을 받기 위한 경고 규칙을 만듭니다. 자동 크기 조정 이벤트에 대한 알림을 받는 경고 규칙을 만들 수도 있습니다.

## <a name="schema-of-autoscale-resource-logs"></a>자동 크기 조정 리소스 로그의 스키마

자세한 내용은 [자동 크기 조정 리소스 로그](autoscale-resource-log-schema.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[자동 크기 조정 모범 사례](autoscale-best-practices.md)에 대한 정보를 읽습니다.