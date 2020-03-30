---
title: 데이터 참조 모니터링 | 마이크로 소프트 문서
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에 대해 수집되고 Azure 모니터에서 사용할 수 있는 데이터 및 리소스에 대해 알아봅니다. Azure Monitor는 Azure Machine Learning 작업 영역에 대한 데이터를 수집 및 표시하며 메트릭을 보고 경고를 설정하고 기록된 데이터를 분석할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927558"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Azure 기계 학습 모니터링 데이터 참조

Azure 기계 학습 작업 영역에서 Azure Monitor에서 수집한 데이터 및 리소스에 대해 알아봅니다. 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [Azure 기계 학습](monitor-azure-machine-learning.md) 모니터링을 참조하십시오.

## <a name="resource-logs"></a>리소스 로그

다음 표에는 Azure 컴퓨터 학습 리소스 로그가 Azure 모니터 로그 또는 Azure 저장소에서 수집될 때의 속성을 나열합니다.

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents 테이블

| 속성 | 설명 |
|:--- |:---|
| TimeGenerated | 로그 항목이 생성된 시간 |
| OperationName | 로그 이벤트와 연결된 작업의 이름 |
| Category | 로그 이벤트 이름, AmlComputeClusterNodeEvent |
| JobId | 제출된 작업의 ID |
| 실험 ID | 실험의 ID |
| 실험 이름 | 실험 이름 |
| 고객 구독ID | 실험 및 작업이 제출된 구독ID |
| 작업 영역 이름 | 기계 학습 작업 영역의 이름 |
| ClusterName | 클러스터 이름 |
| ProvisioningState | 작업 제출 상태 |
| ResourceGroupName | 리소스 그룹의 이름 |
| JobName | 작업 이름 |
| ClusterId | 클러스터의 ID |
| EventType | 작업 이벤트 유형(예: 작업 제출, 작업 실행, 작업 실패, 작업 성공 등) |
| ExecutionState | 작업 상태(실행) ( 예: 큐에 대기 중, 실행 중, 성공, 실패) |
| ErrorDetails | 작업 오류의 세부 정보 |
| 크리에이션ApiVersion | 작업을 만드는 데 사용되는 Api 버전 |
| 클러스터리소스 그룹 이름 | 클러스터의 리소스 그룹 이름 |
| TF워커카운트 | TF 근로자 수 |
| TF매개 변수 서버 카운트 | TF 매개 변수 서버 수 |
| 도구 유형 | 사용되는 공구 유형 |
| 런인 컨테이너 | 컨테이너 내에서 작업을 실행해야 하는지 설명하는 플래그 지정 |
| 작업 오류 메시지 | 작업 오류의 자세한 메시지 |
| NodeId | 작업이 실행 중인 노드의 ID |

### <a name="amlcomputeclusterevents-table"></a>AmlCompute클러스터이벤트 테이블

