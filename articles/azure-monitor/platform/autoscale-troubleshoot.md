---
title: Azure 자동 크기 문제 해결
description: 서비스 패브릭, 가상 컴퓨터, 웹 앱 및 클라우드 서비스에 사용되는 Azure 자동 크기 조정문제를 추적합니다.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751340"
---
# <a name="troubleshooting-azure-autoscale"></a>Azure 자동 크기 문제 해결
 
Azure Monitor 자동 크기 조정을 사용하면 응용 프로그램의 부하를 처리하기 위해 적절한 양의 리소스를 실행할 수 있습니다. 이를 통해 부하 증가를 처리하는 리소스를 추가하고 유휴 상태인 리소스를 제거하여 비용을 절감할 수 있습니다. 선택한 일정, 고정된 날짜 시간 또는 리소스 메트릭을 기반으로 크기를 조정할 수 있습니다. 자세한 내용은 [자동 크기 개요](autoscale-overview.md)를 참조하십시오.

자동 크기 조정 서비스는 메트릭 및 로그를 제공하여 어떤 스케일 작업이 발생했는지 이해하고 이러한 작업을 초래한 조건의 평가를 제공합니다. 다음과 같은 질문에 대한 답변을 찾을 수 있습니다.

- 서비스가 확장되었거나 인젝인 이유는 무엇입니까?
- 서비스가 확장되지 않은 이유는 무엇입니까?
- 자동 크기 조정 작업이 실패한 이유는 무엇입니까?
- 자동 크기 조정 작업이 확장에 시간이 걸리는 이유는 무엇입니까?
  
## <a name="autoscale-metrics"></a>자동 크기 조정 메트릭

