---
title: Azure Machine Learning 데이터 참조 모니터링 | Microsoft Docs
description: Azure Machine Learning 모니터링에 대 한 참조 설명서입니다. 수집 되 고 Azure Monitor에서 사용할 수 있는 데이터 & 리소스에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/02/2020
ms.openlocfilehash: d231de8e262194cd7dda2462a3f54d7d531eb63c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542085"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Azure machine learning 데이터 참조 모니터링

Azure Machine Learning 작업 영역에서 Azure Monitor 하 여 수집 된 데이터 및 리소스에 대해 알아봅니다. 모니터링 데이터를 수집 하 고 분석 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md) 을 참조 하세요.

## <a name="metrics"></a>메트릭

이 섹션에서는 Azure Machine Learning에 대해 수집 된 자동으로 수집 된 모든 플랫폼 메트릭을 나열 합니다. 이러한 메트릭에 대 한 리소스 공급자는 [MachineLearningServices/작업 영역](../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces)입니다.

**모델링**

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| 모델 배포 실패 | 개수 | 실패 한 모델 배포의 수입니다. |
| 모델 배포가 시작 됨 | 개수 | 시작 된 모델 배포의 수입니다. |
| 모델 배포 성공 | 개수 | 성공한 모델 배포의 수입니다. |
| 모델 레지스터가 실패 했습니다. | 개수 | 실패 한 모델 등록 수입니다. |
| 모델 등록 성공 | 개수 | 성공한 모델 등록 수입니다. |

**할당량**

할당량 정보는 Azure Machine Learning 계산 전용입니다.

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| 활성 코어 | 개수 | 활성 계산 코어의 수입니다. |
| 활성 노드 | 개수 | 활성 노드 수입니다. |
| 유휴 코어 | 개수 | 유휴 계산 코어의 수입니다. |
| 유휴 노드 | 개수 | 유휴 계산 노드 수입니다. |
| 코어 탈퇴 | 개수 | 종료 되는 코어 수입니다. |
| 노드 종료 | 개수 | 종료 노드 수입니다. |
| 선점 코어 | 개수 | 선점 되는 코어 수입니다. |
| 선점된 노드 | 개수 | 선점 된 노드 수입니다. |
| 할당량 사용률 비율 | 백분율 | 사용 된 할당량의 백분율입니다. |
| 총 코어 | 개수 | 총 코어입니다. |
| 총 노드 | 개수 | 총 노드입니다. |
| 사용할 수 없는 코어 | 개수 | 사용할 수 없는 코어의 수입니다. |
| 사용할 수 없는 노드 | 개수 | 사용할 수 없는 노드 수입니다. |

**리소스**

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| CpuUtilization | 백분율 | 실행/작업 중 지정 된 노드에 대해 사용 된 CPU의 양 (%)입니다. 이 메트릭은 노드에서 작업을 실행 하는 경우에만 게시 됩니다. 한 작업에서 하나 이상의 노드를 사용할 수 있습니다. 이 메트릭은 노드당 게시 됩니다. |
| GpuUtilization | 백분율 | 실행/작업 중 지정 된 노드에 대해 사용 된 GPU의 비율입니다. 한 노드에 하나 이상의 Gpu가 있을 수 있습니다. 이 메트릭은 노드당 GPU 당 게시 됩니다. |

**Run**

학습 실행에 대 한 정보입니다.

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| 완료 된 실행 | 개수 | 완료 된 실행 수입니다. |
| 실패 한 실행 | 개수 | 실패 한 실행 수입니다. |
| 실행 시작 | 개수 | 시작 된 실행의 수입니다. |

## <a name="metric-dimensions"></a>메트릭 차원

메트릭 차원에 대 한 자세한 내용은 [다차원 메트릭](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics)을 참조 하세요.

Azure Machine Learning에는 해당 메트릭과 관련 된 다음과 같은 차원이 있습니다.

