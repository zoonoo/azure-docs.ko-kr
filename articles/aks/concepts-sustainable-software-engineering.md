---
title: 개념 - AKS(Azure Kubernetes Services)의 지속 가능한 소프트웨어 엔지니어링
description: AKS(Azure Kubernetes Services)의 지속 가능한 소프트웨어 엔지니어링에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: e179b49879b78b2bab738407984c0f50d161114b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572687"
---
# <a name="sustainable-software-engineering-principles-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 지속 가능한 소프트웨어 엔지니어링 원칙

지속 가능한 소프트웨어 엔지니어링 원칙은 지속 가능한 애플리케이션을 정의, 빌드, 실행하는 데 도움이 되는 역량 집합입니다. 전반적인 목표는 애플리케이션의 모든 측면에 대한 탄소 발자국을 줄이는 것입니다. [지속 가능한 소프트웨어 엔지니어링 원칙][principles-sse]에는 지속 가능한 소프트웨어 엔지니어링 원칙에 대한 개요가 포함되어 있습니다.

지속 가능한 소프트웨어 엔지니어링에 대해 이해하는 데 중요한 것은 우선 순위와 초점의 변화입니다. 대부분의 경우에는 빠른 성능과 짧은 대기 시간에 초점을 두는 방식으로 소프트웨어가 설계되고 실행됩니다. 지속 가능한 소프트웨어 엔지니어링은 최대한 탄소 배출량을 줄이는 데 초점을 두는 것입니다. 경우에 따라 지속 가능한 소프트웨어 엔지니어링 원칙을 적용하면 전체 네트워크 이동 감소 등을 통해 더 빠른 성능을 제공하거나 대기 시간을 줄일 수 있습니다. 다른 경우에는 탄소 배출량을 줄이면 우선 순위가 낮은 워크로드를 지연하는 등의 방법으로 성능이 저하되거나 대기 시간이 길어질 수 있습니다. 애플리케이션에 지속 가능한 소프트웨어 엔지니어링 원칙 적용을 고려하기 전에 애플리케이션의 우선 순위, 요구 사항, 장단점을 검토합니다.

## <a name="measure-and-optimize"></a>측정 및 최적화

AKS 클러스터의 탄소 발자국을 줄이려면 클러스터의 리소스를 사용하는 방법을 이해해야 합니다. [Azure Monitor][azure-monitor]는 메모리 및 CPU 사용량과 같은 클러스터의 리소스 사용에 대한 세부 정보를 제공합니다. 이 데이터는 클러스터의 탄소 발자국을 줄이고 변경의 영향을 관찰하는 결정을 내리는 데 도움이 될 수 있습니다. [Microsoft 지속 가능성 계산기][sustainability-calculator]를 설치하여 모든 Azure 리소스의 탄소 발자국을 확인할 수도 있습니다.

## <a name="increase-resource-utilization"></a>리소스 사용률 증가

탄소 발자국을 줄이는 한 가지 방법은 컴퓨팅 리소스에 대한 유휴 시간의 양을 줄이는 것입니다. 유휴 시간을 줄이면 컴퓨팅 리소스의 사용률이 늘어납니다. 예를 들어 클러스터에 4개의 노드가 있고 각 노드가 50% 용량으로 실행되는 경우 4개의 노드는 모두 50%의 사용되지 않는 용량을 유휴 상태로 유지합니다. 클러스터를 3개 노드로 줄인 경우 동일한 워크로드를 수행하면 3개의 노드가 67% 용량으로 실행되어 사용되지 않는 용량이 각 노드에서 33%로 줄어들고 사용률이 늘어납니다.

> [!IMPORTANT]
> 클러스터의 리소스 변경을 고려할 때 클러스터의 핵심 시스템 구성 요소에 대한 안정성을 유지할 만큼 충분한 리소스가 [시스템 풀][system-pools]에 있는지 확인합니다. 클러스터가 불안정해질 수 있는 수준까지 클러스터의 리소스를 줄일 수는 없습니다.

