---
title: Azure NetApp 파일을 Azure Kubernetes 서비스와 통합
description: Azure NetApp 파일을 Azure Kubernetes 서비스와 통합하는 방법에 대해 알아봅니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 09/26/2019
ms.author: zarhoads
ms.openlocfilehash: 1c4996df66d475c63110e3d2797f55598fd85b8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273743"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp 파일을 Azure Kubernetes 서비스와 통합

[Azure NetApp 파일은][anf] Azure에서 실행되는 엔터프라이즈급 고성능, 데이터 통신 파일 저장소 서비스입니다. 이 문서에서는 Azure NetApp 파일을 AKS(Azure Kubernetes 서비스)와 통합하는 방법을 보여 주며, 이 문서에서는 Azure NetApp 파일을 통합하는 방법을 보여 주며,

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

> [!IMPORTANT]
> AKS 클러스터는 [Azure NetApp 파일을 지원하는 리전에도][anf-regions]있어야 합니다.

또한 Azure CLI 버전 2.0.59 이상설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="limitations"></a>제한 사항

Azure NetApp 파일을 사용할 때 다음과 같은 제한 사항이 적용됩니다.

* Azure NetApp 파일은 [일부 Azure 지역에서만][anf-regions]사용할 수 있습니다.
* Azure NetApp 파일을 사용하려면 먼저 Azure NetApp 파일 서비스에 대한 액세스 권한을 부여받아야 합니다. 액세스를 신청하려면 [Azure NetApp 파일 대기자 명단 제출 양식을][anf-waitlist]사용할 수 있습니다. Azure NetApp 파일 팀에서 공식 확인 이메일을 받을 때까지 Azure NetApp 파일 서비스에 액세스할 수 없습니다.
* Azure NetApp 파일 서비스는 AKS 클러스터와 동일한 가상 네트워크에서 만들어야 합니다.
* AKS 클러스터를 처음 배포한 후에는 Azure NetApp 파일에 대한 정적 프로비저닝만 지원됩니다.
* Azure NetApp 파일과 동적 프로비저닝을 사용하려면 [NetApp Trident](https://netapp-trident.readthedocs.io/) 버전 19.07 이상을 설치하고 구성합니다.

## <a name="configure-azure-netapp-files"></a>Azure NetApp 파일 구성

> [!IMPORTANT]
> *Microsoft.NetApp* 리소스 공급자를 등록하려면 먼저 구독에 대한 [Azure NetApp 파일 대기자 명단 제출 양식을][anf-waitlist] 작성해야 합니다. Azure NetApp 파일 팀에서 공식 확인 이메일을 받기 전까지는 리소스 제공을 등록할 수 없습니다.

*Microsoft.NetApp* 리소스 공급자등록:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 이 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다.

AKS와 함께 사용할 Azure NetApp 계정을 만들 때 **노드** 리소스 그룹에서 계정을 만들어야 합니다. 먼저 [az aks show][az-aks-show] 명령을 사용하여 리소스 그룹 이름을 가져온 다음 `--query nodeResourceGroup` 쿼리 매개 변수를 추가합니다. 다음 예제에서는 리소스 그룹 이름 *myResourceGroup에서* *myAKSCluster라는* AKS 클러스터의 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

[az netappfiles][az-netappfiles-account-create]계정을 사용하여 **노드** 리소스 그룹과 AKS 클러스터와 동일한 지역에서 Azure NetApp 파일 계정을 만듭니다. 다음 예제에서는 *MC_myResourceGroup_myAKSCluster_eastus* 리소스 그룹 및 *eastus* 지역에서 *myaccount1이라는* 계정을 만듭니다.

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[az netappfiles 풀][az-netappfiles-pool-create]create를 사용하여 새 용량 풀을 만듭니다. 다음 예제에서는 4TB의 크기와 *프리미엄* 서비스 수준으로 *mypool1이라는* 새 용량 풀을 만듭니다.

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

az 네트워크 vnet 서브넷 을 사용하여 [Azure NetApp 파일에 위임하는][anf-delegate-subnet] [서브넷을 만듭니다.][az-network-vnet-subnet-create] *이 서브넷은 AKS 클러스터와 동일한 가상 네트워크에 있어야 합니다.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

[az netappfiles 볼륨 만들기를 사용하여 볼륨을 만듭니다.][az-netappfiles-volume-create]

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that creation token needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --creation-token $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>영구 볼륨 만들기

[az netappfiles 볼륨 쇼를][az-netappfiles-volume-show] 사용하여 볼륨의 세부 사항을 나열

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

영구 `pv-nfs.yaml` 볼륨 정의 를 만듭니다. 만들기토큰으로 *creationToken* 바꾸고 이전 명령의 *ipAddress로* 바꿉니다. `path` `server` 예를 들어:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

이전 단계에서 만든 NFS(네트워크 파일 시스템) 볼륨값에 대한 *서버* 및 *경로를* 업데이트합니다. [kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨 만들기:

```console
kubectl apply -f pv-nfs.yaml
```

영구 볼륨의 *상태가* [kubectl 설명][kubectl-describe] 명령을 사용하여 *사용할 수 있는지* 확인합니다.

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>영구 볼륨 클레임 만들기

영구 `pvc-nfs.yaml` 볼륨 정의 를 만듭니다. 예를 들어:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

[kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨 클레임 만들기:

```console
kubectl apply -f pvc-nfs.yaml
```

영구 볼륨 클레임의 *상태가* [kubectl 설명][kubectl-describe] 명령을 사용하여 *바인딩되어* 있는지 확인합니다.

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>포드와 함께 마운트

영구 `nginx-nfs.yaml` 볼륨 클레임을 사용하는 포드를 정의합니다. 예를 들어:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

[kubectl apply][kubectl-apply] 명령을 사용하여 포드 만들기:

```console
kubectl apply -f nginx-nfs.yaml
```

[kubectl 설명][kubectl-describe] 명령을 사용하여 포드가 *실행 되고* 있는지 확인합니다.

```console
kubectl describe pod nginx-nfs
```

[kubectl exec을][kubectl-exec] 사용하여 포드에 연결한 다음 `df -h` 볼륨이 장착되었는지 확인하여 볼륨이 포드에 장착되었는지 확인합니다.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>다음 단계

Azure NetApp 파일에 대한 자세한 내용은 [Azure NetApp 파일입니다.][anf] AKS에서 NFS를 사용하는 방법에 대한 자세한 내용은 [Azure Kubernetes 서비스(AKS)에서 NFS(네트워크 파일 시스템) Linux 서버 볼륨을 수동으로 만들고 사용하는][aks-nfs]것을 참조하십시오.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