| 차원 | 설명 |
| ---- | ---- |
| 클러스터 이름 | 계산 클러스터 리소스의 이름입니다. 모든 할당량 메트릭에 사용할 수 있습니다. |
| Vm 제품군 이름 | 클러스터에서 사용 하는 VM 제품군의 이름입니다. 할당량 사용률에 사용할 수 있습니다. |
| Vm 우선 순위 | VM의 우선 순위입니다. 할당량 사용률에 사용할 수 있습니다.
| CreatedTime | CpuUtilization 및 GpuUtilization에만 사용할 수 있습니다. |
| DeviceId | 장치 (GPU)의 ID입니다. GpuUtilization에만 사용할 수 있습니다. |
| NodeId | 작업이 실행 되는 위치에 생성 된 노드의 ID입니다. CpuUtilization 및 GpuUtilization에만 사용할 수 있습니다. |
| RunId | 실행/작업의 ID입니다. CpuUtilization 및 GpuUtilization에만 사용할 수 있습니다. |
| ComputeType | 실행에 사용 되는 계산 형식입니다. 완료 된 실행, 실패 한 실행 및 시작 된 실행에 대해서만 사용할 수 있습니다. |
| PipelineStepType | 실행에 사용 되는 [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?preserve-view=true&view=azure-ml-py) 의 형식입니다. 완료 된 실행, 실패 한 실행 및 시작 된 실행에 대해서만 사용할 수 있습니다. |
| PublishedPipelineId | 실행에 사용 되는 게시 된 파이프라인의 ID입니다. 완료 된 실행, 실패 한 실행 및 시작 된 실행에 대해서만 사용할 수 있습니다. |
| RunType | 실행 유형입니다. 완료 된 실행, 실패 한 실행 및 시작 된 실행에 대해서만 사용할 수 있습니다. |

RunType 차원의 유효한 값은 다음과 같습니다.

| 값 | 설명 |
| ----- | ----- |
| 실험 | 비 파이프라인 실행 |
| PipelineRun | 단계별 실행의 부모인 파이프라인 실행입니다. |
| Strun | 파이프라인 단계에 대 한 실행입니다. |
| ReusedStepRun | 이전 실행을 다시 사용할 파이프라인 단계에 대 한 실행입니다. |

## <a name="activity-log"></a>활동 로그

다음 표에서는 활동 로그에서 만들 수 있는 Azure Machine Learning 관련 된 작업을 보여 줍니다.

| 작업(Operation) | Description |
|:---|:---|
| Machine Learning 작업 영역을 만들거나 업데이트 합니다. | 작업 영역을 만들거나 업데이트 했습니다. |
| CheckComputeNameAvailability | 계산 이름이 이미 사용 중인지 확인 합니다. |
| 계산 리소스를 만들거나 업데이트 합니다. | 계산 리소스를 만들거나 업데이트 했습니다. |
| 계산 리소스를 삭제 합니다. | 계산 리소스가 삭제 되었습니다. |
| 암호 나열 | Machine Learning 작업 영역에 대 한 암호 나열 됨 |

## <a name="resource-logs"></a>리소스 로그

이 섹션에서는 Azure Machine Learning 작업 영역에 대해 수집할 수 있는 리소스 로그 유형을 나열 합니다.

