---
title: AKS에서 Azure 디스크 사용
description: AKS에서 Azure 디스크 사용
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597070"
---
# <a name="volumes-with-azure-disks"></a>Azure 디스크가 포함된 볼륨

컨테이너 기반 응용 프로그램은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Azure 디스크를 외부 데이터 저장소로 사용할 수 있습니다. 이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 Azure 디스크를 Kubernetes 볼륨으로 사용하는 방법을 자세히 설명합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [Kubernetes 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="create-an-azure-disk"></a>Azure 디스크 만들기

Azure 관리 디스크를 Kubernetes 볼륨으로 탑재하려면 먼저 디스크가 AKS **노드** 리소스 그룹에 있어야 합니다. [az resource show][az-resource-show] 명령으로 리소스 그룹 이름을 가져옵니다.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

[az disk create][az-disk-create] 명령을 사용하여 Azure 디스크를 만듭니다.

이전 단계에서 수집한 리소스 그룹의 이름으로 `--resource-group`을, 사용자가 원하는 이름으로 `--name`을 업데이트합니다.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

디스크를 만든 후 다음과 비슷한 출력이 표시됩니다. 이 값은 디스크를 탑재할 때 사용되는 디스크 ID입니다.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>볼륨으로 디스크 탑재

컨테이너 사양에서 볼륨을 구성하여 Azure 디스크를 pod에 탑재합니다.

다음 내용이 포함된 새 파일 `azure-disk-pod.yaml`을 만듭니다. `diskName`을 새로 만든된 디스크의 이름으로, `diskURI`를 디스크 ID로 업데이트합니다. 또한 Azure 디스크를 pod에 탑재하는 경로인 `mountPath`를 적어둡니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

kubectl을 사용하여 Pod를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

이제 Azure 디스크가 `/mnt/azure`에 탑재된 Pod가 실행되고 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 디스크를 사용하는 Kubernetes 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure 디스크용 Kubernetes 플러그 인][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
