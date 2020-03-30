---
title: Azure 기계 학습 모니터링 | 마이크로 소프트 문서
description: Azure 모니터를 사용하여 Azure 기계 학습의 메트릭에 대한 경고를 보고 분석하고 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399108"
---
# <a name="monitoring-azure-machine-learning"></a>Azure 기계 학습 모니터링

이 문서에서는 Azure 기계 학습에서 생성된 모니터링 데이터에 대해 설명합니다. 또한 Azure Monitor를 사용하여 데이터를 분석하고 경고를 정의하는 방법에 대해서도 설명합니다.

> [!TIP]
> 이 문서의 정보는 주로 Azure 기계 학습에 대한 모니터링에 대해 설명하는 관리자를 위한 정보입니다. 데이터 과학자 또는 개발자이고 모델 학습 실행과 관련된 정보를 모니터링하려면 다음 문서를 참조하십시오.
>
> * [교육 실행 시작, 모니터링 및 취소](how-to-manage-runs.md)
> * [학습 실행에 대한 메트릭 로그](how-to-track-experiments.md)
> * [MLflow로 실험 추적](how-to-use-mlflow.md)
> * [TensorBoard로 시각화 실행](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure 기계 학습은 Azure의 전체 스택 모니터링 서비스인 Azure Monitor를 사용하여 모니터링 데이터를 기록합니다. Azure 모니터는 Azure 리소스를 모니터링하는 전체 기능 집합을 제공합니다. 또한 다른 클라우드 및 온-프레미스의 리소스를 모니터링할 수도 있습니다.

모니터링 기능에 대한 개요를 제공하는 [Azure Monitor 개요](/azure/azure-monitor/overview)문서로 시작합니다. 다음 섹션에서는 Azure 컴퓨터 학습과 Azure 모니터를 사용하는 세부 정보를 제공하여 이 정보를 기반으로 합니다.

Azure [모니터와](/azure/azure-monitor/platform/usage-estimated-costs)관련된 비용을 이해하려면 사용 및 예상 비용을 참조하십시오. 데이터가 Azure 모니터에 표시되는 데 걸리는 시간을 이해하려면 [데이터 수집 시간 로그](/azure/azure-monitor/platform/data-ingestion-time)를 참조하십시오.

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure 기계 학습의 데이터 모니터링

Azure Machine Learning은 Azure 리소스의 모니터링 데이터에 설명된 다른 Azure 리소스와 동일한 종류의 [모니터링 데이터를](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)수집합니다. Azure 기계 학습에서 만든 로그 및 메트릭에 대한 자세한 참조는 [Azure 기계 학습 모니터링 데이터 참조를](monitor-resource-reference.md) 참조하십시오.

## <a name="analyzing-metric-data"></a>메트릭 데이터 분석

**Azure 모니터** 메뉴에서 메트릭을 열어 Azure 기계 학습에 대한 메트릭을 분석할 수 **있습니다.** 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작을](/azure/azure-monitor/platform/metrics-getting-started) 참조하십시오.

Azure 기계 학습에 대한 모든 메트릭은 네임스페이스 **기계 학습 서비스 작업 영역에 있습니다.**

