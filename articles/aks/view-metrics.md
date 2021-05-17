---
title: AKS(Azure Kubernetes Service)에 대한 클러스터 메트릭 보기
description: AKS(Azure Kubernetes Service)에 대한 클러스터 메트릭을 봅니다.
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105975960"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 클러스터 메트릭 보기

AKS는 API 서버 및 클러스터 자동 크기 조정기를 포함하는 컨트롤 플레인과 클러스터 노드에 대한 일련의 메트릭을 제공합니다. 이러한 메트릭을 사용하여 클러스터의 상태를 모니터링하고 문제를 해결할 수 있습니다. Azure Portal을 사용하여 클러스터에 대한 메트릭을 볼 수 있습니다.

> [!NOTE]
> 이러한 AKS 클러스터 메트릭은 [Kubernetes에서 제공하는 메트릭][kubernetes-metrics]의 하위 집합과 겹칩니다.

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Azure Portal을 사용하여 AKS 클러스터에 대한 메트릭 보기

AKS 클러스터에 대한 메트릭을 보려면 다음을 수행합니다.

1. [Azure Portal][azure-portal]에 로그인하고 AKS 클러스터로 이동합니다.
1. *모니터링* 아래의 왼쪽에서 *메트릭* 을 선택합니다.
1. 보려는 메트릭에 대한 차트를 만듭니다. 예를 들어 차트 보고서를 만들려면 다음을 수행합니다.
    1. *범위* 에서 클러스터를 선택합니다.
    1. *메트릭 네임스페이스* 에서 *컨테이너 서비스(관리형) 표준 메트릭* 을 선택합니다.
    1. *메트릭* 의 *Pod* 에서 *단계별 Pod 수* 를 선택합니다.
    1. *집계* 에서 *평균* 을 선택합니다.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{대체 텍스트}":::

위의 예는 *myAKSCluster* 의 평균 Pod 수에 대한 메트릭을 보여줍니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

다음 클러스터 메트릭을 사용할 수 있습니다.

| 이름 | 그룹 | ID | Description |
| --- | --- | --- | ---- |
| 처리 중인 요청 수 | API 서버(미리 보기) |apiserver_current_inflight_requests | 요청 종류당 API 서버에서 현재 처리 중인 활성 상태의 최대 요청 수입니다. |
| 클러스터 상태 | 클러스터 자동 크기 조정기(미리 보기) | cluster_autoscaler_cluster_safe_to_autoscale | 클러스터 자동 크기 조정기가 클러스터에서 작업을 수행하는지 여부를 확인합니다. |
| 규모 축소 휴지 | 클러스터 자동 크기 조정기(미리 보기) | cluster_autoscaler_scale_down_in_cooldown | 규모 축소가 휴지 상태인지 여부를 확인합니다. 이 시간 동안에는 노드가 제거되지 않습니다. |
| 불필요한 노드 | 클러스터 자동 크기 조정기(미리 보기) | cluster_autoscaler_unneeded_nodes_count | 클러스터 자동 크기 조정기는 이러한 노드를 삭제 후보로 표시하며 결과적으로는 삭제됩니다. |
| 예약되지 않은 Pod | 클러스터 자동 크기 조정기(미리 보기) | cluster_autoscaler_unschedulable_pods_count | 현재 클러스터에서 예약되지 않은 Pod의 수입니다. |
| 관리 클러스터에서 사용 가능한 cpu 코어의 총 수 | 노드 | kube_node_status_allocatable_cpu_cores | 관리 클러스터에서 사용 가능한 총 CPU 코어의 수입니다. |
| 관리 클러스터에서 사용 가능한 총 메모리 양 | 노드 | kube_node_status_allocatable_memory_bytes | 관리 클러스터에서 사용 가능한 총 메모리 양입니다. |
| 다양한 노드 조건에 대한 상태 | 노드 | kube_node_status_condition | 다양한 노드 조건에 대한 상태 |
| CPU 사용량 밀리코어 | 노드(미리 보기) | node_cpu_usage_millicores | 클러스터 전체에서 집계된 CPU 사용률(밀리코어)에 대한 측정입니다. |
| CPU 사용 백분율 | 노드(미리 보기) | node_cpu_usage_percentage | 클러스터 전체에서 집계된 평균 CPU 사용률(백분율) 측정값입니다. |
| 메모리 RSS 바이트 | 노드(미리 보기) | node_memory_rss_bytes | 사용되는 컨테이너 RSS 메모리(바이트)입니다. |
| 메모리 RSS(백분율) | 노드(미리 보기) | node_memory_rss_percentage | 사용되는 컨테이너 RSS 메모리(%)입니다. |
| 메모리 작업 집합 바이트 | 노드(미리 보기) | node_memory_working_set_bytes | 사용되는 컨테이너 작업 집합 메모리(바이트)입니다. |
| 메모리 작업 집합 백분율 | 노드(미리 보기) | node_memory_working_set_percentage | 사용되는 컨테이너 작업 집합 메모리(백분율)입니다. |
| 사용되는 디스크 바이트 | 노드(미리 보기) | node_disk_usage_bytes | 디바이스에서 사용되는 디스크 공간 (바이트)입니다. |
| 사용되는 디스크 백분율 | 노드(미리 보기) | node_disk_usage_percentage | 디바이스에서 사용되는 디스크 공간(백분율)입니다. |
| 네트워크 입력 바이트 | 노드(미리 보기) | node_network_in_bytes | 네트워크에서 수신한 바이트입니다. |
| 네트워크 출력 바이트 | 노드(미리 보기) | node_network_out_bytes | 네트워크에서 전송한 바이트입니다. |
| 준비 상태인 Pod 수 | Pod | kube_pod_status_ready | *준비* 상태인 Pod 수입니다. |
| 단계별 Pod 수 | Pod | kube_pod_status_phase | 단계별 Pod 수입니다. |

> [!IMPORTANT]
> 미리 보기로 제공되는 메트릭은 해당 이름과 설명을 포함하여 미리 보기 상태인 메트릭을 업데이트하거나 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AKS에 대한 클러스터 메트릭 외에도 AKS 클러스터와 함께 Azure Monitor를 사용할 수 있습니다. AKS와 함께 Azure Monitor를 사용하는 방법에 대한 자세한 내용은 [컨테이너용 Azure Monitor][aks-azure-monitory]을 참조하세요.

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/