리소스 공급자 및 유형: [MachineLearningServices/작업 영역](../azure-monitor/platform/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| 범주 | 표시 이름 |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>스키마

에서 다음 스키마를 사용 하 고 있습니다 Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents 테이블

| 속성 | Description |
|:--- |:---|
| TimeGenerated | 로그 항목이 생성 된 시간 |
| OperationName | 로그 이벤트와 연결 된 작업의 이름입니다. |
| 범주 | 로그 이벤트의 이름, AmlComputeClusterNodeEvent |
| JobId | 제출 된 작업의 ID입니다. |
| ExperimentId | 실험의 ID |
| ExperimentName | 실험 이름 |
| CustomerSubscriptionId | 실험 및 작업을 제출 하는 SubscriptionId |
| WorkspaceName | Machine learning 작업 영역 이름 |
| ClusterName | 클러스터의 이름 |
| ProvisioningState | 작업 제출 상태 |
| ResourceGroupName | 리소스 그룹의 이름입니다. |
| JobName | 작업의 이름입니다. |
| ClusterId | 클러스터의 ID |
| EventType | 작업 이벤트의 유형입니다. 예를 들어 JobSubmitted Jobsubmitted, Jobsubmitted, Jobsubmitted. |
| ExecutionState | 작업의 상태 (실행)입니다. 예: 큐에 대기, 실행 중, 성공, 실패 |
| ErrorDetails | 작업 오류에 대 한 세부 정보 |
| CreationApiVersion | 작업을 만드는 데 사용 되는 Api 버전 |
| ClusterResourceGroupName | 클러스터의 리소스 그룹 이름 |
| TFWorkerCount | TF worker 수 |
| TFParameterServerCount | TF 매개 변수 서버 수 |
| ToolType | 사용 되는 도구 형식 |
| RunInContainer | 컨테이너 내에서 작업을 실행 해야 하는지 설명 하는 플래그입니다. |
| JobErrorMessage | 작업 오류에 대 한 자세한 메시지 |
| NodeId | 작업이 실행 되는 위치에 생성 된 노드의 ID입니다. |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents 테이블

| 속성 | Description |
|:--- |:--- |
| TimeGenerated | 로그 항목이 생성 된 시간 |
| OperationName | 로그 이벤트와 연결 된 작업의 이름입니다. |
| 범주 | 로그 이벤트의 이름, AmlComputeClusterNodeEvent |
| ProvisioningState | 클러스터의 프로 비전 상태 |
| ClusterName | 클러스터의 이름 |
| ClusterType | 클러스터의 유형입니다. |
| CreatedBy | 클러스터를 만든 사용자 |
| CoreCount | 클러스터의 코어 수 |
| VmSize | 클러스터의 Vm 크기 |
| VmPriority | 클러스터 전용/LowPriority 내에서 만든 노드의 우선 순위 |
| ScalingType | 클러스터 크기 조정 수동/자동 크기 조정 |
| InitialNodeCount | 클러스터의 초기 노드 수 |
| MinimumNodeCount | 클러스터의 최소 노드 수 |
| MaximumNodeCount | 클러스터의 최대 노드 수 |
| NodeDeallocationOption | 노드의 할당을 취소 하는 방법 |
| 게시자 | 클러스터 유형 게시자 |
| 제안 | 클러스터가 생성 되는 제품 |
| SKU | 클러스터 내에서 만든 노드/v m의 Sku |
| 버전 | 노드/v m이 생성 되는 동안 사용 된 이미지의 버전 |
| SubnetId | 클러스터의 SubnetId |
| AllocationState | 클러스터 할당 상태 |
| CurrentNodeCount | 클러스터의 현재 노드 수 |
| TargetNodeCount | 확장/축소 하는 동안 클러스터의 대상 노드 수 |
| EventType | 클러스터를 만드는 동안 발생 하는 이벤트의 유형입니다. |
| NodeIdleTimeSecondsBeforeScaleDown | 클러스터가 축소 되기까지 유휴 시간 (초) |
| PreemptedNodeCount | 클러스터의 선점 노드 수 |
| IsResizeGrow | 클러스터가 확장 됨을 나타내는 플래그입니다. |
| VmFamilyName | 클러스터 내에서 만들 수 있는 노드의 VM 제품군 이름 |
| LeavingNodeCount | 클러스터의 노드 수를 종료 합니다. |
| UnusableNodeCount | 클러스터의 노드 수를 사용할 수 없음 |
| IdleNodeCount | 클러스터의 유휴 노드 수 |
| RunningNodeCount | 클러스터의 노드 수를 실행 하는 중 |
| PreparingNodeCount | 클러스터의 노드 수를 준비 하는 중 |
| QuotaAllocated | 클러스터에 할당 된 할당량 |
| QuotaUtilized | 클러스터의 사용 된 할당량 |
| AllocationStateTransitionTime | 한 상태에서 다른 상태로 전환 |
| ClusterErrorCodes | 클러스터를 만들거나 크기를 조정 하는 동안 받은 오류 코드 |
| CreationApiVersion | 클러스터를 만드는 동안 사용 된 Api 버전 |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents 테이블

| 속성 | Description |
|:--- |:--- |
| TimeGenerated | 로그 항목이 생성 된 시간 |
| OperationName | 로그 이벤트와 연결 된 작업의 이름입니다. |
| 범주 | 로그 이벤트의 이름, AmlComputeClusterNodeEvent |
| ClusterName | 클러스터의 이름 |
| NodeId | 만든 클러스터 노드의 ID |
| VmSize | 노드의 Vm 크기 |
| VmFamilyName | 노드가 속한 Vm 제품군 |
| VmPriority | 전용/LowPriority를 만든 노드의 우선 순위입니다. |
| 게시자 | Vm 이미지의 게시자입니다. 예: microsoft-dsvm |
| 제안 | VM 만들기와 관련 된 제안 |
| SKU | 만든 노드/v m의 Sku |
| 버전 | 노드/v m이 생성 되는 동안 사용 된 이미지의 버전 |
| ClusterCreationTime | 클러스터가 생성 된 시간 |
| ResizeStartTime | 클러스터 확장/축소 시작 시간 |
| ResizeEndTime | 클러스터 확장/축소 종료 시간 |
| NodeAllocationTime | 노드가 할당 된 시간 |
| NodeBootTime | 노드가 부팅 된 시간 |
| StartTaskStartTime | 태스크가 노드에 할당 되 고 시작 된 시간 |
| StartTaskEndTime | 노드에 할당 된 작업이 종료 된 시간 |
| TotalE2ETimeInSeconds | 노드가 활성 상태 였던 총 시간 |


## <a name="see-also"></a>참조

- Azure Machine Learning 모니터링에 대 한 설명은 [monitoring Azure Machine Learning](monitor-azure-machine-learning.md) 를 참조 하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/insights/monitor-azure-resource.md)을 참조하세요.
