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
ms.date: 11/04/2019
ms.openlocfilehash: e8a2a00014d0118301193337cc9bb544e4a07690
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770818"
---
# <a name="monitoring-azure-machine-learning"></a>모니터링 Azure Machine Learning

이 문서에서는 Azure Machine Learning에서 생성 하는 모니터링 데이터를 설명 합니다. 또한 Azure Monitor 사용 하 여 데이터를 분석 하 고 경고를 정의 하는 방법을 설명 합니다.

> [!TIP]
> 이 문서의 정보는 Azure Machine Learning에 대 한 모니터링을 설명 하므로 주로 관리자를 위한 것입니다. 데이터 과학자 또는 개발자 이며 모델 학습 실행과 관련 된 정보를 모니터링 하려는 경우 다음 문서를 참조 하세요.
>
> * [학습 실행 시작, 모니터링 및 취소](how-to-manage-runs.md)
> * [학습 실행에 대 한 로그 메트릭](how-to-track-experiments.md)
> * [MLflow를 사용 하 여 eexperiments 추적](how-to-use-mlflow.md)
> * [TensorBoard를 사용 하 여 실행 시각화](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning는 Azure에서 전체 스택 모니터링 서비스인 Azure Monitor를 사용 하 여 모니터링 데이터를 기록 합니다. Azure Monitor는 Azure 리소스를 모니터링 하는 완전 한 기능 집합을 제공 합니다. 또한 다른 클라우드 및 온-프레미스의 리소스도 모니터링할 수 있습니다.

모니터링 기능에 대 한 개요를 제공 하는 [Azure Monitor 개요](/azure/azure-monitor/overview)문서부터 시작 합니다. 다음 섹션에서는 Azure Machine Learning와 함께 Azure Monitor를 사용 하는 특성을 제공 하 여이 정보를 작성 합니다.

Azure Monitor와 관련 된 비용을 이해 하려면 [사용량 및 예상 비용](/azure/azure-monitor/platform/usage-estimated-costs)을 참조 하세요. 데이터가 Azure Monitor에 표시 되는 데 걸리는 시간을 이해 하려면 [로그 데이터 수집 시간](/azure/azure-monitor/platform/data-ingestion-time)을 참조 하세요.

## <a name="monitoring-data-from-azure-machine-learning"></a>Azure Machine Learning에서 데이터 모니터링

Azure Machine Learning는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집 하며, [azure 리소스의 데이터 모니터링](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data)에 설명 되어 있습니다. Azure Machine Learning에서 만든 로그 및 메트릭에 대 한 자세한 내용은 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md) 를 참조 하세요.

## <a name="analyzing-metric-data"></a>메트릭 데이터 분석

**Azure Monitor** 메뉴에서 **메트릭을** 열어 Azure Machine Learning에 대 한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](/azure/azure-monitor/platform/metrics-getting-started) 을 참조 하세요.

Azure Machine Learning에 대 한 모든 메트릭은 네임 스페이스 **Machine Learning 서비스 작업 영역**에 있습니다.

![Machine Learning 서비스 작업 영역을 선택 메트릭 탐색기](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>필터링 및 분할

차원을 지 원하는 메트릭의 경우 차원 값을 사용 하 여 필터를 적용할 수 있습니다. 예를 들어 `cpu-cluster`**클러스터 이름** 에 대 한 **활성 코어** 를 필터링 합니다. 

메트릭을 차원으로 분할 하 여 메트릭의 각 세그먼트가 서로 어떻게 다른 지를 시각화할 수도 있습니다. 예를 들어 파이프라인 **단계 유형을** 분할 하 여 파이프라인에서 사용 되는 단계 유형의 수를 확인 합니다.

필터링 및 분할에 대 한 자세한 내용은 [Azure Monitor의 고급 기능](/azure/azure-monitor/platform/metrics-charts)을 참조 하세요.

## <a name="alerts"></a>경고

**Azure Monitor** 메뉴에서 **경고** 를 열어 Azure Machine Learning에 대 한 경고에 액세스할 수 있습니다. 경고 만들기에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리를](/azure/azure-monitor/platform/alerts-metric) 참조 하세요.

다음 표에서는 Azure Machine Learning에 대 한 일반적인 메트릭 경고 규칙과 권장 메트릭을 보여 줍니다.

