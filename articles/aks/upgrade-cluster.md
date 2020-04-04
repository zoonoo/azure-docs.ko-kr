---
title: AKS(Azure Kubernetes Service) 클러스터 업그레이드
description: 최신 기능 및 보안 업데이트를 받으려면 AKS(Azure Kubernetes) 클러스터를 업그레이드하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 183e0a85f65d24dc7133307391931bea754a456d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632601"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 업그레이드

AKS 클러스터의 수명 주기의 일환으로, 최신 Kubernetes 버전으로 업그레이드해야 하는 경우가 많습니다. 최신 Kubernetes 보안 릴리스를 적용하거나 최신 기능으로 업그레이드하는 것은 중요합니다. 이 문서에서는 AKS 클러스터에서 마스터 구성 요소 또는 단일 기본 노드 풀을 업그레이드하는 방법을 보여 주며 있습니다.

여러 노드 풀 또는 Windows 서버 노드를 사용하는 AKS 클러스터(현재 AKS에서 미리 보기)의 경우 [AKS의 노드 풀 업그레이드를][nodepool-upgrade]참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.65 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!WARNING]
> AKS 클러스터 업그레이드는 노드의 코돈 및 드레인을 트리거합니다. 사용 가능한 계산 할당량이 낮은 경우 업그레이드가 실패할 수 있습니다. 자세한 내용은 [할당량 증가를](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) 참조하세요.
> 자체 클러스터 자동 크기 조정기 배포를 실행하는 경우 업그레이드 프로세스를 방해할 가능성이 있으므로 업그레이드 중에 사용하지 않도록 설정하십시오(복제본 0으로 확장할 수 있음). 관리되는 자동 크기 조정기는 자동으로 이를 처리합니다. 

## <a name="check-for-available-aks-cluster-upgrades"></a>사용할 수 있는 AKS 클러스터 업그레이드를 위한 확인

클러스터에 사용할 수 있는 Kubernetes 릴리스를 확인하려면 [az aks get-upgrades][az-aks-get-upgrades] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에서 *myAKSCluster*라는 클러스터로 가능한 업그레이드를 확인합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AKS 클러스터를 업그레이드하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 예를 들어 *1.12.x* -> *1.13.x* 또는 *1.13.x* -> *1.14.x* 사이의 업그레이드는 허용되지만 *1.12.x* -> *1.14.x는* 허용되지 않습니다.
>
> 업그레이드하려면 *1.12.x* -> *1.14.x에서* *1.12.x* -> *1.13.x에서*먼저 업그레이드한 다음 *1.13.x* -> *1.14.x에서*업그레이드하십시오.

다음 예제 출력은 클러스터를 *버전 1.13.9 및 1.13.10으로* 업그레이드할 수 있음을 보여 주며 다음과 같은 *1.13.10*

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
업그레이드를 사용할 수 없는 경우 다음을 받게 됩니다.
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>AKS 클러스터 업그레이드

AKS 클러스터에 대한 사용 가능한 버전 목록을 통해 [az aks upgrade][az-aks-upgrade] 명령을 사용하여 업그레이드합니다. 업그레이드 프로세스 중에 AKS는 지정된 Kubernetes 버전을 실행하는 클러스터에 새 노드를 추가한 다음 이전 노드 중 하나를 신중하게 [코돈및 드레인하여][kubernetes-drain] 실행 중인 응용 프로그램의 중단을 최소화합니다. 새 노드가 실행 중인 응용 프로그램 포드로 확인되면 이전 노드가 삭제됩니다. 이 프로세스는 클러스터의 모든 노드가 업그레이드될 때까지 반복됩니다.

다음 예제는 클러스터를 버전 *1.13.10으로 업그레이드합니다.*

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

노드 수에 따라 클러스터를 업그레이드하는 데 몇 분 정도 걸립니다. 

> [!NOTE]
> 클러스터 업그레이드를 완료하는 데 허용되는 총 시간이 있습니다. 이 시간은 `10 minutes * total number of nodes in the cluster`의 곱을 복용하여 계산됩니다. 예를 들어 20개의 노드 클러스터에서 업그레이드 작업은 200분 내에 성공해야 하며 AKS는 복구할 수 없는 클러스터 상태를 피하기 위해 작업에 실패합니다. 업그레이드 실패 시 복구하려면 시간 시간이 지난 후 업그레이드 작업을 다시 시도합니다.

업그레이드가 성공적이었는지 확인하려면 [az aks show][az-aks-show] 명령을 사용합니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력은 클러스터가 이제 *1.13.10을*실행한다는 것을 보여 주며 다음과 같은 것을 보여줍니다.

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
