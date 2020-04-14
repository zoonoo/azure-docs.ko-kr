---
title: Azure Kubernetes 서비스(AKS)에서 시스템 노드 풀 사용
description: AZURE Kubernetes 서비스(AKS)에서 시스템 노드 풀을 만들고 관리하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: b567d9e618877463e1e659f368d35fbb787a4ef2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259071"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스(AKS)에서 시스템 노드 풀 관리

AZURE Kubernetes 서비스(AKS)에서 동일한 구성의 노드가 *노드 풀로 그룹화됩니다.* 노드 풀에는 응용 프로그램을 실행하는 기본 VM이 포함됩니다. 시스템 노드 풀과 사용자 노드 풀은 AKS 클러스터에 대해 서로 다른 두 개의 노드 풀 모드입니다. 시스템 노드 풀은 CoreDNS 및 터널프론트와 같은 중요한 시스템 포드를 호스팅하는 주요 용도로 사용됩니다. 사용자 노드 풀은 응용 프로그램 포드를 호스팅하는 주요 용도로 사용됩니다. 그러나 AKS 클러스터에 하나의 풀만 있으면 시스템 노드 풀에서 응용 프로그램 창을 예약할 수 있습니다. 모든 AKS 클러스터에는 노드가 하나 이상 있는 하나 이상의 시스템 노드 풀이 포함되어야 합니다. 

> [!Important]
> 프로덕션 환경에서 AKS 클러스터에 대한 단일 시스템 노드 풀을 실행하는 경우 노드 풀에 대해 세 개 이상의 노드를 사용하는 것이 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure CLI 버전 2.3.1 이상 설치 및 구성이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations"></a>제한 사항

시스템 노드 풀을 지원하는 AKS 클러스터를 만들고 관리할 때 다음 제한 사항이 적용됩니다.

* [Azure Kubernetes 서비스(AKS)에서 할당량, 가상 시스템 크기 제한 및 지역 가용성을][quotas-skus-regions]참조하십시오.
* AKS 클러스터는 가상 시스템 스케일 세트를 VM 유형으로 빌드해야 합니다.
* 노드 풀의 이름은 소문자 숫자 문자만 포함할 수 있으며 소문자로 시작해야 합니다. Linux 노드 풀의 경우 길이는 1에서 12자 사이여야 합니다. Windows 노드 풀의 경우 길이는 1에서 6자 사이여야 합니다.

## <a name="system-and-user-node-pools"></a>시스템 및 사용자 노드 풀

시스템 노드 풀 노드에는 각각 **kubernetes.azure.com/mode 레이블이 있습니다.** 모든 AKS 클러스터에는 하나 이상의 시스템 노드 풀이 포함됩니다. 시스템 노드 풀에는 다음과 같은 제한 사항이 있습니다.

* 시스템 풀 osType은 Linux여야 합니다.
* 사용자 노드 풀 osType리눅스 또는 윈도우일 수 있습니다.
* 시스템 풀에는 하나 이상의 노드가 포함되어야 하며 사용자 노드 풀에는 0개 이상의 노드가 포함될 수 있습니다.
* 시스템 노드 풀에는 VM SKU가 최소 2vCPU와 4GB 메모리가 필요합니다.
* 시스템 노드 풀은 [포드에 대한 최소 값 및 최대값 수식으로][maximum-pods]설명된 대로 최소 30개의 포드를 지원해야 합니다.
* 스팟 노드 풀에는 사용자 노드 풀이 필요합니다.

노드 풀을 사용 하 고 다음 작업을 수행할 수 있습니다.

* AKS 클러스터에서 해당 위치를 차지할 다른 시스템 노드 풀이 있는 경우 시스템 노드 풀을 사용자 노드 풀로 변경합니다.
* 사용자 노드 풀을 시스템 노드 풀로 변경합니다.
* 사용자 노드 풀을 삭제합니다.
* AKS 클러스터에서 해당 위치를 차지할 다른 시스템 노드 풀이 있는 경우 시스템 노드 풀을 삭제할 수 있습니다.
* AKS 클러스터에는 여러 시스템 노드 풀이 있을 수 있으며 하나 이상의 시스템 노드 풀이 필요합니다.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>시스템 노드 풀로 새 AKS 클러스터 만들기

새 AKS 클러스터를 만들 면 단일 노드가 있는 시스템 노드 풀을 자동으로 만듭니다. 초기 노드 풀은 기본적으로 형식 시스템 모드로 설정됩니다. az aks 노드풀이 추가된 새 노드 풀을 만들 때 모드 매개 변수를 명시적으로 지정하지 않는 한 해당 노드 풀은 사용자 노드 풀입니다.

다음 예제에서는 *eastus* 지역에서 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제는 하나의 노드를 포함하는 하나의 시스템 풀을 가진 *myAKSCluster라는* 클러스터를 만듭니다. 프로덕션 워크로드의 경우 3개 이상의 노드가 있는 시스템 노드 풀을 사용하고 있는지 확인합니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>기존 AKS 클러스터에 시스템 노드 풀 추가

기존 AKS 클러스터에 하나 이상의 시스템 노드 풀을 추가할 수 있습니다. 다음 명령은 기본 개수가 3개인 모드 유형 시스템의 노드 풀을 추가합니다.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>노드 풀에 대한 세부 정보 표시

다음 명령을 통해 노드 풀의 세부 정보를 확인할 수 있습니다.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

**시스템** 형식 모드는 시스템 노드 풀에 대해 정의되고 **사용자** 유형 모드는 사용자 노드 풀에 대해 정의됩니다.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>시스템 및 사용자 노드 풀 업데이트

시스템 및 사용자 노드 풀에 대한 모드를 변경할 수 있습니다. 다른 시스템 노드 풀이 AKS 클러스터에 이미 있는 경우에만 시스템 노드 풀을 사용자 풀로 변경할 수 있습니다.

이 명령은 시스템 노드 풀을 사용자 노드 풀로 변경합니다.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

이 명령은 사용자 노드 풀을 시스템 노드 풀로 변경합니다.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>시스템 노드 풀 삭제

> [!Note]
> API 버전 2020-03-02 전에 AKS 클러스터에서 시스템 노드 풀을 사용하려면 새 시스템 노드 풀을 추가한 다음 원래 기본 노드 풀을 삭제합니다.

이전에는 AKS 클러스터의 초기 기본 노드 풀인 시스템 노드 풀을 삭제할 수 없었습니다. 이제 클러스터에서 노드 풀을 유연하게 삭제할 수 있습니다. AKS 클러스터에는 하나 이상의 시스템 노드 풀이 필요하므로 AKS 클러스터에 두 개 이상의 시스템 노드 풀이 있어야 그 중 하나를 삭제할 수 있습니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에서 시스템 노드 풀을 만들고 관리하는 방법을 배웠습니다. 여러 노드 풀을 사용하는 방법에 대한 자세한 내용은 [여러 노드 풀 사용을][use-multiple-node-pools]참조하십시오.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30