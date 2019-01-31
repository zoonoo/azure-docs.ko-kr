---
title: 일반적인 Azure Kubernetes Service 문제 해결
description: AKS(Azure Kubernetes Service)를 사용 할 때 발생하는 일반적인 문제를 해결하는 방법을 알아봅니다.
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 17f6971cfa2dcd8c8988edc063c89859abec5367
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468838"
---
# <a name="aks-troubleshooting"></a>AKS 문제 해결

AKS(Azure Kubernetes Service) 클러스터를 만들거나 관리할 때 경우에 따라 문제가 발생할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>일반적으로 Kubernetes 문제 디버깅에 대한 정보는 어디에서 찾나요?

[Kubernetes 클러스터 문제 해결에 대한 공식 가이드](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)를 검색합니다.
pod, 노드, 클러스터 등의 문제 해결과 관련해서 Microsoft 엔지니어가 게시한 [문제 해결 가이드](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)도 있습니다.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>만들기 또는 업그레이드 동안 "할당량 초과" 오류가 발생합니다. 어떻게 해야 하나요? 

[코어를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)해야 합니다.

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>AKS의 노드당 최대 Pod 설정이란?

노드당 최대 Pod 설정은 Azure Portal에서 AKS 클러스터를 배포하는 경우 기본적으로 30입니다.
노드당 최대 Pod 설정은 Azure CLI에서 AKS 클러스터를 배포하는 경우 기본적으로 110입니다. Azure CLI의 최신 버전을 사용하도록 합니다. 이 기본 설정은 `az aks create` 명령에서 `–-max-pods` 플래그를 사용하여 변경할 수 있습니다.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>고급 네트워킹을 사용하여 AKS 클러스터를 배포하는 동안 insufficientSubnetSize 오류가 발생합니다. 어떻게 해야 하나요?

AKS를 만드는 동안 네트워킹에 대한 사용자 지정 Azure Virtual Network 옵션에서 Azure CNI(ontainer Network Interface)가 IPAM(IP 주소 관리)에 사용됩니다. AKS 클러스터의 노드 수는 1에서 100개 사이일 수 있습니다. 이전 섹션에 따라, 서브넷 크기는 노드 수와 노드당 최대 pod 수를 곱한 값보다 커야 합니다. 이 관계는 서브넷 크기 > 클러스터의 노드 수 * 노드당 최대 pod 수로 나타낼 수 있습니다.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>내 Pod가 CrashLoopBackOff 모드에서 중단됩니다. 어떻게 해야 하나요?

Pod가 해당 모드에서 중단되는 이유는 다양할 수 있습니다. 다음을 확인할 수 있습니다.

* `kubectl describe pod <pod-name>`를 사용하여 pod 자체
* `kubectl log <pod-name>`을 사용하여 로그

Pod 문제를 해결하는 방법에 대한 자세한 내용은 [애플리케이션 디버그](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)를 참조하세요.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>기존 클러스터에서 RBAC를 사용하도록 설정하려고 합니다. 어떻게 하면 되나요?

현재는 기존 클러스터에서 RBAC(역할 기반 액세스 제어)를 사용하도록 설정할 수 없습니다. 새 클러스터를 명시적으로 만들어야 합니다. CLI를 사용하는 경우 RBAC가 기본적으로 사용됩니다. AKS 포털을 사용하는 경우 만들기 워크플로에서 RBAC를 사용하도록 설정하는 토글 단추를 사용할 수 있습니다.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>기본 설정의 Azure CLI 또는 Azure Portal에서 RBAC가 설정된 클러스터를 만들었으나 kubernetes 대시보드에서 경고가 많이 표시됩니다. 이전에는 대시보드가 경고없이 잘 작동되었습니다. 어떻게 해야 하나요?

대시보드에 경고가 표시되는 이유는 클러스터에 RBAC가 설정되어 있으나 해당 클러스터에 대한 액세스는 기본적으로 사용하지 않도록 설정되어 있기 때문입니다. 일반적으로 클러스터의 모든 사용자에게 기본적으로 대시보드를 노출하면 보안 위협이 초래될 수 있으므로 이렇게 하는 것이 적절합니다. 그래도 대시보드를 사용하도록 설정하려면 [이 블로그 게시물](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)의 단계를 따르세요.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>대시보드에 연결할 수 없습니다. 어떻게 해야 하나요?

클러스터 외부의 서비스에 액세스하는 가장 쉬운 방법은 `kubectl proxy`를 실행하는 것입니다. 그러면 localhost 포트 8001로 전송된 요청이 Kubernetes API 서버로 프록시됩니다. 여기에서 API 서버는 서비스로 프록시할 수 있습니다. `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`

Kubernetes 대시보드가 표시되지 않으면 `kube-proxy` pod가 `kube-system` 네임스페이스에서 실행되고 있는지를 확인합니다. 실행 중 상태가 아니면 pod를 삭제합니다. 그러면 다시 시작됩니다.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>kubectl logs를 사용하여 로그를 가져올 수 없고 API 서버에 연결할 수 없습니다. "서버 오류: error dialing backend: dial tcp…" 오류가 발생합니다. 어떻게 해야 하나요?

기본 NSG(네트워크 보안 그룹)가 수정되지 않았는지와 API 서버 연결을 위해 포트 22가 열려 있는지 확인합니다. `tunnelfront` pod가 `kube-system` 네임스페이스에서 실행되는지 여부를 확인합니다. 실행되지 않으면 pod를 강제로 삭제합니다. 그러면 다시 시작됩니다.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-problem"></a>업그레이드하거나 크기를 조정하려고 하는데 "메시지": "'imageReference' 속성을 변경할 수 없습니다." 오류가 표시됩니다.  이 문제를 어떻게 해결하나요?

AKS 클러스터 내의 에이전트 노드에서 태그를 수정했기 때문에 이 오류가 발생하는 것일 수 있습니다. MC_* 리소스 그룹에서 태그 및 리소스의 기타 속성을 수정 및 삭제하면 예기치 않은 결과가 발생할 수 있습니다. AKS 클러스터의 MC_* 그룹 아래에서 리소스를 수정하면 SLO(서비스 수준 목표)가 중단됩니다.
