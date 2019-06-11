---
title: AKS(Azure Kubernetes Service)에 대한 질문과 대답
description: Azure Kubernetes Service (AKS)에 대 한 일반적인 질문 중 일부에 대 한 답변을 찾습니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: 1cc03cbcffc5253e8b357b6702cd21c45740ff81
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514502"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-currently-provide-aks"></a>어떤 Azure 지역에는 현재 AKS 제공 되나요?

사용 가능한 지역 목록은 참조 하세요 [AKS 지역 및 가용성][aks-regions]합니다.

## <a name="does-aks-support-node-autoscaling"></a>AKS는 노드 자동 크기 조정 기능을 지원하나요?

예, 자동 크기 조정을 통해 제공 됩니다는 [Kubernetes autoscaler] [ auto-scaler] Kubernetes 1.10 기준으로 합니다. 수동으로 구성 하 고 클러스터 autoscaler를 사용 하는 방법에 대 한 정보를 참조 하세요 [AKS에 클러스터 자동 크기 조정][aks-cluster-autoscale]합니다.

노드의 확장을 관리할 수 (현재 AKS에서 미리 보기)는에서 기본 제공 클러스터 autoscaler를 사용할 수 있습니다. 자세한 내용은 [AKS에서 응용 프로그램 요구 사항에 맞게 클러스터를 자동으로 크기 조정][aks-cluster-autoscaler]합니다.

## <a name="does-aks-support-kubernetes-rbac"></a>AKS는 Kubernetes RBAC를 지원 하나요?

예, Azure CLI를 사용 하 여 클러스터를 만들 때 Kubernetes 역할 기반 액세스 제어 (RBAC)는 기본적으로 사용 됩니다. Azure portal 또는 템플릿을 사용 하 여 생성 된 클러스터에 대 한 RBAC를 사용할 수 있습니다.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>AKS를 기존 가상 네트워크에 배포할 수 있습니까?

예를 사용 하 여 기존 가상 네트워크에는 AKS 클러스터를 배포할 수 있습니다 합니다 [고급 네트워킹 기능][aks-advanced-networking]합니다.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>만들기 Kubernetes API 서버에 액세스할 수 있는 내 가상 네트워크 내 에서만

