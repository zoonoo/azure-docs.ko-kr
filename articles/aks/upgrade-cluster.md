---
title: AKS(Azure Kubernetes Service) 클러스터 업그레이드
description: AKS (Azure Kubernetes Service) 클러스터를 업그레이드 하 여 최신 기능 및 보안 업데이트를 가져오는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: 603a27f0ecffb762a18f58847110c4dd3de68425
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250994"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 업그레이드

AKS 클러스터의 수명 주기의 일환으로, 최신 Kubernetes 버전으로 업그레이드해야 하는 경우가 많습니다. 최신 Kubernetes 보안 릴리스를 적용하거나 최신 기능으로 업그레이드하는 것은 중요합니다. 이 문서에서는 AKS 클러스터에서 마스터 구성 요소 또는 단일 기본 노드 풀을 업그레이드 하는 방법을 보여 줍니다.

여러 노드 풀 또는 Windows Server 노드를 사용 하는 AKS 클러스터 (현재 AKS에서 미리 보기 상태)의 경우 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.65 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!WARNING]
> AKS 클러스터 업그레이드는 노드의 cordon 드레이닝을 트리거합니다. 사용 가능한 계산 할당량이 낮은 경우 업그레이드가 실패할 수 있습니다. 자세한 내용은 [할당량 늘리기](../azure-portal/supportability/resource-manager-core-quotas-request.md) 를 참조 하세요.

## <a name="check-for-available-aks-cluster-upgrades"></a>사용할 수 있는 AKS 클러스터 업그레이드를 위한 확인

클러스터에 사용할 수 있는 Kubernetes 릴리스를 확인하려면 [az aks get-upgrades][az-aks-get-upgrades] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에서 *myAKSCluster*라는 클러스터로 가능한 업그레이드를 확인합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AKS 클러스터를 업그레이드하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 예를 들어 *1.12*. x  ->  *1.13 x x* x x x x *x x*x x x x x x x x x x x x x x x x x x x x x. x를  ->  사용할 수 있지만*1.14* *1.12.x*  ->  *1.14.x*
>
> 1.14에서 업그레이드 하려면 *1.12.x*  ->  *1.14.x*먼저 *1.12*. x  ->  *1.13.* x에서 업그레이드 한 다음 *1.13.* x  ->  *1.14*에서 업그레이드 합니다.

다음 예제 출력에서는 *1.13.9* 및 *1.13.10*버전으로 클러스터를 업그레이드할 수 있음을 보여 줍니다.

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
업그레이드를 사용할 수 없는 경우 다음을 얻게 됩니다.
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>노드 서 지 업그레이드 사용자 지정 (미리 보기)

> [!Important]
> 노드 작동에는 각 업그레이드 작업에 대해 요청 된 최대 서 수에 대 한 구독 할당량이 필요 합니다. 예를 들어 노드 풀이 5 개인 클러스터에는 각각 4 개 노드 수가 있는 클러스터에는 총 20 개의 노드가 있습니다. 각 노드 풀의 최대 서 수 값이 50% 이면 업그레이드를 완료 하려면 10 개 노드의 추가 계산 및 IP 할당량 (2 개 노드 * 5 풀)이 필요 합니다.
>
> Azure CNI를 사용 하는 경우 서브넷에 사용할 수 있는 ip가 있는지 확인 하 고 [AZURE cni의 IP 요구 사항을 충족할](configure-azure-cni.md)수 있습니다.

기본적으로 AKS는 하나의 추가 노드를 사용 하 여 서 수로 업그레이드를 구성 합니다. 최대 서 수 설정에 대해 1의 기본값을 사용 하면 AKS는 기존 응용 프로그램이 이전 버전의 노드를 대체 하기 전에 추가 노드를 만들어 작업 중단을 최소화할 수 있습니다. 업그레이드 속도와 업그레이드 중단 간에 절충을 사용할 수 있도록 노드 풀 당 최대 서 수 값을 사용자 지정할 수 있습니다. 최대 서 수 값을 늘려도 업그레이드 프로세스가 더 빠르게 완료 되지만 최대 서 수에 대 한 값을 크게 설정 하면 업그레이드 프로세스 중에 중단이 발생할 수 있습니다. 

