---
title: 모니터링 Azure Machine Learning | Microsoft Docs
description: Azure Monitor를 사용 하 여 Azure Machine Learning에서 메트릭에 대 한 경고를 보고 분석 하 고 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/01/2020
ms.openlocfilehash: b8ca96dd0b11f7a4c76f7a954959ef5005fb4a40
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323979"
---
# <a name="monitor-azure-machine-learning"></a>Azure Machine Learning 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있는 경우 이와 같은 리소스의 가용성, 성능 및 작업을 모니터링할 수 있습니다. 이 문서에서는 Azure Machine Learning에서 생성 되는 모니터링 데이터와 Azure Monitor를 사용 하 여이 데이터를 분석 하 고 경고 하는 방법을 설명 합니다.

> [!TIP]
> 이 문서의 정보는 *작업 영역* 수준에서 Azure Machine Learning에 대 한 모니터링을 설명 하므로 주로 관리자를 위한 것입니다. 데이터 과학자 또는 개발자 이며 *모델 학습 실행과* 관련 된 정보를 모니터링 하려는 경우 다음 문서를 참조 하세요.
>
> * [학습 실행 시작, 모니터링 및 취소](how-to-manage-runs.md)
> * [학습 실행에 대한 메트릭 로그](how-to-track-experiments.md)
> * [MLflow로 실험 추적](how-to-use-mlflow.md)
> * [TensorBoard로 시각화 실행](how-to-monitor-tensorboard.md)

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Machine Learning는 Azure에서 전체 스택 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md)를 사용 하 여 모니터링 데이터를 만듭니다. Azure Monitor는 Azure 리소스를 모니터링 하는 완전 한 기능 집합을 제공 합니다. 또한 다른 클라우드 및 온-프레미스의 리소스도 모니터링할 수 있습니다.

다음 개념을 설명 하는 [Azure Monitor을 사용 하 여 Azure 리소스 모니터링](../azure-monitor/insights/monitor-azure-resource.md)문서부터 시작 합니다.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

다음 섹션에서는 Azure Machine Learning에 대해 수집 된 특정 데이터를 설명 하 여이 문서를 작성 합니다. 또한이 섹션에서는 Azure tools를 사용 하 여 데이터 수집을 구성 하 고이 데이터를 분석 하는 예제를 제공 합니다.

> [!TIP]
> Azure Monitor와 관련 된 비용을 이해 하려면 [사용량 및 예상 비용](../azure-monitor/platform/usage-estimated-costs.md)을 참조 하세요. 데이터가 Azure Monitor에 표시 되는 데 걸리는 시간을 이해 하려면 [로그 데이터 수집 시간](../azure-monitor/platform/data-ingestion-time.md)을 참조 하세요.

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure Machine Learning에서 데이터 모니터링

Azure Machine Learning는 [azure 리소스의 데이터 모니터링](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)에 설명 된 다른 azure 리소스와 동일한 종류의 모니터링 데이터를 수집 합니다. 

Azure Machine Learning에서 만든 로그 및 메트릭에 대 한 자세한 내용은 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md) 를 참조 하세요.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>컬렉션 및 라우팅

플랫폼 메트릭 및 활동 로그는 자동으로 수집 되 고 저장 되지만 진단 설정을 사용 하 여 다른 위치로 라우팅될 수 있습니다.  

리소스 로그는 진단 설정을 만들고 하나 이상의 위치로 라우팅할 때까지 수집 및 저장 되지 않습니다.

Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기를](../azure-monitor/platform/diagnostic-settings.md) 참조 하세요. 진단 설정을 만들 때 수집할 로그 범주를 지정 합니다. Azure Machine Learning 범주는 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md#resource-logs)에 나열 되어 있습니다.

> [!IMPORTANT]
> 이러한 설정을 사용 하려면 추가 Azure 서비스 (저장소 계정, 이벤트 허브 또는 Log Analytics)가 필요 하므로 비용이 늘어날 수 있습니다. 예상 비용을 계산 하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 방문 하세요.

Azure Machine Learning에 대해 다음 로그를 구성할 수 있습니다.

| 범주 | 설명 |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 계산 클러스터의 이벤트입니다. |
| AmlComputeClusterNodeEvent | Azure Machine Learning 계산 클러스터 내의 노드에서 발생 한 이벤트입니다. |
| AmlComputeJobEvent | Azure Machine Learning compute에서 실행 되는 작업의 이벤트입니다. |

> [!NOTE]
> 진단 설정에서 메트릭을 사용 하도록 설정 하면 현재 저장소 계정, 이벤트 허브 또는 log analytics로 전송 되는 정보의 일부로 차원 정보가 포함 되지 않습니다.

수집할 수 있는 메트릭 및 로그에 대해서는 다음 섹션에서 설명 합니다.

## <a name="analyzing-metrics"></a>메트릭 분석

**Azure Monitor** 메뉴에서 **메트릭** 을 열어 다른 Azure 서비스의 메트릭과 함께 Azure Machine Learning에 대 한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/platform/metrics-getting-started.md)을 참조하세요.