![기계 학습 서비스 작업 영역을 선택한 메트릭 탐색기](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>필터링 및 분할

측정기준을 지원하는 측정항목의 경우 차원 값을 사용하여 필터를 적용할 수 있습니다. 예를 들어 의 **클러스터 이름에** 대한 활성 `cpu-cluster` **코어** 필터링. 

측정항목을 측정기준별로 분할하여 측정항목의 여러 세그먼트가 서로 비교하는 방식을 시각화할 수도 있습니다. 예를 들어 파이프라인 **단계 유형을** 분할하여 파이프라인에 사용되는 단계 유형의 수를 확인합니다.

필터링 및 분할에 대한 자세한 내용은 [Azure Monitor의 고급 기능을](/azure/azure-monitor/platform/metrics-charts)참조하십시오.

## <a name="alerts"></a>경고

**Azure 모니터** 메뉴에서 경고를 열어 Azure 기계 학습에 대한 경고에 액세스할 수 **있습니다.** [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리를](/azure/azure-monitor/platform/alerts-metric) 참조하여 경고 생성에 대한 자세한 내용을 확인합니다.

다음 표에는 Azure 기계 학습에 대한 공통 및 권장 메트릭 경고 규칙이 나열되어 있습니다.

| 경고 유형 | 조건 | 설명 |
|:---|:---|:---|
| 모델 배포 실패 | 집계 유형: 합계, 연산자: 임계값보다 크다: 0 | 하나 이상의 모델 배포에 실패한 경우 |
| 할당량 사용률 | 집계 유형: 평균, 연산자: 임계값보다 크며 임계값: 90| 할당량 사용률이 90%를 초과하는 경우 |
| 사용할 수 없는 노드 | 집계 유형: 합계, 연산자: 임계값보다 크다: 0 | 하나 이상의 사용할 수 없는 노드가 있는 경우 |

## <a name="configuration"></a>Configuration

> [!IMPORTANT]
> __Azure 기계 학습에 대한 메트릭은 구성할 필요가 없으며__자동으로 수집되며 모니터링 및 경고를 위해 메트릭 탐색기에서 사용할 수 있습니다.

진단 설정을 추가하여 다음 기능을 구성할 수 있습니다.

* Azure 저장소 계정에 로그 및 메트릭 정보를 보관합니다.
* Azure 이벤트 허브로 로그 및 메트릭 정보를 스트리밍합니다.
* Azure 모니터 로그 분석에 로그 및 메트릭 정보를 보냅니다.

이러한 설정을 사용하도록 설정하려면 추가 Azure 서비스(저장소 계정, 이벤트 허브 또는 로그 분석)가 필요하며, 이로 인해 비용이 증가할 수 있습니다. 예상 비용을 계산하려면 [Azure 가격 계산기를](https://azure.microsoft.com/pricing/calculator)방문하십시오.

진단 설정 만들기에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 만들기 설정 만들기를](/azure/azure-monitor/platform/diagnostic-settings)참조하십시오.

Azure 기계 학습에 대해 다음 로그를 구성할 수 있습니다.

| Category | 설명 |
|:---|:---|
| AmlCompute클러스터이벤트 | Azure 기계 학습의 이벤트가 클러스터를 계산합니다. |
| AmlCompute클러스터노드이벤트 | Azure 기계 학습 계산 클러스터 내의 노드의 이벤트입니다. |
| 아믈컴테스잡이벤트 | Azure 기계 학습 계산에서 실행 중인 작업의 이벤트입니다. |

> [!NOTE]
> 진단 설정에서 측정항목을 사용하도록 설정하면 차원 정보는 현재 저장소 계정, 이벤트 허브 또는 로그 분석으로 전송되는 정보의 일부로 포함되지 않습니다.

## <a name="analyzing-log-data"></a>로그 데이터 분석

Azure 모니터 로그 분석을 사용하려면 진단 구성을 만들고 __Log Analytics로 정보 보내기를__사용하도록 설정해야 합니다. 자세한 내용은 [구성](#configuration) 섹션을 참조하십시오.

Azure Monitor Logs의 데이터는 테이블에 저장되며 각 테이블에는 고유한 속성 집합이 있습니다. Azure 기계 학습은 다음 테이블에 데이터를 저장합니다.

| 테이블 | 설명 |
|:---|:---|
| AmlCompute클러스터이벤트 | Azure 기계 학습의 이벤트가 클러스터를 계산합니다. |
| AmlCompute클러스터노드이벤트 | Azure 기계 학습 계산 클러스터 내의 노드의 이벤트입니다. |
| 아믈컴테스잡이벤트 | Azure 기계 학습 계산에서 실행 중인 작업의 이벤트입니다. |

> [!IMPORTANT]
> Azure 기계 학습 메뉴에서 **로그를** 선택하면 쿼리 범위가 현재 작업 영역으로 설정된 상태로 Log Analytics가 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함됩니다. 다른 데이터베이스의 데이터 또는 다른 Azure 서비스의 데이터가 포함된 쿼리를 실행하려면 **Azure Monitor** 메뉴에서 **로그를 선택합니다.** 자세한 내용은 [Azure 모니터 로그 분석에서 로그 쿼리 범위 및 시간 범위를](/azure/azure-monitor/log-query/scope/) 참조하십시오.

로그 및 메트릭에 대한 자세한 참조는 [Azure 기계 학습 모니터링 데이터 참조를](monitor-resource-reference.md)참조하십시오.

### <a name="sample-queries"></a>샘플 쿼리

다음은 Azure 기계 학습 리소스를 모니터링하는 데 사용할 수 있는 쿼리입니다. 

+ 지난 5일 동안 실패한 작업 가져오기:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ 특정 작업 이름에 대한 레코드 를 가져옵니다.

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ VM 크기가 Standard_D1_V2 클러스터에 대해 지난 5일 동안 클러스터 이벤트를 가져옵니다.

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ 지난 8일 동안 노드할당 받기:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>다음 단계

- 로그 및 메트릭에 대한 참조는 [Azure 기계 학습 모니터링 데이터 참조를](monitor-resource-reference.md)참조하십시오.
- Azure 기계 학습과 관련된 할당량 작업에 대한 자세한 내용은 [Azure 리소스에 대한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조하십시오.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure 모니터를 통해 Azure 리소스 모니터링을](/azure/azure-monitor/insights/monitor-azure-resource)참조하십시오.
