---
title: Azure Kubernetes Service에 대한 질문과 대답
description: Azure Kubernetes Service에 대한 일반적인 질문에 대한 답변을 제공합니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: ea22b33233f85da117de54829e5a16bd7dcab36a
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205251"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 대한 질문과 대답

이 문서에서는 AKS(Azure Kubernetes Service)에 대한 질문과 대답을 제공합니다.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>현재 AKS(Azure Kubernetes Service)는 어떤 Azure 지역에서 제공되나요?

전체 목록은 Azure Kubernetes Service [지역 및 가용성][aks-regions] 설명서를 참조하세요.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>보안 업데이트가 AKS 에이전트 노드에 적용되나요?

Azure는 자동으로 야간 일정에 따라 클러스터의 노드에 보안 패치를 적용합니다. 하지만 필요에 따라 노드가 다시 부팅되도록 해야 합니다. 노드 다시 부팅을 수행하기 위한 몇 가지 옵션이 있습니다.

- Azure Portal 또는 Azure CLI를 통해 수동으로.
- AKS 클러스터를 업그레이드하여. 클러스터는 자동으로 [cordon 및 드레이닝 노드](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)를 업그레이드한 다음, 최신 Ubuntu 이미지로 백업합니다. `az aks upgrade`에서 현재 클러스터 버전을 지정하여 Kubernetes 버전을 변경하지 않으면서 노드에 OS 이미지를 업데이트합니다.
- Kubernetes를 위한 오픈 소스 재부팅 디먼인 [Kured](https://github.com/weaveworks/kured)를 사용합니다. Kured는 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)으로 실행하며 다시 부팅해야 함을 표시하는 파일의 존재에 대한 각 노드를 모니터링합니다. 그런 다음, 앞서 설명한 동일한 cordon 및 드레이닝 프로세스에 따라 클러스터 전반의 다시 부팅을 오케스트레이트합니다.

## <a name="does-aks-support-node-autoscaling"></a>AKS는 노드 자동 크기 조정 기능을 지원하나요?

예, 자동 크기 조정은 Kubernetes 1.10 기준으로 [Kubernetes autoscaler][auto-scaler]를 통해 사용할 수 있습니다.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS는 Kubernetes RBAC(역할 기반 액세스 제어)를 지원합니까?

예, RBAC는 [Azure CLI 또는 Azure Resource Manager 템플릿에서 AKS 클러스터를 배포](https://docs.microsoft.com/en-us/azure/aks/aad-integration)하는 경우 사용할 수 있습니다. 이 기능은 곧 Azure Portal에서 공개될 예정입니다.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-this-be-configured"></a>AKS가 지원하는 Kubernetes 허용 컨트롤러는 무엇인가요? 이를 구성할 수 있나요?

AKS는 다음과 같은 [허용 컨트롤러][admission-controllers]를 지원합니다.

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

현재는 AKS에서 허용 컨트롤러의 목록을 수정할 수 없습니다.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>AKS를 기존 가상 네트워크에 배포할 수 있습니까?

예, [고급 네트워킹 기능](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md)을 사용하여 AKS 클러스터를 기존 가상 네트워크에 배포할 수 있습니다.

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Kubernetes API 서버가 내 가상 네트워크 내에서만 액세스할 수 있도록 제한할 수 있습니까?

지금은 없습니다. Kubernetes API 서버는 공용의 정규화된 도메인 이름(FQDN)으로 공개됩니다. [Kubernetes RBAC(역할 기반 액세스 제어) 및 AAD(Azure Active Directory)](https://docs.microsoft.com/en-us/azure/aks/aad-integration)를 사용하여 클러스터에 대한 액세스를 제어해야 합니다.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault는 AKS와 통합되나요?

AKS는 기본적으로 현재는 Azure Key Vault와 통합되지 않습니다. 그러나 [Hexadite의 acs-keyvault-agent][hexadite]와 같은 커뮤니티 솔루션이 있습니다.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Windows Server 컨테이너를 AKS에서 실행할 수 있습니까?

Windows Server 컨테이너를 실행하려면 Windows Server 기반 노드를 실행해야 합니다. Windows 서버 기반 노드는 현재 AKS에서 사용할 수 없습니다. 단, Virtual Kubelet을 사용하여 Azure Container Instances에서 Windows 컨테이너를 예약하여 AKS 클러스터의 일부로 관리할 수 있습니다. 자세한 내용은 [AKS를 통한 Virtual Kubelet 사용][virtual-kubelet]을 참조하세요.

## <a name="why-are-two-resource-groups-created-with-aks"></a>AKS를 통해 2개의 리소스 그룹이 생성되는 이유는 무엇인가요?

각 AKS 배포는 두 리소스 그룹에 걸쳐 있습니다. 첫 번째는 사용자에 의해 생성되며 Kubernetes 서비스 리소스만 포함합니다. *MC_myResourceGroup_myAKSCluster_eastus*와 같은 이름으로 배포되는 동안 AKS 리소스 공급자는 자동으로 두 번째 리소스 그룹을 만듭니다. 두 번째 리소스 그룹에는 VM, 네트워킹 및 저장소와 같이 클러스터와 연결된 인프라 리소스의 모든 항목이 포함됩니다. 이는 리소스 정리를 간소화하기 위해 생성됩니다.

저장소 계정 또는 예약된 공용 IP 주소와 같은 AKS 클러스터를 함께 사용될 리소스를 만드는 경우 자동으로 생성된 리소스 그룹에 배치해야 합니다.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS는 서비스 수준 계약을 제공합니까?

SLA(서비스 수준 계약)에서, 공급자는 게시된 서비스 수준이 충족되지 않을 경우 고객에게 서비스 비용을 배상하는 것에 동의합니다. AKS 자체는 무료이므로 배상할 비용이 없으며 따라서 공식 SLA가 없습니다. 그러나 Kubernetes API 서버의 가용성을 99.5% 이상으로 유지하기 위해 노력하고 있습니다.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