| 속성 | 설명 |
|:--- |:--- |
| TimeGenerated | 로그 항목이 생성된 시간 |
| OperationName | 로그 이벤트와 연결된 작업의 이름 |
| Category | 로그 이벤트 이름, AmlComputeClusterNodeEvent |
| ProvisioningState | 클러스터의 프로비저닝 상태 |
| ClusterName | 클러스터 이름 |
| 클러스터 유형 | 클러스터 유형 |
| CreatedBy | 클러스터를 만든 사용자 |
| CoreCount | 클러스터의 코어 수 |
| VmSize | 클러스터의 Vm 크기 |
| Vm우선 순위 | 클러스터 내부에 생성된 노드의 우선 순위 전용/낮은 우선 순위 |
| 크기 조정 유형 | 클러스터 크기 조정 수동/자동 유형 |
| 초기 노드 카운트 | 클러스터의 초기 노드 수 |
| 최소노드카운트 | 클러스터의 최소 노드 수 |
| 최대노드카운트 | 클러스터의 최대 노드 수 |
| 노드딜로케이션 옵션 | 노드를 할당 할당 하는 방법 |
| 게시자 | 클러스터 유형의 게시자 |
| 제안 | 클러스터가 생성되는 오퍼 |
| SKU | 클러스터 내부에 생성된 노드/VM의 스쿠 |
| 버전 | 노드/VM이 생성되는 동안 사용되는 이미지 버전 |
| 서브넷 Id | 클러스터의 서브넷Id |
| 할당상태 | 클러스터 할당 상태 |
| 현재노드카운트 | 클러스터의 현재 노드 수 |
| 대상 노드 카운트 | 확장/축소하는 동안 클러스터의 대상 노드 수 |
| EventType | 클러스터 를 만드는 동안 이벤트 유형입니다. |
| 노드들레타임초전스케일다운 | 클러스터 축소 전의 유휴 시간(초) |
| PreemptedNodeCount | 클러스터의 선점 노드 수 |
| 이스리사이즈그로우 | 클러스터가 확장중임을 나타내는 플래그 |
| VmFamilyName | 클러스터 내에서 만들 수 있는 노드의 VM 패밀리 이름 |
| 이탈노드카운트 | 클러스터의 노드 수 를 남깁니다. |
| UnusableNodeCount | 클러스터의 사용할 수 없는 노드 수 |
| IdleNodeCount | 클러스터의 유휴 노드 수 |
| RunningNodeCount | 클러스터의 노드 수 실행 |
| 준비노드카운트 | 클러스터의 노드 수 준비 |
| 할당량 할당 | 클러스터에 할당된 할당량 |
| 할당량 활용 | 클러스터의 활용 할당량 |
| 할당상태전환시간 | 한 상태에서 다른 상태로전환 시간 |
| 클러스터오류코드 | 클러스터 생성 또는 크기 조정 중에 받은 오류 코드 |
| 크리에이션ApiVersion | 클러스터를 만드는 동안 사용되는 Api 버전 |

### <a name="amlcomputeclusternodeevents-table"></a>AmlCompute클러스터노드이벤트 테이블

| 속성 | 설명 |
|:--- |:--- |
| TimeGenerated | 로그 항목이 생성된 시간 |
| OperationName | 로그 이벤트와 연결된 작업의 이름 |
| Category | 로그 이벤트 이름, AmlComputeClusterNodeEvent |
| ClusterName | 클러스터 이름 |
| NodeId | 생성된 클러스터 노드의 ID |
| VmSize | 노드의 Vm 크기 |
| VmFamilyName | 노드가 속한 Vm 패밀리 |
| Vm우선 순위 | 생성된 노드의 우선 순위/낮은 우선 순위 |
| 게시자 | VM 이미지의 게시자(예: microsoft-dsvm) |
| 제안 | VM 생성과 관련된 오퍼 |
| SKU | 생성된 노드/VM의 스쿠프 |
| 버전 | 노드/VM이 생성되는 동안 사용되는 이미지 버전 |
| 클러스터 생성 시간 | 클러스터가 생성된 시간 |
| 크기 조정시작 시간 | 클러스터 확장/축소가 시작된 시간 |
| 크기 조정 시간 | 클러스터 확장/축소가 종료된 시간 |
| 노드 할당 시간 | 노드가 할당된 시간 |
| 노드부트타임 | 노드가 부팅된 시간 |
| 시작 작업 시작 시간 | 작업이 노드에 할당되어 시작된 시간 |
| 시작 작업 종료 시간 | 노드에 할당된 작업이 종료된 시간 |
| 토탈2E타임인세컨스 | 총 시간 노드가 활성 상태였습니다. |

### <a name="metrics"></a>메트릭

다음 표에는 Azure 기계 학습에 대해 수집된 플랫폼 메트릭이 나열되어 모든 메트릭이 네임스페이스 **Azure Machine Learning 작업 영역에**저장됩니다.

