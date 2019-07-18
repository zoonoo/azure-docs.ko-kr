---
title: AKS(Azure Kubernetes Service) 클러스터 업그레이드
description: AKS(Azure Kubernetes Service) 클러스터를 업그레이드하는 방법 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: dd88b5a044fe495da374178be8774f45bdd30f61
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614058"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 업그레이드

AKS 클러스터의 수명 주기의 일환으로, 최신 Kubernetes 버전으로 업그레이드해야 하는 경우가 많습니다. 최신 Kubernetes 보안 릴리스를 적용하거나 최신 기능으로 업그레이드하는 것은 중요합니다. 이 문서에서는 마스터 구성 요소 또는 단일 AKS 클러스터의 기본 노드 풀을 업그레이드 하는 방법을 보여 줍니다.

AKS 클러스터에 여러 노드 풀 또는 Windows Server 노드 (둘 다에서 현재 AKS 미리 보기)를 사용 하는 참조 [AKS에 노드 풀을 업그레이드][nodepool-upgrade]합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 Azure CLI 버전 2.0.65 실행 이상. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="check-for-available-aks-cluster-upgrades"></a>사용할 수 있는 AKS 클러스터 업그레이드를 위한 확인

클러스터에 대해 사용할 수 있는 Kubernetes 릴리스를 확인 하려면 사용 합니다 [az aks get-업그레이드][az-aks-get-upgrades] 명령입니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에서 *myAKSCluster*라는 클러스터로 가능한 업그레이드를 확인합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AKS 클러스터를 업그레이드하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 하지만 예를 들어 간의 업그레이드 *1.11.x* -> *1.12.x* 하거나 *1.12.x* -> *1.13.x* 허용 *1.11.x* -> *1.13.x* 아닙니다.
>
> 를 업그레이드 하려면에서 *1.11.x* -> *1.13.x*먼저 업그레이드할 *1.11.x* -> *1.12.x*, 업그레이드 *1.12.x* -> *1.13.x*합니다.

다음 예제 출력에서는 버전으로 클러스터를 업그레이드할 수 있습니다 *1.12.7* 하거나 *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>AKS 클러스터 업그레이드

AKS 클러스터에 대 한 사용 가능한 버전 목록을 사용 합니다 [az aks 업그레이드][az-aks-upgrade] command to upgrade. During the upgrade process, AKS adds a new node to the cluster that runs the specified Kubernetes version, then carefully [cordon and drains][kubernetes-drain] 응용 프로그램을 실행 중인 중단을 최소화 하기 위해 이전 노드 중 하나입니다. 새 노드는 응용 프로그램 pod를 실행 하는 것에 확정 되 면 이전 노드가 삭제 됩니다. 이 프로세스는 클러스터의 모든 노드에서 업그레이드 될 때까지 반복 합니다.

다음 예제에서는 버전으로 클러스터 업그레이드 *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

노드 수에 따라 클러스터를 업그레이드하는 데 몇 분 정도 걸립니다.

업그레이드가 성공적으로 완료 되었는지 확인 하려면 사용 합니다 [az aks 표시][az-aks-show] 명령:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력에서는 클러스터가 이제 실행 *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 AKS 클러스터를 업그레이드하는 방법을 설명했습니다. AKS 클러스터의 배포 및 관리에 대해 더 자세히 알아보려면 자습서 세트를 참조하세요.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
