---
title: AKS(Azure Kubernetes Service) 클러스터 업그레이드
description: AKS (Azure Kubernetes Service) 클러스터를 업그레이드 하 여 최신 기능 및 보안 업데이트를 가져오는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 947d669d436308a550bce31f04c7b1a2b8a8485a
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734355"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 업그레이드

AKS 클러스터 수명 주기에는 최신 Kubernetes 버전으로 정기적으로 업그레이드 하는 작업이 포함 됩니다. 최신 기능을 얻으려면 최신 보안 릴리스를 적용 하거나 업그레이드 하는 것이 중요 합니다. 이 문서에서는 AKS 클러스터에서 마스터 구성 요소 또는 단일 기본 노드 풀을 업그레이드 하는 방법을 보여 줍니다.

여러 노드 풀 또는 Windows Server 노드를 사용 하는 AKS 클러스터의 경우 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.65 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!WARNING]
> AKS 클러스터 업그레이드는 노드의 cordon 드레이닝을 트리거합니다. 사용 가능한 계산 할당량이 낮은 경우 업그레이드가 실패할 수 있습니다. 자세한 내용은 [할당량 늘리기](../azure-portal/supportability/resource-manager-core-quotas-request.md) 를 참조 하세요.

## <a name="check-for-available-aks-cluster-upgrades"></a>사용할 수 있는 AKS 클러스터 업그레이드를 위한 확인

클러스터에 사용할 수 있는 Kubernetes 릴리스를 확인하려면 [az aks get-upgrades][az-aks-get-upgrades] 명령을 사용합니다. 다음 예에서는 *Myresourcegroup* 에서 *myAKSCluster* 에 대 한 사용 가능한 업그레이드를 확인 합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 지원 되는 AKS 클러스터를 업그레이드 하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 모든 업그레이드는 주 버전 번호로 순차적으로 수행 되어야 합니다. 예를 들어 *1.14*  ->   또는 *1.15*  ->  *1.16* 간 업그레이드를 사용할 수 있지만 *1.14*  ->  *1.16* 는 허용 되지 않습니다. 
> > 지원 _되지 않는 버전_ 에서 _지원 되는 버전_ 으로 업그레이드 하는 경우에만 여러 버전을 건너뛸 수 있습니다. 예를 들어 *지원 되는* *1.10* . x >에서 업그레이드를 완료할 수 있습니다.

다음 예제 출력에서는 *1.19.1* 및 *1.19.3* 버전으로 클러스터를 업그레이드할 수 있음을 보여 줍니다.

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

업그레이드를 사용할 수 없는 경우 다음 메시지를 받게 됩니다.

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>노드 서 지 업그레이드 사용자 지정

> [!Important]
> 노드 작동에는 각 업그레이드 작업에 대해 요청 된 최대 서 수에 대 한 구독 할당량이 필요 합니다. 예를 들어 노드 풀이 5 개인 클러스터에는 각각 4 개 노드 수가 있는 클러스터에는 총 20 개의 노드가 있습니다. 각 노드 풀의 최대 서 수 값이 50% 이면 업그레이드를 완료 하려면 10 개 노드의 추가 계산 및 IP 할당량 (2 개 노드 * 5 풀)이 필요 합니다.
>
> Azure CNI를 사용 하는 경우 서브넷에 사용할 수 있는 ip가 있는지 확인 하 고 [AZURE cni의 IP 요구 사항을 충족할](configure-azure-cni.md)수 있습니다.

기본적으로 AKS는 하나의 추가 노드를 사용 하 여 서 수로 업그레이드를 구성 합니다. 최대 서 수 설정에 대해 1의 기본값을 사용 하면 이전 버전의 노드를 대체 하기 위해 기존 응용 프로그램을 cordon 하기 전에 추가 노드를 만들어 작업 중단을 최소화할 수 있습니다. 업그레이드 속도와 업그레이드 중단 간에 절충을 사용할 수 있도록 노드 풀 당 최대 서 수 값을 사용자 지정할 수 있습니다. 최대 서 수 값을 늘려도 업그레이드 프로세스가 더 빠르게 완료 되지만 최대 서 수에 대 한 값을 크게 설정 하면 업그레이드 프로세스 중에 중단이 발생할 수 있습니다. 

예를 들어 최대 서 수 값 100%는 가장 빠른 업그레이드 프로세스 (노드 수를 두 배로)를 제공 하지만 노드 풀의 모든 노드를 동시에 방전 시킵니다. 테스트 환경에 대해 이와 같은 더 높은 값을 사용할 수 있습니다. 프로덕션 노드 풀의 경우 33%의 max_surge 설정 하는 것이 좋습니다.

AKS는 정수 값과 최대 서 지의 백분율 값을 모두 허용 합니다. "5"와 같은 정수는 서 수에 대 한 추가 노드 5 개를 나타냅니다. 값 "50%"는 풀에서 현재 노드 수의 절반에 해당 하는 서 수 값을 나타냅니다. 최대 서 수 값은 최소 1%이 고 최대값은 100%가 될 수 있습니다. 백분율 값은 가장 가까운 노드 수로 반올림 됩니다. 업그레이드 시 최대 서 수 값이 현재 노드 수보다 적으면 현재 노드 수는 최대 서 수 값에 사용 됩니다.

업그레이드 하는 동안 최대 서 수 값은 1이 고 최대 값은 노드 풀의 노드 수와 같아야 합니다. 더 큰 값을 설정할 수 있지만 최대 서 수에 사용 되는 최대 노드 수는 업그레이드 시 풀의 노드 수보다 높아야 합니다.

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