**모델**

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| 모델 배포 실패 | 개수 | 실패한 모델 배포 수입니다. |
| 모델 배포 시작 | 개수 | 모델 배포가 시작된 횟수입니다. |
| 모델 배포 성공 | 개수 | 성공한 모델 배포 수입니다. |
| 모델 레지스터실패 | 개수 | 실패한 모델 등록 수입니다. |
| 모델 레지스터 성공 | 개수 | 성공한 모델 등록 수입니다. |

**할당량**

할당량 정보는 Azure 기계 학습 계산전용입니다.

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| 액티브 코어 | 개수 | 활성 계산 코어 의 수입니다. |
| 활성 노드 | 개수 | 활성 노드의 수입니다. |
| 유휴 코어 | 개수 | 유휴 계산 코어의 수입니다. |
| 유휴 노드 | 개수 | 유휴 계산 노드의 수입니다. |
| 코어 를 남기기 | 개수 | 코어를 떠나는 수입니다. |
| 노드 를 떠나기 | 개수 | 떠나는 노드의 수입니다. |
| 선점코어 | 개수 | 선점코어 수입니다. |
| 선점된 노드 | 개수 | 선점된 노드의 수입니다. |
| 할당량 사용률 | 백분율 | 사용된 할당량의 백분율입니다. |
| 총 코어 | 개수 | 총 코어입니다. |
| 총 노드 | 개수 | 총 노드입니다. |
| 사용할 수 없는 코어 | 개수 | 사용할 수 없는 코어의 수입니다. |
| 사용할 수 없는 노드 | 개수 | 사용할 수 없는 노드의 수입니다. |

다음은 할당량 메트릭을 필터링하는 데 사용할 수 있는 차원입니다.

| 차원 | 사용 가능한 메트릭 | 설명 |
| ---- | ---- | ---- |
| 클러스터 이름 | 모든 할당량 메트릭 | 계산 인스턴스의 이름입니다. |
| Vm 패밀리 이름 | 할당량 사용률 | 클러스터에서 사용하는 VM 패밀리의 이름입니다. |
| Vm 우선 순위 | 할당량 사용률 | VM의 우선 순위입니다.

**실행**

교육 실행에 대한 정보입니다.

| 메트릭 | 단위 | 설명 |
| ----- | ----- | ----- |
| 완료된 실행 | 개수 | 완료된 실행 수입니다. |
| 실패한 실행 | 개수 | 실패한 실행 수입니다. |
| 시작 된 실행 | 개수 | 시작된 실행 수입니다. |

다음은 실행 메트릭을 필터링하는 데 사용할 수 있는 차원입니다.

| 차원 | 설명 |
| ---- | ---- |
| 계산 유형 | 실행이 사용된 계산 유형입니다. |
| 파이프라인 스텝타입 | 실행에 사용되는 [파이프라인스텝의](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) 유형입니다. |
| 게시된 파이프라인Id | 실행에 사용된 게시된 파이프라인의 ID입니다. |
| 실행 유형 | 실행 유형입니다. |

RunType 차원의 유효한 값은 다음과 같습니다.

| 값 | 설명 |
| ----- | ----- |
| 실험 | 파이프라인이 아닌 실행. |
| 파이프라인 실행 | StepRun의 부모인 파이프라인 실행입니다. |
| 단계 실행 | 파이프라인 단계에 대한 실행입니다. |
| 재사용 스텝런 | 이전 실행을 다시 사용하는 파이프라인 단계에 대한 실행입니다. |

## <a name="see-also"></a>관련 항목

- [Azure 기계 학습 모니터링에](monitor-azure-machine-learning.md) 대한 설명은 Azure 기계 학습 모니터링을 참조하십시오.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure 모니터를 통해 Azure 리소스](/azure/azure-monitor/insights/monitor-azure-resource) 모니터링을 참조하십시오.