| 경고 유형 | 조건 | Description |
|:---|:---|:---|
| 모델 배포 실패 | 집계 유형: 합계, 연산자: 보다 큼, 임계값: 0 | 하나 이상의 모델 배포에 실패 한 경우 |
| 할당량 사용률 비율 | 집계 유형: 평균, 연산자: 보다 큼, 임계값: 90| 할당량 사용률 백분율이 90% 보다 큰 경우 |
| 사용할 수 없는 노드 | 집계 유형: 합계, 연산자: 보다 큼, 임계값: 0 | 사용할 수 없는 노드가 하나 이상 있는 경우 |

## <a name="configuration"></a>구성

> [!IMPORTANT]
> __Azure Machine Learning에 대 한 메트릭은 구성할 필요가 없으며__, 자동으로 수집 되 고 모니터링 및 경고를 위해 메트릭 탐색기에서 사용할 수 있습니다.

진단 설정을 추가 하 여 다음 기능을 구성할 수 있습니다.

* Azure 저장소 계정에 로그 및 메트릭 정보를 보관 합니다.
* Azure 이벤트 허브에 로그 및 메트릭 정보를 스트리밍합니다.
* 로그 및 메트릭 정보를 Azure Monitor Log Analytics 보냅니다.

이러한 설정을 사용 하려면 추가 Azure 서비스 (저장소 계정, 이벤트 허브 또는 Log Analytics)가 필요 하므로 비용이 늘어날 수 있습니다. 예상 비용을 계산 하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator)를 방문 하세요.

진단 설정 만들기에 대 한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기](/azure/azure-monitor/platform/diagnostic-settings)를 참조 하세요.

Azure Machine Learning에 대해 다음 로그를 구성할 수 있습니다.

| 범주 | Description |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 계산 클러스터의 이벤트입니다. |
| AmlComputeClusterNodeEvent | Azure Machine Learning 계산 클러스터 내의 노드에서 발생 한 이벤트입니다. |
| AmlComputeJobEvent | Azure Machine Learning compute에서 실행 되는 작업의 이벤트입니다. |

> [!NOTE]
> 진단 설정에서 메트릭을 사용 하도록 설정 하면 현재 저장소 계정, 이벤트 허브 또는 log analytics로 전송 되는 정보의 일부로 차원 정보가 포함 되지 않습니다.

## <a name="analyzing-log-data"></a>로그 데이터 분석

Azure Monitor Log Analytics를 사용 하려면 진단 구성을 만들고 __Log Analytics에 정보 보내기__를 사용 하도록 설정 해야 합니다. 자세한 내용은 [구성](#configuration) 섹션을 참조 하세요.

Azure Monitor 로그의 데이터는 테이블에 저장 되 고 각 테이블에는 고유한 속성 집합이 있습니다. Azure Machine Learning은 다음 테이블에 데이터를 저장 합니다.

| Table | Description |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning 계산 클러스터의 이벤트입니다. |
| AmlComputeClusterNodeEvent | Azure Machine Learning 계산 클러스터 내의 노드에서 발생 한 이벤트입니다. |
| AmlComputeJobEvent | Azure Machine Learning compute에서 실행 되는 작업의 이벤트입니다. |

> [!IMPORTANT]
> Azure Machine Learning 메뉴에서 **로그** 를 선택 하면 쿼리 범위가 현재 작업 영역으로 설정 된 Log Analytics 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함 됩니다. 다른 데이터베이스의 데이터 또는 다른 Azure 서비스의 데이터를 포함 하는 쿼리를 실행 하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택 합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](/azure/azure-monitor/log-query/scope/) 를 참조 하세요.

로그 및 메트릭에 대 한 자세한 내용은 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md)를 참조 하세요.

### <a name="sample-queries"></a>샘플 쿼리

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

## <a name="next-steps"></a>다음 단계

- 로그 및 메트릭에 대 한 참조는 [Azure Machine Learning 모니터링 데이터 참조](monitor-resource-reference.md)를 참조 하세요.
- Azure Machine Learning와 관련 된 할당량을 사용 하는 방법에 대 한 자세한 내용은 [Azure 리소스에 대 한 할당량 관리 및 요청](how-to-manage-quotas.md)을 참조 하세요.
- Azure 리소스 모니터링에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resource)을 참조 하세요.