자동 크기 조정은 작업을 이해하는 [네 가지 메트릭을](metrics-supported.md#microsoftinsightsautoscalesettings) 제공합니다. 

- **관찰된 메트릭 값** - 자동 크기 조정 엔진에서 보거나 계산한 대로 배율 작업을 수행하도록 선택한 메트릭 값입니다. 단일 자동 크기 조정 설정에는 여러 규칙과 여러 메트릭 원본이 있을 수 있으므로 "메트릭 원본"을 측정값으로 필터링할 수 있습니다.
- **메트릭 임계값** - 배율 조정 작업을 수행하도록 설정한 임계값입니다. 단일 자동 크기 조정 설정에는 여러 규칙과 여러 메트릭 원본이 있을 수 있으므로 "메트릭 규칙"을 측정값으로 필터링할 수 있습니다.
- **관찰된 용량** - 자동 크기 조정 엔진에서 볼 수 있는 대상 리소스의 활성 인스턴스 수입니다.
- **시작된 크기 조정 작업** - 자동 크기 조정 엔진에서 시작된 확장 및 축소 작업의 수입니다. 작업의 배율 과 배율로 필터링할 수 있습니다.

[메트릭 탐색기를](metrics-getting-started.md) 사용하여 위의 메트릭을 한 곳에서 모두 차트로 차트로 만들 수 있습니다. 차트에는 다음이 표시됩니다.

  - 실제 메트릭
  - 자동 크기 조정 엔진에서 보거나 계산한 메트릭
  - 배율 조정 작업에 대한 임계값
  - 용량 의 변화 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>예제 1 - 간단한 자동 크기 조정 규칙 분석 

다음과 같은 가상 시스템 스케일 집합에 대한 간단한 자동 크기 조정 설정이 있습니다.

- 세트의 평균 CPU 비율이 10분 동안 70%를 초과할 때 확장됩니다. 
- 10분 이상 동안 세트의 CPU 비율이 5% 미만일 때 의 배율입니다. 

자동 크기 조정 서비스의 메트릭을 살펴보겠습니다.
 
![가상 머신 스케일 세트 백분율 CPU 예제](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![가상 머신 스케일 세트 백분율 CPU 예제](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***그림 1a - 가상 시스템 규모 집합에 대한 백분율 CPU 메트릭 및 자동 크기 조정 설정에 대한 관찰된 메트릭 값 메트릭***

![메트릭 임계값 및 관찰 된 용량](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***그림 1b - 미터법 임계값 및 관찰 된 용량***

그림 1b에서 배율 계산 규칙에 대한 **메트릭 임계값(연한** 파란색 선)은 70입니다.  **관찰된** 용량(진한 파란색 선)은 현재 3인 활성 인스턴스 수를 표시합니다. 

> [!NOTE]
> 메트릭 트리거 규칙 차원 확장(증가) 규칙별로 **메트릭 임계값을** 필터링하여 확장 임계값을 확인하고 규칙의 배율(감소)을 확인해야 합니다. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>예제 2 - 가상 시스템 스케일 집합에 대한 고급 자동 크기 조정

가상 시스템 스케일 세트 리소스가 자체 메트릭 **아웃바운드 흐름에**따라 확장할 수 있도록 하는 자동 크기 조정 설정이 있습니다. 메트릭 임계값에 대한 **인스턴스 수 옵션별 분할** 메트릭이 선택됩니다. 

배율 조정 작업 규칙은 다음과 같은 것입니다. 

**인스턴스당 아웃바운드 흐름** 값이 10보다 큰 경우 자동 크기 조정 서비스는 인스턴스 1개씩 확장되어야 합니다. 

이 경우 자동 크기 조정 엔진의 관찰된 메트릭 값은 실제 메트릭 값과 인스턴스 수를 나눈 값으로 계산됩니다. 관찰된 메트릭 값이 임계값보다 적으면 확장 작업이 시작되지 않습니다. 
 
![가상 머신 스케일 세트 자동 크기 메트릭 차트 예제](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![가상 머신 스케일 세트 자동 크기 메트릭 차트 예제](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***그림 2 - 가상 머신 스케일 세트 자동 크기 메트릭 차트 예제***

그림 2에서 두 개의 메트릭 차트를 볼 수 있습니다. 

상단차트에는 **아웃바운드 흐름** 메트릭의 실제 값이 표시됩니다. 실제 값은 6입니다. 

하단의 차트에는 몇 가지 값이 표시됩니다. 
 - **관찰된 메트릭** 값(연한 파란색)은 2개의 활성 인스턴스가 있고 6이 2로 나누어져 있기 때문에 3입니다. 
 - **관찰 된 용량** (보라색)는 자동 크기 조정 엔진에서 볼 인스턴스 수를 보여줍니다. 
 - **미터법** 임계값(녹색)은 10으로 설정됩니다. 

여러 축척 작업 규칙이 있는 경우 메트릭 탐색기 차트에서 분할 또는 **필터 추가** 옵션을 사용하여 특정 소스 또는 규칙별로 메트릭을 볼 수 있습니다. 메트릭 차트 분할에 대한 자세한 내용은 [메트릭 차트의 고급 기능 - 분할을](metrics-charts.md#apply-splitting-to-a-chart) 참조하세요.

## <a name="example-3---understanding-autoscale-events"></a>예 3 - 자동 크기 조정 이벤트 이해

자동 크기 조정 화면에서 기록 **실행** 탭으로 이동하여 가장 최근 배율 작업을 확인합니다. 탭에는 시간이 지남에 따라 **관찰된 용량의** 변화도 표시됩니다. 자동 크기 조정 설정 업데이트/삭제와 같은 작업을 포함한 모든 자동 크기 조정 작업에 대한 자세한 내용을 보려면 활동 로그를 보고 자동 크기 조정 작업별로 필터링합니다.

![자동 크기 조정 설정 실행 기록](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>리소스 로그 자동 크기 조정

다른 Azure 리소스와 마찬가지로 자동 크기 조정 서비스는 [리소스 로그를](platform-logs-overview.md)제공합니다. 로그에는 두 가지 범주가 있습니다.

- **자동 크기 조정 평가** - 자동 크기 조정 엔진은 검사를 수행 할 때마다 모든 단일 조건 평가에 대한 로그 항목을 기록합니다.  항목에는 메트릭의 관찰된 값, 평가된 규칙 및 평가결과 배율 작업 여부에 대한 세부 정보가 포함됩니다.

- **자동 크기 조정 작업** - 엔진 레코드는 자동 크기 조정 서비스에서 시작된 작업 이벤트 및 이러한 스케일 작업의 결과(성공, 실패 및 자동 크기 조정 서비스에서 볼 수 있는 크기 조정이 발생한 양)를 기록합니다.

지원되는 모든 Azure Monitor 서비스와 마찬가지로 [진단 설정을](diagnostic-settings.md) 사용하여 이러한 로그를 라우팅할 수 있습니다.

- 자세한 분석을 위해 로그 분석 작업 영역으로 이동
- 이벤트 허브로 이동한 다음 Azure 이외의 도구로 이동
- 보관을 위해 Azure 저장소 계정으로  

![자동 크기 조정 진단 설정](media/autoscale-troubleshoot/diagnostic-settings.png)

이전 그림은 Azure 포털 자동 크기 진단 설정을 보여 주며 있습니다. 진단/리소스 로그 탭을 선택하고 로그 수집 및 라우팅을 활성화할 수 있습니다. 또한 *Microsoft.Insights/AutoscaleSettings로*리소스 유형을 선택하여 진단 설정에 대한 REST API, CLI, PowerShell, 리소스 관리자 템플릿을 사용하여 동일한 작업을 수행할 수도 있습니다. 

## <a name="troubleshooting-using-autoscale-logs"></a>자동 크기 조정 로그를 사용한 문제 해결 

최상의 문제 해결 환경을 위해 자동 크기 조정 설정을 만들 때 작업 영역을 통해 로그를 Azure 모니터 로그(Log Analytics)로 라우팅하는 것이 좋습니다. 이 프로세스는 이전 섹션의 그림에 표시됩니다. Log Analytics를 사용하여 평가의 유효성을 검사하고 작업을 더 잘 확장할 수 있습니다.

자동 크기 조정 로그를 Log Analytics 작업 영역으로 보내도록 구성한 후에는 다음 쿼리를 실행하여 로그를 확인할 수 있습니다. 

시작하려면 이 쿼리를 사용하여 최신 자동 크기 조정 평가 로그를 확인하십시오.

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

또는 다음 쿼리를 사용하여 최신 배율 작업 로그를 확인합니다.

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

다음 섹션을 사용하여 다음 질문들을 활용한다. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>예상하지 못했던 스케일 작업이 발생했습니다.

먼저 배율 작업에 대한 쿼리를 실행하여 관심 있는 배율 작업을 찾습니다. 최신 배율 조정 작업인 경우 다음 쿼리를 사용합니다.

```Kusto
AutoscaleScaleActionsLog
| take 1
```

배율 작업 로그에서 상관 Id 필드를 선택합니다. 상관 관계 Id를 사용하여 올바른 평가 로그를 찾습니다. 아래 쿼리를 실행하면 해당 배율 작업으로 이어지는 평가된 모든 규칙 및 조건이 표시됩니다.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>어떤 프로파일로 인해 스케일 작업이 발생했습니까?

배율 조정된 작업이 발생했지만 규칙과 프로필이 겹치고 어떤 작업이 발생했는지 추적해야 합니다. 

(예제 1에서 설명된 대로) 스케일 작업의 correlationId를 찾은 다음 평가 로그에 대한 쿼리를 실행하여 프로필에 대해 자세히 알아봅니다.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

전체 프로필 평가는 다음 쿼리를 사용하여 더 잘 이해할 수 있습니다.

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>배율 작업이 발생하지 않았습니다.

나는 스케일 작업을 예상하고 발생하지 않았다. 배율 작업 이벤트 또는 로그가 없을 수 있습니다.

메트릭 기반 배율 규칙을 사용하는 경우 자동 크기 조정 메트릭을 검토합니다. **관찰된 메트릭 값** 또는 **관찰된 용량이** 예상한 것과 다를 수 있으므로 배율 규칙이 적용되지 않았을 수 있습니다. 평가는 계속 표시되지만 확장 규칙은 표시되지 않습니다. 냉각 시간이 스케일 동작이 발생하지 않도록 할 수도 있습니다. 

 배율 작업이 발생할 것으로 예상한 기간 동안 자동 크기 조정 평가 로그를 검토합니다. 모든 평가와 스케일 작업을 트리거하지 않기로 결정한 이유를 검토합니다.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>배율 조정 작업이 실패했습니다.

자동 크기 조정 서비스가 스케일 작업을 수행했지만 시스템이 확장하지 않거나 스케일 작업을 완료하지 못한 경우가 있을 수 있습니다. 이 쿼리를 사용하여 실패한 배율 작업을 찾습니다.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

자동 크기 조정 작업 또는 오류에 대한 알림을 받을 수 있도록 경고 규칙을 만듭니다. 경고 규칙을 만들어 자동 크기 조정 이벤트에 대한 알림을 받을 수도 있습니다.

## <a name="schema-of-autoscale-resource-logs"></a>자동 크기 조정 리소스 로그 스키마

자세한 내용은 [리소스 로그를 자동으로 조정합니다.](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>다음 단계
[자동 크기 조정 모범 사례에](autoscale-best-practices.md)대한 정보를 읽어보십시오. 