AKS 클러스터에 대한 사용 가능한 버전 목록을 통해 [az aks upgrade][az-aks-upgrade] 명령을 사용하여 업그레이드합니다. 업그레이드 프로세스 중에 AKS은 다음을 수행 합니다. 
- 지정 된 Kubernetes 버전을 실행 하는 클러스터에 새 버퍼 노드 (또는 [최대 과전류](#customize-node-surge-upgrade)에 구성 된 노드 수)를 추가 합니다. 
- 이전 노드 [중 하나를 사용 하 여 실행][kubernetes-drain] 중인 응용 프로그램의 중단을 최소화 합니다. 최대 서 수를 사용 하는 경우 지정 된 버퍼 노드 수와 동시에 노드 수를 최소화 [하 고 드레이닝][kubernetes-drain] 합니다. 
- 이전 노드가 완전히 방전 되 면 새 버전을 수신 하는 것이 이미지로 다시 설치 다음 노드를 업그레이드할 버퍼 노드가 됩니다. 
- 이 프로세스는 클러스터의 모든 노드가 업그레이드 될 때까지 반복 됩니다. 
- 프로세스가 끝나면 마지막 버퍼 노드가 삭제 되어 기존 에이전트 노드 수와 영역 잔액이 유지 됩니다.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

노드 수에 따라 클러스터를 업그레이드하는 데 몇 분 정도 걸립니다.

> [!IMPORTANT]
> `PodDisruptionBudgets`(Pdb)가 한 번에 1 개 이상의 pod 복제본을 이동 하도록 허용 해야 합니다. 그렇지 않으면 드레이닝/제거 작업이 실패 합니다.
> 드레이닝 작업이 실패 하는 경우 응용 프로그램이 중단 되지 않도록 설계에서 업그레이드 작업이 실패 합니다. 작업 중지 (잘못 된 Pdb, 할당량 부족 등)의 원인을 수정한 후 작업을 다시 시도 하십시오.

업그레이드가 성공적이었는지 확인하려면 [az aks show][az-aks-show] 명령을 사용합니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력에서는 클러스터가 이제 *1.13.10* 를 실행 하는 방법을 보여 줍니다.

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>자동 업그레이드 채널 설정

클러스터를 수동으로 업그레이드 하는 것 외에도 클러스터에서 자동 업그레이드 채널을 설정할 수 있습니다. 다음 업그레이드 채널을 사용할 수 있습니다.

|채널| 작업 | 예제
|---|---|---|
| `none`| 자동 업그레이드를 사용 하지 않도록 설정 하 고 클러스터를 현재 버전의 Kubernetes에 보관 합니다.| 변경 되지 않은 상태로 유지 되는 경우 기본 설정|
| `patch`| 보조 버전을 동일 하 게 유지 하면서 클러스터를 사용할 수 있게 되 면 지원 되는 최신 패치 버전으로 자동 업그레이드 합니다.| 예를 들어 클러스터가 버전 *1.17.7* 을 실행 하 고 *1.17.9*, *1.18.4*, *1.18.6* 및 *1.19.1* 을 사용할 수 있는 경우 클러스터가 *1.17.9* 로 업그레이드 됩니다.|
| `stable`| 부 버전 *n-1* 에서 지원 되는 최신 패치 릴리스로 클러스터를 자동으로 업그레이드 합니다. 여기서 *n* 은 지원 되는 최신 부 버전입니다.| 예를 들어 클러스터가 버전 *1.17.7* 을 실행 하 고 *1.17.9*, *1.18.4*, *1.18.6* 및 *1.19.1* 을 사용할 수 있는 경우 클러스터가 *1.18.6* 로 업그레이드 됩니다.
| `rapid`| 지원 되는 최신 부 버전에서 클러스터를 지원 되는 최신 패치 릴리스로 자동으로 업그레이드 합니다.| 클러스터가 n *-2* 부 버전에 있는 Kubernetes의 버전에 있는 경우 *n* 은 지원 되는 최신 부 버전입니다. n *-1* 부 버전에서는 클러스터가 먼저 지원 되는 최신 패치 버전으로 업그레이드 됩니다. 예를 들어 클러스터가 버전 *1.17.7* 을 실행 하 고 *1.17.9*, *1.18.4*, *1.18.6* 및 *1.19.1* 을 사용할 수 있는 경우 클러스터는 먼저 *1.18.6* 로 업그레이드 된 다음 *1.19.1* 로 업그레이드 됩니다.

> [!NOTE]
> 클러스터 자동 업그레이드는 Kubernetes의 GA 버전 으로만 업데이트 되며 미리 보기 버전으로 업데이트 되지 않습니다.

클러스터를 자동으로 업그레이드 하는 것은 클러스터를 수동으로 업그레이드 하는 것과 동일한 프로세스를 따릅니다. 자세한 내용은 [AKS 클러스터 업그레이드][upgrade-cluster]를 참조 하세요.

AKS 클러스터에 대 한 클러스터 자동 업그레이드는 미리 보기 기능입니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

`AutoUpgradePreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 하 여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용 하 여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

클러스터를 만들 때 자동 업그레이드 채널을 설정 하려면 다음 예제와 유사 하 게 *자동 업그레이드 채널* 매개 변수를 사용 합니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

기존 클러스터에서 자동 업그레이드 채널을 설정 하려면 다음 예제와 비슷한 *자동 업그레이드-채널* 매개 변수를 업데이트 합니다.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster