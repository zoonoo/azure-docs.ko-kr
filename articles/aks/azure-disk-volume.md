---
title: AKS(Azure Kubernetes Service)에서 Pod용 정적 볼륨 만들기
description: AKS(Azure Kubernetes Service)에서 Pod에 사용할 Azure 디스크가 포함된 볼륨을 수동으로 만드는 방법에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 9017c8cf721fbb9c493dc18da769b9d6e83ddf05
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616130"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure 디스크가 포함된 볼륨을 수동으로 만들어 사용

컨테이너 기반 애플리케이션은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Pod 하나가 저장소에 액세스해야 하는 경우 Azure 디스크를 사용하여 응용 프로그램에서 사용하도록 할 기본 볼륨을 제공할 수 있습니다. 이 문서에서는 Azure 디스크를 수동으로 만들어 AKS의 Pod에 연결하는 방법을 설명합니다.

> [!NOTE]
> Azure 디스크는 한 번에 한 Pod에만 탑재할 수 있습니다. 여러 pod에서 영구적 볼륨을 공유 해야 하는 경우 사용 하 여 [Azure Files][azure-files-volume]합니다.

Kubernetes 볼륨에 대 한 자세한 내용은 참조 하세요. [AKS에서 응용 프로그램에 대 한 저장소 옵션][concepts-storage]합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터에 필요한 경우 AKS 빠른 시작을 참조 하세요 [Azure CLI를 사용 하 여][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]합니다.

또한 Azure cli 버전 2.0.59 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드를 참조 해야 하는 경우 [Azure CLI 설치][install-azure-cli]합니다.

## <a name="create-an-azure-disk"></a>Azure 디스크 만들기

AKS에서 사용할 Azure 디스크를 만들 때는 **노드** 리소스 그룹에 디스크 리소스를 만들 수 있습니다. 이러한 방식을 사용하면 AKS 클러스터가 디스크 리소스에 액세스하고 해당 리소스를 관리할 수 있습니다. 이 방식 대신 별도의 리소스 그룹에 디스크를 만들어야 하는 경우에는 클러스터의 AKS(Azure Kubernetes Service) 서비스 주체에 디스크 리소스 그룹에 대한 `Contributor` 역할을 부여해야 합니다.

이 문서에서는 노드 리소스 그룹에 디스크를 만듭니다. 먼저 리소스 그룹 이름을 가져옵니다 합니다 [az aks show][az-aks-show] 명령 및 추가 `--query nodeResourceGroup` 쿼리 매개 변수입니다. 다음 예제는 *myResourceGroup* 리소스 그룹에서 AKS 클러스터 *myAKSCluster*의 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

이제 사용 하 여 디스크를 만들 합니다 [az 디스크 만들기][az-disk-create] 명령입니다. 이전 명령에서 가져온 노드 리소스 그룹 이름을 지정한 다음 디스크 리소스의 이름을 *myAKSDisk*와 같이 지정합니다. 다음 예제는 *20*GiB 디스크 및 만든 디스크의 ID 출력 합니다. (현재 AKS에서 미리 보기)는에서 Windows Server 컨테이너를 사용 하 여 사용할 수 있도록 디스크를 만들려는 경우 추가 `--os-type windows` 올바르게 디스크를 포맷 하려면 매개 변수입니다.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Azure 디스크는 특정 크기가 되면 SKU를 기준으로 요금이 청구됩니다. 이러한 Sku 범위에서 S4 또는 P4 디스크용 32GiB (미리 보기)에서 S80 또는 P80 디스크용 32TiB입니다. 프리미엄 관리 디스크의 처리량 및 IOPS 성능은 SKU 및 AKS 클러스터에서 노드의 인스턴스 크기에 따라 달라집니다. 참조 [가격 책정 및 Managed Disks의 성능을][managed-disk-pricing-performance]합니다.

다음 예제 출력에 나와 있는 것처럼 명령이 정상적으로 완료되면 디스크 리소스 ID가 표시됩니다. 이 디스크 ID를 사용하여 다음 단계에서 디스크를 탑재합니다.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>볼륨으로 디스크 탑재

Pod에 Azure 디스크를 탑재하려면 컨테이너 사양에서 볼륨을 구성합니다. 다음 내용이 포함된 새 파일 `azure-disk-pod.yaml`을 만듭니다. `diskName`은 이전 단계에서 만든 디스크의 이름으로, `diskURI`는 disk create 명령의 출력에 표시된 디스크 ID로 업데이트합니다. 원하는 경우 Pod에서 Azure 디스크가 탑재되는 경로인 `mountPath`를 업데이트합니다. Windows Server 컨테이너 (현재 미리 보기 AKS에서)를 지정는 *mountPath* 와 같은 Windows 경로 규칙을 사용 하 여 *'d ':* 합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
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

`kubectl` 명령을 사용하여 Pod을 작성합니다.

```console
kubectl apply -f azure-disk-pod.yaml
```

이제 Azure 디스크가 `/mnt/azure`에 탑재된 Pod가 실행됩니다. `kubectl describe pod mypod`를 사용하여 디스크가 제대로 탑재되었는지 확인할 수 있습니다. 압축된 다음 예제 출력에서는 컨테이너에 탑재된 볼륨을 보여 줍니다.

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>다음 단계

관련된 모범 사례를 참조 하세요 [저장소 및 백업 AKS에 대 한 유용한][operator-best-practices-storage]합니다.

AKS에 대 한 자세한 내용은 클러스터 Azure 디스크와 상호 작용, 참조를 [Azure 디스크용 Kubernetes 플러그 인][kubernetes-disks]합니다.

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