예를 들어 최대 서 수 값 100%는 가장 빠른 업그레이드 프로세스 (노드 수를 두 배로)를 제공 하지만 노드 풀의 모든 노드를 동시에 방전 시킵니다. 테스트 환경에 대해 이와 같은 더 높은 값을 사용할 수 있습니다. 프로덕션 노드 풀의 경우 33%의 max_surge 설정 하는 것이 좋습니다.

AKS는 정수 값과 최대 서 지의 백분율 값을 모두 허용 합니다. "5"와 같은 정수는 서 수에 대 한 추가 노드 5 개를 나타냅니다. 값 "50%"는 풀에서 현재 노드 수의 절반에 해당 하는 서 수 값을 나타냅니다. 최대 서 수 값은 최소 1%이 고 최대값은 100%가 될 수 있습니다. 백분율 값은 가장 가까운 노드 수로 반올림 됩니다. 업그레이드 시 최대 서 수 값이 현재 노드 수보다 적으면 현재 노드 수는 최대 서 수 값에 사용 됩니다.

업그레이드 하는 동안 최대 서 수 값은 1이 고 최대 값은 노드 풀의 노드 수와 같아야 합니다. 더 큰 값을 설정할 수 있지만 최대 서 수에 사용 되는 최대 노드 수는 업그레이드 시 풀의 노드 수보다 높아야 합니다.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>노드 서 지 업그레이드 사용자 지정을 위한 미리 보기 기능 설정

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

등록 하는 데 몇 분 정도 걸릴 수 있습니다. 다음 명령을 사용 하 여 기능이 등록 되었는지 확인 합니다.

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

미리 보기 중에는 최대 서 수를 사용 하는 *aks-preview* CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> 노드 풀의 최대 서 수 설정은 영구적입니다.  이후 Kubernetes 업그레이드 또는 노드 버전 업그레이드 시이 설정이 사용 됩니다. 언제 든 지 노드 풀의 최대 서 수 값을 변경할 수 있습니다. 프로덕션 노드 풀의 경우 최대 과전류 설정 33%를 권장 합니다.

다음 명령을 사용 하 여 새 노드 풀 또는 기존 노드 풀의 최대 서 수 값을 설정 합니다.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>AKS 클러스터 업그레이드

AKS 클러스터에 대한 사용 가능한 버전 목록을 통해 [az aks upgrade][az-aks-upgrade] 명령을 사용하여 업그레이드합니다. 업그레이드 프로세스가 진행 되는 동안 AKS는 지정 된 Kubernetes 버전을 실행 하는 클러스터에 새 노드를 추가한 다음 실행 중인 응용 프로그램의 중단을 최소화 하기 위해 이전 노드 중 하나를 중단 [하 고 드레이닝][kubernetes-drain] 합니다. 새 노드가 application pod를 실행 하는 것으로 확인 되 면 이전 노드가 삭제 됩니다. 이 프로세스는 클러스터의 모든 노드가 업그레이드 될 때까지 반복 됩니다.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

노드 수에 따라 클러스터를 업그레이드하는 데 몇 분 정도 걸립니다.

> [!NOTE]
> 클러스터 업그레이드를 완료 하는 데 허용 되는 총 시간이 있습니다. 이 시간은의 제품을 활용 하 여 계산 됩니다 `10 minutes * total number of nodes in the cluster` . 예를 들어 20 개 노드 클러스터에서 업그레이드 작업은 200 분 내에 성공 해야 합니다. 그렇지 않으면 AKS는 복구 불가능 한 클러스터 상태를 방지 하기 위해 작업에 실패 합니다. 업그레이드 실패 시 복구 하려면 시간 제한이 적중 된 후 업그레이드 작업을 다시 시도 하세요.

업그레이드가 성공적이었는지 확인하려면 [az aks show][az-aks-show] 명령을 사용합니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력에서는 클러스터가 이제 *1.13.10*를 실행 하는 방법을 보여 줍니다.

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
