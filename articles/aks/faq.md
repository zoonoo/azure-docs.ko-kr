---
title: AKS(Azure Kubernetes Service)에 대한 질문과 대답
description: AZURE Kubernetes 서비스(AKS)에 대한 몇 가지 일반적인 질문에 대한 답변을 찾아보십시오.
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497760"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-currently-provide-aks"></a>현재 AKS를 제공하는 Azure 리전은 무엇입니까?

사용 가능한 리전의 전체 목록은 [AKS 지역 및 가용성을][aks-regions]참조하십시오.

## <a name="does-aks-support-node-autoscaling"></a>AKS는 노드 자동 크기 조정 기능을 지원하나요?

예. 현재 미리 보기에서 AKS에서 에이전트 노드를 가로로 조정하는 기능을 사용할 수 있습니다. 지침은 [AKS의 응용 프로그램 요구 사항을 충족하도록 클러스터를 자동으로 확장합니다.][aks-cluster-autoscaler] AKS 자동 크기 조정은 [Kubernetes 자동 크기 조정기][auto-scaler]기반입니다.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>AKS를 기존 가상 네트워크에 배포할 수 있습니까?

예. [고급 네트워킹 기능을][aks-advanced-networking]사용하여 AKS 클러스터를 기존 가상 네트워크에 배포할 수 있습니다.

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kubernetes API 서버에 액세스할 수 있는 사람을 제한할 수 있습니까?

예. API 서버 인증 IP 범위를 사용하여 Kubernetes API 서버에 대한 액세스를 제한할 수 [있습니다.][api-server-authorized-ip-ranges]

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Kubernetes API 서버를 가상 네트워크 내에서만 액세스할 수 있도록 할 수 있습니까?

지금은 아니지만, 이것은 계획되어 있습니다. [AKS GitHub 리포지토리에서][private-clusters-github-issue]진행 상황을 추적할 수 있습니다.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>단일 클러스터에 서로 다른 VM 크기를 가질 수 있습니까?