수집 되는 플랫폼 메트릭의 목록은 [모니터링 Azure Machine Learning 데이터 참조 메트릭](monitor-resource-reference.md#metrics)을 참조 하세요.

Azure Machine Learning에 대 한 모든 메트릭은 네임 스페이스 **Machine Learning 서비스 작업 영역** 에 있습니다.

![Machine Learning 서비스 작업 영역을 선택 메트릭 탐색기](./media/monitor-azure-machine-learning/metrics.png)

참조를 위해 [Azure Monitor에서 지원 되는 모든 리소스 메트릭의](../azure-monitor/platform/metrics-supported.md)목록을 볼 수 있습니다.

### <a name="filtering-and-splitting"></a>필터링 및 분할

차원을 지 원하는 메트릭의 경우 차원 값을 사용 하 여 필터를 적용할 수 있습니다. 예를 들어 **클러스터 이름** 에 대 한 **활성 코어** 를 필터링 `cpu-cluster` 합니다. 

메트릭을 차원으로 분할 하 여 메트릭의 각 세그먼트가 서로 어떻게 다른 지를 시각화할 수도 있습니다. 예를 들어 파이프라인 **단계 유형을** 분할 하 여 파이프라인에서 사용 되는 단계 유형의 수를 확인 합니다.

필터링 및 분할에 대 한 자세한 내용은 [Azure Monitor의 고급 기능](../azure-monitor/platform/metrics-charts.md)을 참조 하세요.

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>로그 분석

Azure Monitor Log Analytics를 사용 하려면 진단 구성을 만들고 __Log Analytics에 정보 보내기__ 를 사용 하도록 설정 해야 합니다. 자세한 내용은 [컬렉션 및 라우팅](#collection-and-routing) 섹션을 참조 하세요.

Azure Monitor 로그의 데이터는 테이블에 저장 되 고 각 테이블에는 고유한 속성 집합이 있습니다. Azure Machine Learning은 다음 테이블에 데이터를 저장 합니다.

| 테이블 | 설명 |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 계산 클러스터의 이벤트입니다. |
| AmlComputeClusterNodeEvent | Azure Machine Learning 계산 클러스터 내의 노드에서 발생 한 이벤트입니다. |
| AmlComputeJobEvent | Azure Machine Learning compute에서 실행 되는 작업의 이벤트입니다. |

> [!IMPORTANT]
> Azure Machine Learning 메뉴에서 **로그** 를 선택 하면 쿼리 범위가 현재 작업 영역으로 설정 된 Log Analytics 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 Azure 서비스의 데이터 또는 다른 데이터베이스의 데이터를 포함하는 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/log-query/scope.md)를 참조하세요.

로그 및 메트릭에 대 한 자세한 내용은 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md)를 참조 하세요.

### <a name="sample-kusto-queries"></a>샘플 Kusto 쿼리

> [!IMPORTANT]
> [서비스-이름] 메뉴에서 **로그** 를 선택 하면 현재 Azure Machine Learning 작업 영역으로 설정 된 쿼리 범위를 사용 하 여 Log Analytics 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 작업 영역 또는 기타 Azure 서비스의 데이터를 포함 하는 쿼리를 실행 하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택 합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/log-query/scope.md)를 참조하세요.

Azure Machine Learning 리소스를 모니터링 하는 데 사용할 수 있는 쿼리는 다음과 같습니다. 

+ 지난 5 일 동안 실패 한 작업 가져오기:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 특정 작업 이름에 대 한 레코드 가져오기:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ VM 크기가 Standard_D1_V2 된 클러스터의 최근 5 일 동안 클러스터 이벤트를 가져옵니다.

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 지난 8 일 동안 할당 된 노드 가져오기:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>경고

**Azure Monitor** 메뉴에서 **경고** 를 열어 Azure Machine Learning에 대 한 경고에 액세스할 수 있습니다. 경고 만들기에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리를](../azure-monitor/platform/alerts-metric.md) 참조 하세요.

다음 표에서는 Azure Machine Learning에 대 한 일반적인 메트릭 경고 규칙과 권장 메트릭을 보여 줍니다.

| 경고 유형 | 조건 | 설명 |
|:---|:---|:---|
| 모델 배포 실패 | 집계 유형: 합계, 연산자: 보다 큼, 임계값: 0 | 하나 이상의 모델 배포에 실패 한 경우 |
| 할당량 사용률 | 집계 유형: 평균, 연산자: 보다 큼, 임계값: 90| 할당량 사용률 백분율이 90% 보다 큰 경우 |
| 사용 불가 노드 | 집계 유형: 합계, 연산자: 보다 큼, 임계값: 0 | 사용할 수 없는 노드가 하나 이상 있는 경우 |

## <a name="next-steps"></a>다음 단계

- 로그 및 메트릭에 대 한 참조는 [모니터링 Azure Machine Learning 데이터 참조](monitor-resource-reference.md)를 참조 하세요.
- Azure Machine Learning와 관련 된 할당량을 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스에 대 한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조 하세요.
- Azure 리소스 모니터링에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure 리소스 모니터링](../azure-monitor/insights/monitor-azure-resource.md)을 참조 하세요.
