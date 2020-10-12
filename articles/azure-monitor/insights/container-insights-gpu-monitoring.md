---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 GPU 모니터링 구성 | Microsoft Docs
description: 이 문서에서는 Kubernetes Azure Monitor 클러스터를 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 4c6044d407dc4abd0e69bac0190cc19c901022c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569699"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 GPU 모니터링 구성

에이전트 버전 *ciprod03022019*부터 컨테이너 통합 에이전트는 이제 Gpu 인식 Kubernetes 클러스터 노드에서 gpu (그래픽 처리 단위) 사용 모니터링을 지원 하 고 gpu 리소스를 요청 하 고 사용 하는 pod/컨테이너를 모니터링 합니다.

## <a name="supported-gpu-vendors"></a>지원 되는 GPU 공급 업체

컨테이너의 Azure Monitor는 다음 GPU 공급 업체의 GPU 클러스터 모니터링을 지원 합니다.

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [뛰어난](https://github.com/RadeonOpenCompute/k8s-device-plugin)

컨테이너에 대 한 Azure Monitor는 60 초 간격으로 다음 메트릭을 수집 하 여 **InsightMetrics** 테이블에 저장 함으로써 노드의 gpu 사용량 모니터링과 pod 및 작업 부하를 자동으로 시작 합니다.

>[!NOTE]
>Gpu 노드를 사용 하 여 클러스터를 프로 비전 한 후 gpu 작업을 실행 하기 위해 AKS에서 요구 하는 [gpu 드라이버가](../../aks/gpu-cluster.md) 설치 되어 있는지 확인 합니다. 컨테이너 Azure Monitor는 노드에서 실행 되는 GPU 드라이버 pod를 통해 GPU 메트릭을 수집 합니다. 

|메트릭 이름 |메트릭 차원 (태그) |설명 |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|GPU가 사용 중이 고 컨테이너에 대해 적극적으로 처리 되는 과거 샘플 기간 (60 초) 동안의 시간 비율입니다. 업무 주기는 1에서 100 사이의 숫자입니다. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |각 컨테이너는 한 개 이상의 Gpu로 한도를 지정할 수 있습니다. GPU의 일부를 요청 하거나 제한할 수 없습니다. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |각 컨테이너는 하나 이상의 Gpu를 요청할 수 있습니다. GPU의 일부를 요청 하거나 제한할 수 없습니다.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |특정 컨테이너에 사용할 수 있는 GPU 메모리 크기 (바이트)입니다. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |특정 컨테이너에서 사용 하는 GPU 메모리의 양 (바이트)입니다. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Kubernetes에서 사용할 수 있는 노드의 Gpu 수입니다. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |노드에 있는 Gpu의 총 수입니다. |

## <a name="gpu-performance-charts"></a>GPU 성능 차트 

컨테이너에 대 한 Azure Monitor에는 테이블 앞부분에 나열 된 메트릭에 대 한 미리 구성 된 차트가 모든 클러스터에 대 한 GPU 통합 문서로 포함 됩니다. 왼쪽 창에서 **통합 문서** 를 선택 하 고 정보에 있는 **통합 문서 보기** 드롭다운 목록에서 GPU 통합 문서 **노드 gpu** 를 직접 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- GPU 사용 노드가 포함 된 AKS 클러스터를 배포 하는 방법에 [대 한 자세한 내용은 Azure Kubernetes 서비스에서 계산 집약적인 작업에 Gpu 사용](../../aks/gpu-cluster.md) (AKS)을 참조 하세요.

- [Microsoft Azure에서 GPU에 최적화 된 VM sku](../../virtual-machines/sizes-gpu.md)에 대해 자세히 알아보세요.

- [Kubernetes의 gpu 지원](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) 을 검토 하 여 클러스터에 있는 하나 이상의 노드에서 gpu를 관리 하기 위한 Kubernetes 실험적 지원에 대해 자세히 알아보세요.
