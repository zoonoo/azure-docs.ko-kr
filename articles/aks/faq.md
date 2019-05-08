---
title: AKS(Azure Kubernetes Service)에 대한 질문과 대답
description: AKS(Azure Kubernetes Service)에 대한 일반적인 질문에 대한 답변을 제공합니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: f365fcd61944fbae131ab79a1c3660aaf02fa8d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073938"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>현재 AKS(Azure Kubernetes Service)는 어떤 Azure 지역에서 제공되나요?

사용 가능한 지역의 전체 목록은 [AKS 지역 및 가용성][aks-regions]을 참조하세요.

## <a name="does-aks-support-node-autoscaling"></a>AKS는 노드 자동 크기 조정 기능을 지원하나요?

예, 자동 크기 조정은 Kubernetes 1.10 기준으로 [Kubernetes autoscaler][auto-scaler]를 통해 사용할 수 있습니다. 수동으로 구성 하 고 클러스터 autoscaler를 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [AKS에 클러스터 자동 크기 조정][aks-cluster-autoscale]합니다.

노드의 확장을 관리할 수 (현재 AKS에서 미리 보기)는에서 기본 제공 클러스터 autoscaler를 사용할 수 있습니다. 자세한 내용은 [AKS에서 응용 프로그램 요구 사항에 맞게 클러스터를 자동으로 크기 조정][aks-cluster-autoscaler]합니다.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS는 Kubernetes RBAC(역할 기반 액세스 제어)를 지원합니까?

예. Kubernetes RBAC는 Azure CLI를 사용하여 클러스터를 만들 때 기본적으로 사용되도록 설정됩니다. Azure Portal 또는 템플릿을 사용하여 만든 클러스터의 경우 RBAC를 사용하도록 설정할 수 있습니다.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>AKS를 기존 가상 네트워크에 배포할 수 있습니까?

예. [고급 네트워킹 기능][aks-advanced-networking]을 사용하여 AKS 클러스터를 기존 가상 네트워크에 배포할 수 있습니다.

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Kubernetes API 서버가 내 가상 네트워크 내에서만 액세스할 수 있도록 제한할 수 있습니까?