클러스터의 사용률을 검토한 후에는 [여러 노드 풀][multiple-node-pools]에서 제공하는 기능을 사용하는 것이 좋습니다. [노드 크기 조정][node-sizing]을 사용하여 특정 CPU 및 메모리 프로필로 노드 풀을 정의할 수 있으므로 워크로드 요구에 맞게 노드를 조정할 수 있습니다. 워크로드 요구 사항에 맞게 노드 크기를 조정하면 적은 노드를 더 높은 사용률로 실행하는 데 도움이 될 수 있습니다. 또한 클러스터의 [크기 조정][scale] 방법을 구성하고 [수평 Pod 자동 크기 조정기][scale-horizontal] 및 [클러스터 자동 크기 조정기][scale-auto]를 사용하여 구성에 따라 클러스터를 자동으로 확장할 수 있습니다. 클러스터 스케일링 방법을 제어하면 클러스터의 워크로드에 대한 변경 내용을 유지하면서 모든 노드를 높은 사용률로 실행하는 데 도움이 될 수 있습니다. 워크로드가 갑작스러운 중단 또는 종료를 허용하는 경우 Azure 내에서 유휴 용량을 활용하기 위해 [스폿 풀][spot-pools]을 사용할 수 있습니다. 예를 들어, 일괄 작업 또는 개발 환경에서 스폿 풀이 원활하게 작동할 수 있습니다.

사용률을 높이면 과도한 노드를 줄일 수 있으므로 [각 노드의 리소스 예약][resource-reservations]에 사용되는 에너지를 줄일 수 있습니다.

또한 애플리케이션의 Kubernetes 매니페스트에서 CPU 및 메모리 *요청* 및 *한도* 를 검토합니다. 메모리 및 CPU의 값을 낮추면 클러스터에서 다른 워크로드를 실행하는 데 더 많은 메모리와 CPU를 사용할 수 있습니다. 낮은 CPU와 메모리를 사용하여 더 많은 워크로드를 실행하는 경우 클러스터는 더 조밀하게 할당되므로 사용률이 높아집니다. 애플리케이션의 CPU 및 메모리를 줄일 때 이러한 값을 너무 낮게 설정하면 애플리케이션의 동작이 저하되거나 불안정해질 수 있습니다. CPU 및 메모리 *요청* 과 *한도* 를 변경하기 전에 일부 벤치마킹 테스트를 실행하여 이러한 값이 적절하게 설정되어 있는지 파악하는 것이 좋습니다. 또한 애플리케이션이 불안정해지는 수준까지 이러한 값을 줄이지는 마세요.

## <a name="reduce-network-travel"></a>네트워크 이동 감소

클러스터와의 요청 및 응답이 이동해야 하는 거리를 줄이면 일반적으로 네트워킹 디바이스의 전력 소비량과 탄소 배출량이 줄어듭니다. 네트워크 트래픽을 검토한 후 네트워크 트래픽 소스에 가까운 [지역에][regions] 클러스터를 만드는 것이 좋습니다. [Azure Traffic Manager][azure-traffic-manager]를 사용하여 가장 가까운 클러스터 및 [근접 배치 그룹][proiximity-placement-groups]으로 트래픽을 라우팅하는 데 도움을 줄 수 있습니다.

> [!IMPORTANT]
> 클러스터의 네트워킹 변경을 고려할 때 워크로드 요구 사항 충족을 포기하면서까지 네트워크 이동을 줄이지 마세요. 예를 들어 [가용성 영역][availability-zones]을 사용하면 클러스터에서 더 많은 네트워크 이동이 발생하지만 워크로드 요구 사항을 처리하기 위해 이 기능을 사용해야 할 수 있습니다.

## <a name="demand-shaping"></a>수요 셰이핑

가능하면 클러스터의 리소스에 대한 수요를 초과 용량을 사용할 수 있는 시간 또는 지역으로 이동하는 것이 좋습니다. 예를 들어, 일괄 작업을 실행하거나 [스폿 풀][spot-pools]을 사용하기 위해 시간 또는 지역을 변경하는 것이 좋습니다. 또한 큐를 사용하여 즉각적인 처리가 필요하지 않은 실행 중인 작업을 연기하도록 애플리케이션을 리팩터링하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에 설명된 AKS의 기능에 대해 자세히 알아보세요.

* [여러 노드 풀][multiple-node-pools]
* [노드 크기 조정][node-sizing]
* [클러스터 크기 조정][scale]
* [Horizontal Pod Autoscaler][scale-horizontal]
* [클러스터 자동 크기 조정기][scale-auto]
* [스폿 풀][spot-pools]
* [시스템 풀][system-pools]
* [리소스 예약][resource-reservations]
* [근접 배치 그룹][proiximity-placement-groups]
* [가용성 영역][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principles-sse]: https://docs.microsoft.com/learn/modules/sustainable-software-engineering-overview/