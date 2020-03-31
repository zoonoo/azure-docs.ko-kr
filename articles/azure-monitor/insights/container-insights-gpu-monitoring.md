---
title: 컨테이너에 대한 Azure 모니터로 GPU 모니터링 구성 | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너에 대한 Azure 모니터를 통해 NVIDIA 및 AMD GPU 지원 노드를 통해 Kubernetes 모니터링 클러스터를 구성하는 방법에 대해 설명합니다.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373311"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>컨테이너에 대한 Azure 모니터를 통해 GPU 모니터링 구성

에이전트 버전 *ciprod03022019로*시작하여 컨테이너 통합 에이전트용 Azure 모니터는 이제 GPU 인식 Kubernetes 클러스터 노드에서 GPU(그래픽 처리 장치) 사용량을 모니터링하고 GPU 리소스를 요청하고 사용하는 포드/컨테이너를 모니터링합니다.

## <a name="supported-gpu-vendors"></a>지원되는 GPU 공급업체

컨테이너용 Azure Monitor는 다음 GPU 공급업체의 모니터링 GPU 클러스터를 지원합니다.

- [엔비디아](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

컨테이너용 Azure Monitor는 노드에서 GPU 사용량 모니터링을 자동으로 시작하고 GPU는 60초 간격으로 다음 메트릭을 수집하고 **InsightMetrics** 테이블에 저장하여 포드 및 워크로드를 요청합니다.

|메트릭 이름 |미터법 차원(태그) |설명 |
|------------|------------------------|------------|
|컨테이너Gpu듀티사이클 |container.azm.ms/clusterId, container.azm.ms/clusterName, 컨테이너이름, gpuId, gpuModel, gpuVendor|GPU가 사용 중/컨테이너에 대해 사용 중이던 지난 샘플 기간(60초)에 대한 시간 비율입니다. 듀티 사이클은 1과 100 사이의 숫자입니다. |
|컨테이너GPU제한 |container.azm.ms/clusterId, container.azm.ms/clusterName, 컨테이너이름 |각 컨테이너는 제한을 하나 이상의 GPU로 지정할 수 있습니다. GPU의 일부를 요청하거나 제한할 수 없습니다. |
|컨테이너Gpu요청 |container.azm.ms/clusterId, container.azm.ms/clusterName, 컨테이너이름 |각 컨테이너는 하나 이상의 GPU를 요청할 수 있습니다. GPU의 일부를 요청하거나 제한할 수 없습니다.|
|컨테이너Gpumemory총바이트 |container.azm.ms/clusterId, container.azm.ms/clusterName, 컨테이너이름, gpuId, gpuModel, gpuVendor |특정 컨테이너에 사용할 수 있는 바이트 내 GPU 메모리 양입니다. |
|컨테이너GPU메모리사용바이트 |container.azm.ms/clusterId, container.azm.ms/clusterName, 컨테이너이름, gpuId, gpuModel, gpuVendor |특정 컨테이너에서 사용하는 바이트 내 GPU 메모리 양입니다. |
|노드GPUAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Kubernetes에서 사용할 수 있는 노드의 GPU 수입니다. |
|노드Gpu용량 |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |노드의 총 GPU 수입니다. |

## <a name="gpu-performance-charts"></a>GPU 성능 차트 

컨테이너용 Azure Monitor에는 모든 클러스터에 대한 GPU 통합 문서로 표의 앞에 나열된 메트릭에 대해 미리 구성된 차트가 포함됩니다. 왼쪽 창에서 **통합 문서를** 선택하고 인사이트의 **통합 문서** 드롭다운 목록에서 AKS 클러스터에서 직접 GPU 통합 문서 **노드 GPU를** 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [AZURE Kubernetes 서비스(AKS)에서 계산 집약적인 워크로드에 GPU 사용을 사용하여](../../aks/gpu-cluster.md) GPU 지원 노드가 포함된 AKS 클러스터를 배포하는 방법을 알아봅니다.

- [마이크로소프트 Azure에서 GPU 최적화 VM SUS에](../../virtual-machines/sizes-gpu.md)대해 자세히 알아보십시오.

- [Kubernetes의 GPU 지원을](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) 검토하여 클러스터의 하나 이상의 노드에서 GPU를 관리하기 위한 Kubernetes 실험 적 지원에 대해 자세히 알아봅니다.