지금은 없습니다. Kubernetes API 서버는 공용의 FQDN(정규화된 도메인 이름)으로 공개됩니다. 사용 하 여 클러스터에 대 한 액세스를 제어할 수 있습니다 [Kubernetes RBAC와 Azure Active Directory (Azure AD)][aks-rbac-aad]합니다.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 자동으로 야간 일정에 따라 클러스터의 Linux 노드에 보안 패치를 적용합니다. 그러나 수는 해당 Linux로 노드가 다시 부팅 필요 함을 확인 하는 일을 담당 합니다. 노드 다시 부팅에 대 한 몇 가지 옵션이 있습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터 업그레이드 [cordon 및 드레이닝 노드] [ cordon-drain] 자동으로 최신 Ubuntu 이미지 및 새 패치 버전 또는 부 Kubernetes 버전을 사용 하 여 새 노드가 온라인 상태로 됩니다. 자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade]를 참조하세요.
- 사용 하 여 [Kured](https://github.com/weaveworks/kured), kubernetes는 오픈 소스 재부팅 디먼입니다. 으로 kured 실행을 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 하 고 다시 부팅이 필요 하다 나타내는 파일의 존재에 대 한 각 노드를 모니터링 합니다. 클러스터에 OS 재부팅에서 관리 하는 동일한 [cordon 및 드레이닝 프로세스] [ cordon-drain] 클러스터 업그레이드 합니다.

kured 사용에 대한 자세한 내용은 [AKS에서 노드에 보안 및 커널 업데이트 적용][node-updates-kured]을 참조하세요.

### <a name="windows-server-nodes"></a>Windows 서버 노드

(현재 AKS에서 미리 보기)는에서 Windows Server 노드에 대 한 Windows Update는 자동으로 실행 및 최신 업데이트를 적용 합니다. Windows 업데이트 릴리스 주기 및 사용자 고유의 유효성 검사 프로세스 정기적인 일정에 따라 AKS 클러스터에서 Windows Server 노드 풀에서 업그레이드를 수행 해야 합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행 하는 노드를 만듭니다 다음 이전 노드를 제거 합니다. 이 프로세스에 대 한 자세한 내용은 참조 하세요. [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

각 AKS 배포는 두 리소스 그룹에 걸쳐 있습니다.

1. 첫 번째 리소스 그룹을 만듭니다. 이 그룹에는 Kubernetes 서비스 리소스만을 포함합니다. AKS 리소스 공급자는 자동으로 배포 하는 동안 두 번째 리소스 그룹을 만듭니다. 두 번째 리소스 그룹의 예로 *MC_myResourceGroup_myAKSCluster_eastus*합니다. 이 두 번째 리소스 그룹의 이름을 지정 하는 방법에 대 한 자세한 내용은 다음 섹션을 참조 하세요.
1. 두 번째 리소스 그룹을 같은 *MC_myResourceGroup_myAKSCluster_eastus*, 모든 클러스터와 연결 된 인프라 리소스를 포함 합니다. 이러한 리소스에는 Kubernetes 노드 VM, 가상 네트워킹 및 저장소가 포함됩니다. 이 리소스 그룹의 목적은 리소스 정리를 간소화 하는 것입니다.

저장소 계정 또는 예약 된 공용 IP 주소와 같은 AKS 클러스터를 사용 하는 리소스를 만드는 경우 자동으로 생성 된 리소스 그룹에 배치 합니다.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>나만의 AKS 인프라 리소스 그룹의 이름을 제공할 수 있습니까?

예. 기본적으로 AKS 리소스 공급자를 자동으로 보조 리소스 그룹을 만듭니다 (같은 *MC_myResourceGroup_myAKSCluster_eastus*) 배포 하는 동안. 회사 정책을 준수 하는 관리 되는이 클러스터에 대 한 고유한 이름을 제공할 수 있습니다 (*MC_* ) 리소스 그룹입니다.

고유한 리소스 그룹 이름을 지정 하려면 다음을 설치 합니다 [aks 미리 보기] [ aks-preview-cli] Azure CLI 확장 버전 *평면이 0.3.2* 이상. 사용 하 여 AKS 클러스터를 만들 때 합니다 [az aks 만들기] [ az-aks-create] 명령에 사용 하 여는 *-노드 리소스 그룹* 매개 변수 리소스 그룹의 이름을 지정 합니다. 경우 있습니다 [Azure Resource Manager 템플릿을 사용 하 여] [ aks-rm-template] AKS 클러스터를 배포 하려면 리소스 그룹 이름을 사용 하 여 정의할 수 있습니다 합니다 *nodeResourceGroup* 속성입니다.

* 보조 리소스 그룹 사용자의 구독에서 Azure 리소스 공급자에 의해 자동으로 만들어집니다.
* 클러스터를 만들려는 경우에 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다.

사용 하 여 작업할 때 합니다 *MC_* 수 있음을 명심 리소스 그룹:

* 에 대 한 기존 리소스 그룹을 지정 합니다 *MC_* 그룹입니다.
* 다른 구독을 지정 합니다 *MC_* 리소스 그룹입니다.
* 변경 된 *MC_* 클러스터가 만들어진 후 리소스 그룹 이름입니다.
* 내에서 관리 되는 리소스의 이름을 지정 합니다 *MC_* 리소스 그룹입니다.
* 수정 하거나 태그 내에서 관리 되는 리소스를 삭제 합니다 *MC_* 리소스 그룹입니다. (다음 섹션에서 추가 정보 참조).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>태그 및 MC_ 리소스 그룹에 AKS 리소스의 다른 속성을 수정할 수 있나요?

수정 하거나 Azure에서 만든 태그 및 기타 리소스 속성을 삭제 하는 경우는 *MC_* 리소스 그룹을 크기 조정 및 오류 업그레이드와 같은 예기치 않은 결과 얻을 수 있습니다. AKS를 사용 하면 생성 및 사용자 지정 태그를 수정할 수 있습니다. 만들거나 예를 들어 사용자 지정 태그를 수정 하려면, 비즈니스 단위 또는 비용 센터를 할당할 수도 있습니다. 아래에 있는 리소스를 수정 하 여 합니다 *MC_* AKS 클러스터에서 서비스 수준 목표 (SLO) 중단 합니다. 자세한 내용은 참조 하세요. [AKS는 제공 서비스 수준 계약?](#does-aks-offer-a-service-level-agreement)

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
- *DenyEscalatingExec*
- *AlwaysPullImages*

현재 AKS에서 입학 허가 컨트롤러 목록을 수정할 수 없습니다.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

AKS는 Azure Key Vault를 사용 하 여 현재 고유 하 게 통합 되지 않았습니다. 그러나 합니다 [Kubernetes 프로젝트에 대 한 Azure 키 자격 증명 모음 FlexVolume] [ keyvault-flexvolume] 하면 직접 Kubernetes pod에서 키 자격 증명 모음 비밀에 통합 합니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

예, Windows Server 컨테이너는 미리 보기에서 사용할 수 있습니다. AKS에서 Windows Server 컨테이너를 실행 하려면 게스트 OS로 Windows Server를 실행 하는 노드 풀을 만들어야 합니다. Windows Server 컨테이너는 Windows Server 2019 에서만 사용할 수 있습니다. 시작 하려면 참조 [Windows Server 노드 풀을 사용 하 여 AKS 클러스터 만들기][aks-windows-cli]합니다.

노드가 풀에 대 한 창 Server 지원에는 업스트림 Kubernetes 프로젝트에서 Windows 서버에 포함 된 몇 가지 제한 사항이 포함 됩니다. 이러한 제한 사항에 대 한 자세한 내용은 참조 하세요. [AKS 제한 사항에 대 한 Windows Server 컨테이너][aks-windows-limitations]합니다.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS는 서비스 수준 계약을 제공 하나요?

서비스 수준 계약 (SLA), 공급자는 게시 된 서비스 수준을 충족 되지 않으면 서비스의 비용에 대 한 고객 돌려줄에 동의 해야 합니다. AKS는 무료 이며, 때문에 비용 없이 이므로 돌려줄를 사용할 수 있는 AKS는 공식 SLA는 없습니다. 그러나 AKS Kubernetes API 서버에 대 한 최소 99.5%의 가용성을 유지 하려고 노력 합니다.

## <a name="why-cant-i-set-maxpods-below-30"></a>30 아래 maxPods를 설정할 수 없습니다는 이유

AKS에서 설정할 수 있습니다는 `maxPods` Azure CLI 및 Azure Resource Manager 템플릿을 사용 하 여 클러스터를 만들 때 값입니다. 그러나 Kubenet와 Azure CNI 모두 필요는 *최소값* (생성 시 유효성 검사):

| 네트워킹 | 최소 | 최대 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

AKS 관리 서비스 이기 때문에 배포 하 고 클러스터의 일부로 추가 기능 및 pod를 관리 합니다. 과거에는 사용자가 정의할 수는 `maxPods` 관리 되는 pod (예: 30) 실행 하는 데 필요한 값 보다 낮은 값입니다. 이제 AKS이이 수식을 사용 하 여 pod의 최소 수를 계산 합니다. ((maxPods 또는 (maxPods * vm_count)) > 관리 되는 추가 기능 pod 최소입니다.

사용자는 최소를 재정의할 수 없습니다. `maxPods` 유효성 검사 합니다.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>내 AKS 에이전트 노드에 Azure 예약 할인을 적용할 수 있나요?

AKS 에이전트 노드는 표준 Azure virtual machines로 청구 되며 따라서 구입한 [Azure 예약] [ reservation-discounts] AKS에서 사용 하는 VM 크기, 할인 자동으로 적용 됩니다.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
