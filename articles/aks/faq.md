---
title: AKS(Azure Kubernetes Service)에 대한 질문과 대답
description: AKS(Azure Kubernetes Service)에 대한 일반적인 질문에 대한 답변을 찾아보세요.
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: ba4ceaf0d7f9e3b344b2a6efbb84f2145c4a2f65
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86275719"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-currently-provide-aks"></a>현재 어떤 Azure 지역에서 AKS를 제공하나요?

사용 가능한 지역의 전체 목록은 [AKS 지역 및 가용성][aks-regions]을 참조하세요.

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>여러 지역에 AKS 클러스터를 분산할 수 있나요?

아니요. AKS 클러스터는 지역 리소스이며 여러 지역에 걸쳐 있을 수 없습니다. 여러 영역을 포함하는 아키텍처를 만드는 방법에 대한 지침은 [비즈니스 연속성 및 재해 복구에 대한 모범 사례][bcdr-bestpractices]를 참조하세요.

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>여러 가용성 영역에 AKS 클러스터를 분산할 수 있나요?

예. [지원하는 지역][az-regions]에 있는 하나 이상의 [가용성 영역][availability-zones]에서 AKS 클러스터를 배포할 수 있습니다.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kubernetes API 서버에 액세스할 수 있는 사용자를 제한할 수 있나요?

예. API 서버에 대한 액세스를 제한하는 옵션에는 다음 두 가지가 있습니다.

- API 서버에 대한 퍼블릭 엔드포인트를 유지하지만 신뢰할 수 있는 IP 범위 세트로 액세스를 제한하려는 경우 [API Server 권한 있는 IP 범위][api-server-authorized-ip-ranges]를 사용합니다.
- API 서버를 가상 네트워크 내에서*만* 액세스할 수 있도록 제한하려면 [프라이빗 클러스터][private-clusters]를 사용합니다.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>단일 클러스터에서 다른 크기의 VM을 사용할 수 있나요?

