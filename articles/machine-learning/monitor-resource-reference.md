---
title: 데이터 참조 모니터링 | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에 대해 수집 되 고 Azure Monitor에서 사용할 수 있는 데이터 및 리소스에 대해 알아봅니다. Azure Monitor은 Azure Machine Learning 작업 영역에 대 한 데이터를 수집 및 표시 하 고, 메트릭을 보고, 경고를 설정 하 고, 기록 데이터를 분석할 수 있도록 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: 1abd52c98cb1fa6ebe1014fc7a65e756d038d683
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187599"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure machine learning 모니터링 데이터 참조

Azure Machine Learning 작업 영역에서 Azure Monitor 하 여 수집 된 데이터 및 리소스에 대해 알아봅니다. 모니터링 데이터를 수집 하 고 분석 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning 모니터링](monitor-azure-machine-learning.md) 을 참조 하세요.

## <a name="resource-logs"></a>리소스 로그

다음 표에서는 Azure Monitor 로그 또는 Azure Storage에서 수집 될 때 Azure Machine Learning 리소스 로그의 속성을 나열 합니다.

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
| EventType | 작업 이벤트 유형 (예: JobSubmitted 됨, Jobsubmitted, Jobsubmitted, Jobsubmitted 등)입니다. |
| ExecutionState | 작업 상태 (실행) (예: 큐에 대기, 실행 중, 성공, 실패) |
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
| 제품 | 클러스터가 생성 되는 제품 |
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
| 게시자 | Vm 이미지 (예: microsoft-dsvm)의 게시자 |
| 제품 | VM 만들기와 관련 된 제안 |
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

### <a name="metrics"></a>메트릭

다음 표에서는 Azure Machine Learning에 대해 수집 된 플랫폼 메트릭을 나열 합니다. 모든 메트릭은 네임 스페이스 **Azure Machine Learning 작업 영역**에 저장 됩니다.

**모델**

| 메트릭 | 단위 | Description |
| ----- | ----- | ----- |
| 모델 배포 실패 | 개수 | 실패 한 모델 배포의 수입니다. |
| 모델 배포가 시작 됨 | 개수 | 시작 된 모델 배포의 수입니다. |
| 모델 배포 성공 | 개수 | 성공한 모델 배포의 수입니다. |
| 모델 레지스터가 실패 했습니다. | 개수 | 실패 한 모델 등록 수입니다. |
| 모델 등록 성공 | 개수 | 성공한 모델 등록 수입니다. |

**양보다**

할당량 정보는 Azure Machine Learning 계산 전용입니다.

| 메트릭 | 단위 | Description |
| ----- | ----- | ----- |
| 활성 코어 | 개수 | 활성 계산 코어의 수입니다. |
| 활성 노드 | 개수 | 활성 노드 수입니다. |
| 유휴 코어 | 개수 | 유휴 계산 코어의 수입니다. |
| 유휴 노드 | 개수 | 유휴 계산 노드 수입니다. |
| 코어 탈퇴 | 개수 | 종료 되는 코어 수입니다. |
| 노드 종료 | 개수 | 종료 노드 수입니다. |
| 선점 코어 | 개수 | 선점 되는 코어 수입니다. |
| 선점 노드 | 개수 | 선점 된 노드 수입니다. |
| 할당량 사용률 비율 | 백분율 | 사용 된 할당량의 백분율입니다. |
| 총 코어 | 개수 | 총 코어입니다. |
| 총 노드 | 개수 | 총 노드입니다. |
| 사용할 수 없는 코어 | 개수 | 사용할 수 없는 코어의 수입니다. |
| 사용할 수 없는 노드 | 개수 | 사용할 수 없는 노드 수입니다. |

다음은 할당량 메트릭을 필터링 하는 데 사용할 수 있는 차원입니다.

| 차원 | 에서 사용 가능한 메트릭 | Description |
| ---- | ---- | ---- |
| 클러스터 이름 | 모든 할당량 메트릭 | 계산 인스턴스의 이름입니다. |
| Vm 제품군 이름 | 할당량 사용률 비율 | 클러스터에서 사용 하는 VM 제품군의 이름입니다. |
| Vm 우선 순위 | 할당량 사용률 비율 | VM의 우선 순위입니다.

**리소스**

| 메트릭 | 단위 | Description |
| ----- | ----- | ----- |
| CpuUtilization | 백분율 | 실행/작업 중 지정 된 노드에 대해 사용 된 CPU의 양 (%)입니다. 이 메트릭은 노드에서 작업을 실행 하는 경우에만 게시 됩니다. 한 작업에서 하나 이상의 노드를 사용할 수 있습니다. 이 메트릭은 노드당 게시 됩니다. |
| GpuUtilization | 백분율 | 실행/작업 중 지정 된 노드에 대해 사용 된 GPU의 비율입니다. 한 노드에 하나 이상의 Gpu가 있을 수 있습니다. 이 메트릭은 노드당 GPU 당 게시 됩니다. |

다음은 리소스 메트릭을 필터링 하는 데 사용할 수 있는 차원입니다.

| 차원 | Description |
| ----- | ----- |
| CreatedTime | |
| DeviceId | 장치 (GPU)의 ID입니다. GpuUtilization에만 사용할 수 있습니다. |
| NodeId | 작업이 실행 되는 위치에 생성 된 노드의 ID입니다. |
| RunId | 실행/작업의 ID입니다. |

**실행할지**

학습 실행에 대 한 정보입니다.

| 메트릭 | 단위 | Description |
| ----- | ----- | ----- |
| 완료 된 실행 | 개수 | 완료 된 실행 수입니다. |
| 실패 한 실행 | 개수 | 실패 한 실행 수입니다. |
| 실행 시작 | 개수 | 시작 된 실행의 수입니다. |

다음은 실행 메트릭을 필터링 하는 데 사용할 수 있는 차원입니다.

| 차원 | Description |
| ---- | ---- |
| 고 대 여 Etype | 실행에 사용 되는 계산 형식입니다. |
| PipelineStepType | 실행에 사용 되는 [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) 의 형식입니다. |
| PublishedPipelineId | 실행에 사용 되는 게시 된 파이프라인의 ID입니다. |
| RunType | 실행 유형입니다. |

RunType 차원의 유효한 값은 다음과 같습니다.

| 값 | Description |
| ----- | ----- |
| 실험 | 비 파이프라인 실행 |
| PipelineRun | 단계별 실행의 부모인 파이프라인 실행입니다. |
| Strun | 파이프라인 단계에 대 한 실행입니다. |
| ReusedStepRun | 이전 실행을 다시 사용할 파이프라인 단계에 대 한 실행입니다. |

## <a name="see-also"></a>참고 항목

- Azure Machine Learning 모니터링에 대 한 설명은 [monitoring Azure Machine Learning](monitor-azure-machine-learning.md) 를 참조 하세요.
- Azure 리소스 모니터링에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure 리소스 모니터링](/azure/azure-monitor/insights/monitor-azure-resource) 을 참조 하세요.