예. [여러 노드 풀을][multi-node-pools]만들어 AKS 클러스터에서 다양한 가상 시스템 크기를 사용할 수 있습니다.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 야간 일정에 따라 클러스터의 Linux 노드에 보안 패치를 자동으로 적용합니다. 그러나 필요에 따라 해당 Linux 노드를 재부팅할 책임은 귀하에게 있습니다. 노드를 재부팅하기 위한 몇 가지 옵션이 있습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터는 [코돈과 드레인 노드를][cordon-drain] 자동으로 업그레이드한 다음 최신 우분투 이미지와 새 패치 버전 또는 마이너 Kubernetes 버전으로 새 노드를 온라인 상태가 합니다. 자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade]를 참조하세요.
- [Kured를](https://github.com/weaveworks/kured)사용 하 여, Kubernetes에 대 한 오픈 소스 재부팅 데몬. Kured는 [DaemonSet으로](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 실행되고 각 노드에서 재부팅이 필요하다는 파일이 있는지 모니터링합니다. 클러스터 전체에서 OS 재부팅은 클러스터 [업그레이드와 동일한 코돈 및 드레인 프로세스에][cordon-drain] 의해 관리됩니다.

kured 사용에 대한 자세한 내용은 [AKS에서 노드에 보안 및 커널 업데이트 적용][node-updates-kured]을 참조하세요.

### <a name="windows-server-nodes"></a>윈도우 서버 노드

현재 AKS에서 미리 보기 중인 Windows Server 노드의 경우 Windows 업데이트가 자동으로 실행되고 최신 업데이트를 적용하지 않습니다. Windows Update 릴리스 주기 및 자체 유효성 검사 프로세스에 대한 정기적인 일정에 따라 AKS 클러스터의 클러스터 및 Windows Server 풀에서 업그레이드를 수행해야 합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행한 다음 이전 노드를 제거하는 노드를 만듭니다. 이 프로세스에 대한 자세한 내용은 [AKS의 노드 풀 업그레이드를][nodepool-upgrade]참조하십시오.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

AKS는 가상 시스템 규모 집합, 가상 네트워크 및 관리 디스크를 비롯한 여러 Azure 인프라 리소스를 기반으로 합니다. 이를 통해 AKS에서 제공하는 관리되는 Kubernetes 환경 내에서 Azure 플랫폼의 많은 핵심 기능을 활용할 수 있습니다. 예를 들어 대부분의 Azure 가상 시스템 유형은 AKS와 함께 직접 사용할 수 있으며 Azure 예약은 해당 리소스에 대한 할인을 자동으로 받는 데 사용할 수 있습니다.

이 아키텍처를 사용하도록 설정하려면 각 AKS 배포는 두 개의 리소스 그룹에 걸쳐 있습니다.

1. 첫 번째 리소스 그룹을 만듭니다. 이 그룹에는 Kubernetes 서비스 리소스만 포함됩니다. AKS 리소스 공급자는 배포 하는 동안 두 번째 리소스 그룹을 자동으로 만듭니다. 두 번째 리소스 그룹의 예는 *MC_myResourceGroup_myAKSCluster_eastus.* 이 두 번째 리소스 그룹의 이름을 지정하는 방법에 대한 자세한 내용은 다음 섹션을 참조하십시오.
1. 노드 리소스 그룹이라고 하는 두 번째 리소스 *그룹에는*클러스터와 연결된 모든 인프라 리소스가 포함됩니다. 이러한 리소스에는 Kubernetes 노드 VM, 가상 네트워킹 및 스토리지가 포함됩니다. 기본적으로 노드 리소스 그룹에는 *MC_myResourceGroup_myAKSCluster_eastus*. AKS는 클러스터가 삭제될 때마다 노드 리소스를 자동으로 삭제하므로 클러스터의 수명 주기를 공유하는 리소스에만 사용해야 합니다.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>AKS 노드 리소스 그룹에 대한 내 이름을 제공할 수 있습니까?

예. 기본적으로 AKS는 노드 리소스 *그룹*MC_resourcegroupname_clustername_location 이름을 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 [aks-preview][aks-preview-cli] Azure CLI 확장 버전 *0.3.2* 이상을 설치합니다. [az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만들 때 *--node-resource-group* 매개 변수를 사용하고 리소스 그룹에 대한 이름을 지정합니다. Azure [리소스 관리자 템플릿을 사용하여][aks-rm-template] AKS 클러스터를 배포하는 경우 *nodeResourceGroup* 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

* 보조 리소스 그룹은 자체 구독의 Azure 리소스 공급자에 의해 자동으로 만들어집니다.
* 클러스터를 만들 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다.

노드 리소스 그룹으로 작업할 때 다음을 수행할 수 없습니다.

* 노드 리소스 그룹에 대한 기존 리소스 그룹을 지정합니다.
* 노드 리소스 그룹에 대해 다른 구독을 지정합니다.
* 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
* 노드 리소스 그룹 내에서 관리되는 리소스에 대한 이름을 지정합니다.
* 노드 리소스 그룹 내에서 관리되는 리소스의 태그를 수정하거나 삭제합니다. (다음 섹션의 추가 정보를 참조하십시오.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>노드 리소스 그룹에서 AKS 리소스의 태그 및 기타 속성을 수정할 수 있습니까?

노드 리소스 그룹에서 Azure에서 만든 태그 및 기타 리소스 속성을 수정하거나 삭제하면 크기 조정 및 업그레이드 오류와 같은 예기치 않은 결과가 발생할 수 있습니다. AKS를 사용하면 사용자 지정 태그를 만들고 수정할 수 있습니다. 예를 들어 비즈니스 단위 또는 비용 센터를 할당하기 위해 사용자 지정 태그를 만들거나 수정할 수 있습니다. AKS 클러스터의 노드 리소스 그룹 아래의 리소스를 수정하면 서비스 수준 목표(SLO)가 중단됩니다. 자세한 내용은 [AKS가 서비스 수준 계약을 제공합니까?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS가 지원하는 Kubernetes 허용 컨트롤러는 무엇인가요? 허용 컨트롤러를 추가하거나 제거할 수 있나요?

AKS는 다음과 같은 [허용 컨트롤러][admission-controllers]를 지원합니다.

- *NamespaceLifecycle*
- *LimitRanger*
- *서비스 계정*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*

현재 AKS의 입학 컨트롤러 목록을 수정할 수 없습니다.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

AKS는 현재 Azure 키 볼트와 기본적으로 통합되지 않습니다. 그러나 [Kubernetes에 대한 Azure 키 볼트 FlexVolume프로젝트를][keyvault-flexvolume] 통해 Kubernetes 포드에서 키 볼트 비밀로 직접 통합할 수 있습니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

예. Windows 서버 컨테이너는 미리 보기에서 사용할 수 있습니다. AKS에서 Windows Server 컨테이너를 실행하려면 Windows Server를 게스트 OS로 실행하는 노드 풀을 만듭니다. Windows 서버 컨테이너는 Windows Server 2019만 사용할 수 있습니다. 시작하려면 Windows [서버 풀이 있는 AKS 클러스터 만들기를][aks-windows-cli]참조하십시오.

노드 풀에 대한 Windows Server 지원에는 Kubernetes 프로젝트의 업스트림 Windows 서버의 일부인 몇 가지 제한 사항이 포함되어 있습니다. 이러한 제한 사항에 대한 자세한 내용은 [AKS 제한의 Windows 서버 컨테이너를][aks-windows-limitations]참조하십시오.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS는 서비스 수준 계약을 제공합니까?

서비스 수준 계약(SLA)에서 공급자는 게시된 서비스 수준이 충족되지 않는 경우 고객에게 서비스 비용을 상환하는 데 동의합니다. AKS는 무료이기 때문에 상환할 수 있는 비용은 없으므로 AKS에는 공식적인 SLA가 없습니다. 그러나 AKS는 Kubernetes API 서버의 가용성을 99.5% 이상 유지하려고 합니다.

Kubernetes 제어 평면의 가동 시간을 참조하는 AKS 서비스 가용성과 Azure 가상 컴퓨터에서 실행 중인 특정 워크로드의 가용성 간의 차이를 인식하는 것이 중요합니다. 제어 평면이 준비되지 않은 경우 제어 평면을 사용할 수 없지만 Azure VM에서 실행 중인 클러스터 워크로드는 계속 작동할 수 있습니다. Azure VM이 유료 리소스인 경우 재무 SLA가 지원합니다. Azure VM SLA에 대한 자세한 내용과 [가용성 영역과][availability-zones]같은 기능을 사용하여 가용성을 높이는 방법에 [대한 자세한 내용은 여기를](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) 참조하십시오.

## <a name="why-cant-i-set-maxpods-below-30"></a>maxPods를 30 미만으로 설정할 수 없는 이유는 무엇입니까?

AKS에서 Azure CLI `maxPods` 및 Azure 리소스 관리자 템플릿을 사용하여 클러스터를 만들 때 값을 설정할 수 있습니다. 그러나 Kubenet과 Azure CNI 모두 *최소 값(생성* 시 유효성 검사)이 필요합니다.

| 네트워킹 | 최소 | 최대 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| 쿠베넷 (주) | 30 | 110 |

AKS는 관리되는 서비스이므로 클러스터의 일부로 추가 기능 및 포드를 배포하고 관리합니다. 과거에는 사용자가 관리되는 `maxPods` 포드가 실행하는 데 필요한 값보다 낮은 값을 정의할 수 있었습니다(예: 30). AKS는 이제 이 수식을 사용하여 최소 포드 수를 계산합니다: (maxPods 또는 (maxPods * vm_count)) 관리되는 애드온 포드 최소를 >.

사용자는 최소 `maxPods` 유효성 검사를 재정의할 수 없습니다.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>AKS 에이전트 노드에 Azure 예약 할인을 적용할 수 있습니까?

AKS 에이전트 노드는 표준 Azure 가상 컴퓨터로 청구되므로 AKS에서 사용 중인 VM 크기에 대한 [Azure 예약을][reservation-discounts] 구입한 경우 이러한 할인이 자동으로 적용됩니다.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Azure 테넌트 간에 클러스터를 이동/마이그레이션할 수 있습니까?

이 `az aks update-credentials` 명령을 사용하여 Azure 테넌자 간에 AKS 클러스터를 이동할 수 있습니다. [서비스 주체를 업데이트하거나 만든](https://docs.microsoft.com/azure/aks/update-credentials) 다음 새 [자격 증명으로 aks 클러스터를 업데이트하도록 선택의 지침에 따릅니다.](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>구독 간에 클러스터를 이동/마이그레이션할 수 있습니까?

구독 간의 클러스터 이동은 현재 지원되지 않습니다.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>AKS 클러스터를 현재 Azure 구독에서 다른 Azure 구독으로 이동할 수 있습니까? 

AKS 클러스터를 이동 하면 Azure 구독 간에 연결 된 리소스를 이동 지원 되지 않습니다.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>클러스터 삭제시간이 너무 오래 걸리는 이유는 무엇입니까? 

대부분의 클러스터는 사용자 요청에 따라 삭제됩니다. 경우에 따라 특히 고객이 자체 리소스 그룹을 가져오거나 RG 간 작업을 삭제하는 경우 추가 시간이 걸리거나 실패할 수 있습니다. 삭제에 문제가 있는 경우 RG에 잠금이 없는지, RG 외부의 리소스가 RG 등에서 분리되었는지 다시 한 번 확인합니다.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>'NodeLost' 또는 '알 수 없음' 상태에 포드/배포가 있는 경우에도 클러스터를 업그레이드할 수 있습니까?

할 수 있지만 AKS는 이 것을 권장하지 않습니다. 클러스터 의 상태가 정상이고 정상인 경우 업그레이드를 수행하는 것이 좋습니다.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>비정상 상태에 하나 이상의 노드가 있는 클러스터가 있거나 종료된 경우 업그레이드를 수행할 수 있습니까?

아니요, 업그레이드하기 전에 실패한 상태의 노드를 삭제/제거하거나 클러스터에서 제거하십시오.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>클러스터 삭제를 실행했지만 오류가 표시됩니다.`[Errno 11001] getaddrinfo failed` 

가장 일반적으로 이 문제는 하나 이상의 NSG(네트워크 보안 그룹)가 아직 사용 중이고 클러스터와 연결된 사용자가 발생하기 때문입니다.  삭제하고 다시 삭제를 시도하십시오.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>업그레이드를 실행했지만 이제 포드가 충돌 루프에 있고 준비 프로브가 실패합니까?

서비스 주체가 만료되지 않은 지 확인하십시오.  참조하시기 바랍니다: [AKS 서비스 주체](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) 및 [AKS 업데이트 자격 증명](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>클러스터가 작동중이었지만 갑자기 로드밸슬러, 마운트 PVC 등을 프로비저닝할 수 없습니다. 

서비스 주체가 만료되지 않은 지 확인하십시오.  참조하시기 바랍니다: [AKS 서비스 주체](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) 및 [AKS 업데이트 자격 증명](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>가상 시스템 스케일 세트 API를 사용하여 수동으로 확장할 수 있습니까?

아니요, 가상 시스템 규모 집합 API를 사용하여 확장 작업은 지원되지 않습니다. AKS API ()를`az aks scale`사용합니다.

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>가상 시스템 스케일 세트를 사용하여 수동으로 0개의 노드로 확장할 수 있습니까?

아니요, 가상 시스템 규모 집합 API를 사용하여 확장 작업은 지원되지 않습니다.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>모든 VM을 중지하거나 할당 해제할 수 있습니까?

AKS에는 이러한 구성을 견디고 복구하는 복원 메커니즘이 있지만 권장되는 구성은 아닙니다.

## <a name="can-i-use-custom-vm-extensions"></a>사용자 지정 VM 확장을 사용할 수 있습니까?

AKS는 관리되는 서비스이며 IaaS 리소스의 조작은 지원되지 않습니다. 사용자 정의 구성 요소 등을 설치합니다. Kubernetes API 및 메커니즘을 활용하십시오. 예를 들어 데몬세트를 활용하여 필요한 구성 요소를 설치합니다.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
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

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