예, [여러 노드 풀][multi-node-pools]을 만들어 AKS 클러스터에서 다른 크기의 가상 머신을 사용할 수 있습니다.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 자동으로 야간 일정에 따라 클러스터의 Linux 노드에 보안 패치를 적용합니다. 하지만 필요에 따라 해당 Linux 노드를 다시 부팅해야 합니다. 노드를 다시 부팅하기 위한 몇 가지 옵션은 다음과 같습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터 업그레이드는 자동으로 [노드를 차단 및 드레이닝][cordon-drain]한 후 최신 Ubuntu 이미지와 새 패치 버전 또는 부 Kubernetes 버전을 사용하여 새 노드를 다시 온라인 상태로 전환합니다. 자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade]를 참조하세요.
- Kubernetes를 위한 오픈 소스 재부팅 디먼인 [Kured](https://github.com/weaveworks/kured)를 사용합니다. Kured는 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)으로 실행되며 각 노드에서 다시 부팅해야 함을 나타내는 파일이 있는지 모니터링합니다. 클러스터 전체에서 업그레이드와 동일한 [차단 및 드레이닝 프로세스][cordon-drain]를 사용하여 클러스터 전체에서 OS 재부팅이 관리됩니다.

kured 사용에 대한 자세한 내용은 [AKS에서 노드에 보안 및 커널 업데이트 적용][node-updates-kured]을 참조하세요.

### <a name="windows-server-nodes"></a>Windows Server 노드

Windows Server 노드의 경우 Windows 업데이트가 자동으로 실행되고 최신 업데이트를 적용하지 않습니다. Windows 업데이트 릴리스 주기와 사용자 고유의 유효성 검사 프로세스에 대한 정기적인 일정에 따라, 클러스터에서 업그레이드를 수행하고 AKS 클러스터에서 Windows Server 노드 풀을 업그레이드해야 합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행하는 노드를 만든 다음, 이전 노드를 제거합니다. 이 프로세스에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조하세요.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

AKS는 가상 머신 확장 집합, 가상 네트워크 및 Managed Disks를 포함하는 다양 한 Azure 인프라 리소스를 기준으로 합니다. 따라서 AKS에서 제공하는 관리형 Kubernetes 환경 내에서 Azure 플랫폼의 여러 핵심 기능을 활용할 수 있습니다. 예를 들어, 대부분의 Azure Virtual Machine 유형은 AKS에서 직접 사용할 수 있으며, Azure Reservations를 사용하여 이러한 리소스에 대해 자동으로 할인 혜택을 받을 수 있습니다.

이 아키텍처를 지원하기 위해 각 AKS 배포는 다음 두 리소스 그룹에 걸쳐 있습니다.

1. 첫 번째 리소스 그룹을 만듭니다. 이 그룹에는 Kubernetes 서비스 리소스만 포함됩니다. AKS 리소스 공급자는 배포하는 동안 두 번째 리소스 그룹을 자동으로 만듭니다. 두 번째 리소스 그룹의 예는 *MC_myResourceGroup_myAKSCluster_eastus*입니다. 이 두 번째 리소스 그룹의 이름을 지정하는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.
1. *노드 리소스 그룹*으로 알려져 있는 두 번째 리소스 그룹에는 클러스터와 연결된 인프라 리소스의 모든 항목이 포함됩니다. 이러한 리소스에는 Kubernetes 노드 VM, 가상 네트워킹 및 스토리지가 포함됩니다. 기본적으로 노드 리소스 그룹에는 *MC_myResourceGroup_myAKSCluster_eastus*와 같은 이름이 지정됩니다. AKS는 클러스터가 삭제될 때마다 노드 리소스를 자동으로 삭제하므로 클러스터의 수명 주기를 공유하는 리소스에만 사용해야 합니다.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>AKS 노드 리소스 그룹에 고유한 이름을 지정할 수 있나요?

예. 기본적으로 AKS는 노드 리소스 그룹 이름을 *MC_resourcegroupname_clustername_location*으로 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 [aks-preview][aks-preview-cli] Azure CLI 확장 버전 0.3.2 이상을 설치합니다. [az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만들 때 *--node-resource-group* 매개 변수를 사용하고 리소스 그룹의 이름을 지정합니다. [Azure Resource Manager 템플릿을 사용][aks-rm-template]하여 AKS 클러스터를 배포하는 경우 *nodeResourceGroup* 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

* 자체 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다.
* 클러스터를 만들 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다.

노드 리소스 그룹으로 작업할 때는 다음을 수행할 수 없습니다.

* 노드 리소스 그룹에 기존 리소스 그룹을 지정합니다.
* 노드 리소스 그룹에 다른 구독을 지정합니다.
* 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
* 노드 리소스 그룹 내에서 관리 리소스의 이름을 지정합니다.
* 노드 리소스 그룹 내에서 관리 리소스의 Azure에서 만든 태그를 수정하거나 삭제합니다. 다음 섹션에서 추가 정보를 참조하세요.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>노드 리소스 그룹에서 태그 및 AKS 리소스의 다른 속성을 수정할 수 있나요?

노드 리소스 그룹에서 Azure에서 만든 태그 및 기타 리소스 속성을 수정하거나 삭제하는 경우 크기 조정 및 업그레이드 오류와 같은 예기치 않은 결과가 발생할 수 있습니다. AKS를 사용 하면 최종 사용자가 만든 사용자 지정 태그를 만들고 수정할 수 있습니다. 예를 들어 사업부 또는 비용 센터를 할당하기 위해 사용자 지정 태그를 만들거나 수정할 수 있습니다. 이는 관리 되는 리소스 그룹의 범위를 사용 하 여 Azure 정책을 만들어 달성할 수 있습니다.

그러나 AKS 클러스터에서 노드 리소스 그룹의 리소스에 대해 **Azure에서 만든 태그** 를 수정 하는 작업은 SLO (서비스 수준 목표)를 중단 하는 지원 되지 않는 작업입니다. 자세한 내용은 [AKS는 서비스 수준 계약을 제공합니까?](#does-aks-offer-a-service-level-agreement)를 참조하세요.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS가 지원하는 Kubernetes 허용 컨트롤러는 무엇인가요? 허용 컨트롤러를 추가하거나 제거할 수 있나요?

AKS는 다음과 같은 [허용 컨트롤러][admission-controllers]를 지원합니다.

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

현재, AKS에서 허용 컨트롤러 목록을 수정할 수 없습니다.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>AKS에서 허용 컨트롤러 Webhook를 사용할 수 있나요?

예, AKS에서 허용 컨트롤러 Webhook를 사용할 수 있습니다. **제어 평면 레이블**로 표시된 내부 AKS 네임스페이스를 제외하는 것이 좋습니다. 예를 들어, Webhook 구성에 아래 내용을 추가합니다.

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>허용 컨트롤러 Webhook가 kube-system 및 내부 AKS 네임스페이스에 영향을 미칠 수 있나요?

시스템의 안정성을 보호하고 사용자 지정 허용 컨트롤러가 kube-system의 내부 서비스에 영향을 주지 않도록 하기 위해 네임스페이스 AKS에는 kube-system 및 AKS 내부 네임스페이스를 자동으로 제외하는 **Admissions Enforcer**가 있습니다. 이 서비스는 사용자 지정 허용 컨트롤러가 kube-system에서 실행되는 서비스에 영향을 주지 않도록 합니다.

사용자 지정 허용 webhook를 통해 적용해야 하는 기능을 kube-system에 배포하기(권장하지 않음) 위한 중요한 사용 사례가 있는 경우 Admissions Enforcer에서 무시하도록 아래 레이블 또는 주석을 추가할 수 있습니다.

레이블: ```"admissions.enforcer/disabled": "true"``` 또는 주석: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

현재, AKS는 기본적으로 Azure Key Vault와 통합되지 않습니다. 그러나 [CSI 비밀 저장소용 Azure Key Vault 공급자][csi-driver]를 통해 Kubernetes Pod에서 KeyVault 비밀로 직접 통합할 수 있습니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

예, Windows Server 컨테이너는 AKS에서 사용할 수 있습니다. AKS에서 Windows Server 컨테이너를 실행하려면 Windows Server를 게스트 OS로 실행하는 노드 풀을 만듭니다. Windows Server 컨테이너는 Windows Server 2019만 사용할 수 있습니다. 시작하려면 [Windows Server 노드 풀을 사용하여 AKS 클러스터 만들기][aks-windows-cli]를 참조하세요.

노드 풀에 대한 Windows Server 지원에는 Kubernetes 프로젝트의 업스트림 Windows Server에 적용되는 몇 가지 제한 사항이 포함됩니다. 이러한 제한 사항에 대한 자세한 내용은 [AKS의 Windows Server 컨테이너 제한 사항][aks-windows-limitations]을 참조하세요.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS는 서비스 수준 계약을 제공합니까?

AKS는 [작동 시간 SLA][uptime-sla]를 통해 SLA 보장 기능을 선택적 추가 기능으로 제공합니다.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>AKS 에이전트 노드에 Azure 예약 할인을 적용할 수 있나요?

AKS 에이전트 노드는 표준 Azure Virtual Machines로 청구되므로 AKS에서 사용하는 VM 크기에 대해 [Azure Reservations][reservation-discounts]를 구매한 경우 해당 할인이 자동으로 적용됩니다.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Azure 테넌트 간에 클러스터를 이동/마이그레이션할 수 있나요?

`az aks update-credentials` 명령을 사용하여 Azure 테넌트 간에 AKS 클러스터를 이동할 수 있습니다. [서비스 주체를 업데이트하거나 만들도록 선택](./update-credentials.md)의 지침을 따른 후 [새 자격 증명으로 AKS 클러스터 업데이트](./update-credentials.md#update-aks-cluster-with-new-service-principal-credentials)의 작업을 수행합니다.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>내 클러스터를 구독 간에 이동/마이그레이션할 수 있나요?

현재 구독 간의 클러스터 이동은 지원되지 않습니다.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>AKS 클러스터를 현재 Azure 구독에서 다른 구독으로 이동할 수 있나요? 

Azure 구독 간에 AKS 클러스터 및 연결 된 리소스를 이동 하는 것은 지원 되지 않습니다.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>AKS 클러스터 또는 AKS 인프라 리소스를 다른 리소스 그룹으로 이동 하거나 이름을 바꿀 수 있나요?

AKS 클러스터 및 관련 리소스의 이동 또는 이름 변경은 지원 되지 않습니다.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>클러스터 삭제가 오래 걸리는 이유는 무엇인가요? 

사용자 요청 시 대부분의 클러스터가 삭제됩니다. 일부 경우, 특히 고객이 자신의 리소스 그룹을 가져오거나 RG 간 태스크를 수행할 때 삭제에 추가 시간이 발생하거나 삭제가 실패할 수 있습니다. 삭제 시 문제가 발생하면 RG에 잠금이 없는지와 RG 외부의 모든 리소스와 RG의 연결이 끊어져 있는지를 한번 더 확인합니다.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>'NodeLost' 또는 '알 수 없음' 상태의 pod/배포가 있는 경우 클러스터를 여전히 업그레이드할 수 있나요?

가능하지만 AKS에서 이러한 업그레이드를 권장하지 않습니다. 클러스터의 상태가 알려져 있고 정상일 때 업그레이드를 수행하는 것이 가장 좋습니다.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>하나 이상의 노드가 있는 클러스터가 비정상 상태이거나 종료된 경우 업그레이드를 수행할 수 있나요?

아니요. 실패한 상태의 모든 노드를 삭제/제거합니다. 그렇지 않으면 업그레이드 전에 클러스터에서 해당 노드가 제거됩니다.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>클러스터 삭제를 실행했지만 오류가 표시됩니다. `[Errno 11001] getaddrinfo failed` 

가장 일반적으로 이 문제는 하나 이상의 NSG(네트워크 보안 그룹)가 여전히 사용 중이고 클러스터와 연결된 경우에 발생합니다.  이러한 NSG를 제거한 후 다시 시도하세요.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>업그레이드를 실행했지만 pod이 충돌 루프에 있습니다. 준비 상태 프로브가 실패하나요?

서비스 주체가 만료되지 않았는지 확인하세요.  참조 항목: [AKS 서비스 주체](./kubernetes-service-principal.md) 및 [AKS 업데이트 자격 증명](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>클러스터가 작동하지만 갑자기 LoadBalancer를 프로비저닝하거나 PVC를 탑재할 수 없습니다. 

서비스 주체가 만료되지 않았는지 확인하세요.  참조 항목: [AKS 서비스 주체](./kubernetes-service-principal.md) 및 [AKS 업데이트 자격 증명](./update-credentials.md).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>가상 머신 확장 집합 API를 사용하여 수동으로 크기를 조정할 수 있나요?

아니요, 가상 머신 확장 집합 API를 사용한 크기 조정 작업은 지원되지 않습니다. AKS API(`az aks scale`)를 사용하세요.

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>가상 머신 확장 집합을 사용하여 수동으로 0개 노드로 확장할 수 있나요?

아니요, 가상 머신 확장 집합 API를 사용한 크기 조정 작업은 지원되지 않습니다.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>모든 VM을 중지하거나 할당 취소할 수 있나요?

AKS에는 이러한 구성을 견디고 복구할 수 있는 복원 메커니즘이 있지만 권장되는 구성은 아닙니다.

## <a name="can-i-use-custom-vm-extensions"></a>사용자 지정 VM 확장을 사용할 수 있나요?

AKS는 관리형 서비스이며 IaaS 리소스 조작은 지원되지 않습니다. 사용자 지정 구성 요소 등을 설치하려면 Kubernetes API 및 메커니즘을 활용하세요. 예를 들어 DaemonSets를 활용하여 필수 구성 요소를 설치합니다.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