지금은 없습니다. Kubernetes API 서버는 공용의 FQDN(정규화된 도메인 이름)으로 공개됩니다. [Kubernetes RBAC(역할 기반 액세스 제어) 및 AAD(Azure Active Directory)][aks-rbac-aad]를 사용하여 클러스터에 대한 액세스를 제어할 수 있습니다.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 자동으로 야간 일정에 따라 클러스터의 Linux 노드에 보안 패치를 적용합니다. 그러나 수는 해당 Linux로 노드가 다시 부팅 필요 함을 확인 하는 일을 담당 합니다. 노드 다시 부팅을 수행하기 위한 몇 가지 옵션이 있습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터 업그레이드는 자동으로 [노드를 차단 및 드레이닝][cordon-drain]한 후 최신 Ubuntu 이미지와 새 패치 버전 또는 부 Kubernetes 버전을 사용하여 각 노드를 다시 작동합니다. 자세한 내용은 [AKS 클러스터 업그레이드][aks-upgrade]를 참조하세요.
- Kubernetes를 위한 오픈 소스 재부팅 디먼인 [Kured](https://github.com/weaveworks/kured)를 사용합니다. Kured는 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)으로 실행하며 다시 부팅해야 함을 표시하는 파일의 존재에 대한 각 노드를 모니터링합니다. 업그레이드와 동일한 [차단 및 드레이닝 프로세스][cordon-drain]를 사용하여 클러스터 전체에서 OS 재부팅이 관리됩니다.

kured 사용에 대한 자세한 내용은 [AKS에서 노드에 보안 및 커널 업데이트 적용][node-updates-kured]을 참조하세요.

### <a name="windows-server-nodes"></a>Windows 서버 노드

(현재 AKS에서 미리 보기)는에서 Windows Server 노드에 대 한 Windows Update는 자동으로 실행 및 최신 업데이트를 적용 합니다. Windows 업데이트 릴리스 주기 및 사용자 고유의 유효성 검사 프로세스 정기적인 일정에 따라 AKS 클러스터에서 Windows Server 노드 풀에서 업그레이드를 수행 해야 합니다. 이 업그레이드 프로세스는 최신 Windows Server 이미지 및 패치를 실행 하는 노드를 만듭니다 다음 이전 노드를 제거 합니다. 이 프로세스에 대 한 자세한 내용은 참조 하세요. [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

각 AKS 배포는 두 리소스 그룹에 걸쳐 있습니다.

- 첫 번째 리소스 그룹은 사용자가 생성하며 Kubernetes 서비스 리소스만 포함합니다. AKS 리소스 공급자는 배포 중에 자동으로 두 번째 리소스 그룹(예: *MC_myResourceGroup_myAKSCluster_eastus*)을 만듭니다. 이 두 번째 리소스 그룹의 이름을 지정 하는 방법에 대 한 내용은 다음 섹션을 참조 합니다.
- 이 두 번째 리소스 그룹(예: *MC_myResourceGroup_myAKSCluster_eastus*)에는 클러스터와 연결된 모든 인프라 리소스가 포함됩니다. 이러한 리소스에는 Kubernetes 노드 VM, 가상 네트워킹 및 저장소가 포함됩니다. 이 별도의 리소스 그룹은 리소스 정리를 간소화하기 위해 생성됩니다.

저장소 계정 또는 예약된 공용 IP 주소와 같이 AKS 클러스터와 함께 사용할 리소스를 만드는 경우 자동으로 생성된 리소스 그룹에 배치합니다.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>나만의 AKS 인프라 리소스 그룹의 이름을 제공할 수 있습니까?

예. 기본적으로 AKS 리소스 공급자를 자동으로 보조 리소스 그룹을 만듭니다 배포 하는 동안 같은 *MC_myResourceGroup_myAKSCluster_eastus*합니다. 회사 정책을 준수 하는 관리 되는이 클러스터에 대 한 고유한 이름을 제공할 수 있습니다 (*MC_*) 리소스 그룹입니다.

고유한 리소스 그룹 이름을 지정 하려면 다음을 설치 합니다 [aks 미리 보기] [ aks-preview-cli] Azure CLI 확장 버전 *평면이 0.3.2* 이상. 사용 하 여 AKS 클러스터를 만들 때를 [az aks 만들기] [ az-aks-create] 명령에 사용 합니다 *-노드 리소스 그룹* 매개 변수 리소스 그룹의 이름을 지정 합니다. 경우 있습니다 [Azure Resource Manager 템플릿을 사용 하 여] [ aks-rm-template] 사용 하 여 리소스 그룹 이름을 정의할 수 있습니다 AKS 클러스터를 배포 하려면 합니다 *nodeResourceGroup* 속성입니다.

* 이 리소스 그룹 사용자의 구독에서 Azure 리소스 공급자에 의해 자동으로 만들어집니다.
* 클러스터를 만들 때에 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다.

다음 시나리오는 지원 되지 않습니다.

* 에 대 한 기존 리소스 그룹을 지정할 수 없습니다 *MC_* 그룹입니다.
* 다른 구독을 지정할 수 없습니다는 *MC_* 리소스 그룹입니다.
* 변경할 수 없습니다는 *MC_* 클러스터가 만들어진 후 리소스 그룹 이름입니다.
* 내에서 관리 되는 리소스에 대 한 이름을 지정할 수 없습니다는 *MC_* 리소스 그룹입니다.
* 수정 하거나 내에서 관리 되는 리소스의 태그를 삭제할 수 없습니다는 *MC_* 리소스 그룹 (다음 섹션에서 추가 정보 참조).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>MC_* 리소그스 룹에서 태그 및 AKS 리소스의 다른 속성을 수정할 수 있나요?

*MC_** 리소스 그룹에서 리소스의 Azure 생성 태그 및 기타 속성을 수정 및 삭제하면 크기 조정 및 업그레이드 오류와 같은 예기치 않은 결과가 발생할 수 있습니다. 비즈니스 단위 또는 비용 센터 할당과 같은 추가 사용자 지정 태그를 만들고 수정하도록 지원됩니다. AKS 클러스터의 *MC_** 아래에 있는 리소스를 수정하면 SLO(서비스 수준 목표)가 중단됩니다. 자세한 내용은 [AKS는 서비스 수준 계약을 제공합니까?](#does-aks-offer-a-service-level-agreement)를 참조하세요.

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

현재는 AKS에서 허용 컨트롤러의 목록을 수정할 수 없습니다.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

현재, AKS는 기본적으로 Azure Key Vault와 통합되지 않습니다. 그러나 [Kubernetes에 대한 Azure KeyVault Flex Volume 프로젝트][keyvault-flexvolume]를 통해 Kubernetes Pod에서 KeyVault 비밀로 직접 통합할 수 있습니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

예, Windows Server 컨테이너는 미리 보기에서 사용할 수 있습니다. AKS에서 Windows Server 컨테이너를 실행 하려면 게스트 OS로 Windows Server를 실행 하는 노드 풀을 만들어야 합니다. Windows Server 컨테이너는 Windows Server 2019만 사용할 수 있습니다. 시작 하려면 [Windows Server 노드 풀을 사용 하 여 AKS 클러스터 만들기][aks-windows-cli]합니다.

창 서버 노드 풀 지원에는 업스트림 Kubernetes 프로젝트에서 Windows 서버에 포함 된 몇 가지 제한 사항이 포함 됩니다. 이러한 제한 사항에 대 한 자세한 내용은 참조 하세요. [AKS 제한 사항에 대 한 Windows Server 컨테이너][aks-windows-limitations]합니다.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS는 서비스 수준 계약을 제공합니까?

SLA(서비스 수준 계약)에서, 공급자는 게시된 서비스 수준이 충족되지 않을 경우 고객에게 서비스 비용을 배상하는 것에 동의합니다. AKS 자체는 무료이므로 배상할 비용이 없으며 따라서 공식 SLA가 없습니다. 그러나 AKS는 Kubernetes API 서버의 가용성을 99.5% 이상으로 유지하기 위해 노력하고 있습니다.

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

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
