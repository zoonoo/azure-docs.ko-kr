---
title: Azure NetApp Files와 Azure Kubernetes Service의 통합
description: Azure NetApp Files와 Azure Kubernetes Service를 통합하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776054"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files와 Azure Kubernetes Service의 통합

[Azure NetApp Files][anf]는 Azure에서 실행되는 엔터프라이즈급, 고성능, 요금제 파일 스토리지 서비스입니다. 이 문서에서는 Azure NetApp Files와 AKS(Azure Kubernetes Service)를 통합하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에
이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

> [!IMPORTANT]
> 또한 AKS 클러스터는 [Azure NetApp Files를 지원하는 지역에 있어야][anf-regions] 합니다.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="limitations"></a>제한 사항

Azure NetApp Files를 사용하는 경우 다음 제한 사항이 적용됩니다.

* Azure NetApp Files는 [선택한 Azure 지역][anf-regions]에서만 사용할 수 있습니다.
* Azure NetApp Files를 사용하기 전에 Azure NetApp Files 서비스에 대한 액세스 권한을 부여받아야 합니다. 액세스 권한을 받으려면 [Azure NetApp Files 대기 목록 제출 양식][anf-waitlist]을 사용하거나 https://azure.microsoft.com/services/netapp/#getting-started 로 이동합니다. Azure NetApp Files 팀에서 공식 확인 메일을 받을 때까지 Azure NetApp Files 서비스에 액세스할 수 없습니다.
* AKS 클러스터의 초기 배포 후 Azure NetApp Files에 대한 정적 프로비저닝만 지원됩니다.
* Azure NetApp Files에서 동적 프로비저닝을 사용하려면 [NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07 버전 이상을 설치하고 구성합니다.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files 구성

> [!IMPORTANT]
> *Microsoft.NetApp* 리소스 공급자를 등록하기 전에 [Azure NetApp Files 대기 목록 제출 양식][anf-waitlist]을 작성하거나 구독에 대한 https://azure.microsoft.com/services/netapp/#getting-started 로 이동해야 합니다. Azure NetApp Files 팀에서 공식 확인 메일을 받을 때까지 리소스 공급자를 등록할 수 없습니다.

*Microsoft.NetApp* 리소스 공급자를 등록합니다.

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> 이 작업을 완료하는 데 약간의 시간이 걸릴 수 있습니다.

AKS와 함께 사용할 Azure NetApp 계정을 만들 때 **노드** 리소스 그룹에서 계정을 만들어야 합니다. 먼저 [az aks show][az-aks-show] 명령을 사용하여 리소스 그룹 이름을 가져온 다음 `--query nodeResourceGroup` 쿼리 매개 변수를 추가합니다. 다음 예제는 *myResourceGroup* 리소스 그룹에서 AKS 클러스터 *myAKSCluster* 에 대한 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

[az netappfiles account create][az-netappfiles-account-create]를 사용하여 **노드** 리소스 그룹과 AKS 클러스터와 동일한 지역에 Azure NetApp Files 계정을 만듭니다. 다음 예제에서는 *MC_myResourceGroup_myAKSCluster_eastus* 리소스 그룹 및 *eastus* 지역에 *myaccount1* 이라는 계정을 만듭니다.

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

[az netappfiles pool create][az-netappfiles-pool-create]를 사용하여 새 용량 풀을 만듭니다. 다음 예제에서는 크기가 4TB이고 *프리미엄* 서비스 수준의 *mypool1* 이라는 이름의 새 용량 풀을 만듭니다.

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

[az network vnet subnet create][az-network-vnet-subnet-create]를 사용하여 [Azure NetApp Files에 위임할][anf-delegate-subnet] 서브넷을 만듭니다. *이 서브넷은 AKS 클러스터와 동일한 가상 네트워크에 있어야 합니다.*

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

[az netappfiles volume create][az-netappfiles-volume-create]를 사용하여 볼륨을 만듭니다.

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
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

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
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>PersistentVolume 만들기

[az netappfiles volume show][az-netappfiles-volume-show]를 사용하여 볼륨의 세부 정보 나열

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

PersistentVolume을 정의하는 `pv-nfs.yaml`을 만듭니다. 이전 명령에서 `path`를 *creationToken* 으로, `server`를 *ipAddress* 로 대체합니다. 예를 들면 다음과 같습니다.

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
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

*서버* 및 *경로* 를 이전 단계에서 만든 NFS(네트워크 파일 시스템) 볼륨의 값으로 업데이트합니다. [kubectl apply][kubectl-apply] 명령을 사용하여 PersistentVolume을 만듭니다.

```console
kubectl apply -f pv-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 PersistentVolume의 *Status* 가 *Available* 인지 확인합니다.

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>PersistentVolumeClaim 만들기

PersistentVolume을 정의하는 `pvc-nfs.yaml`을 만듭니다. 예를 들면 다음과 같습니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 PersistentVolumeClaim을 만듭니다.

```console
kubectl apply -f pvc-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 PersistentVolumeClaim의 *Status* 가 *Bound* 인지 확인합니다.

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Pod를 사용하여 탑재

PersistentVolumeClaim을 사용하는 Pod를 정의하는 `nginx-nfs.yaml`을 만듭니다. 예를 들면 다음과 같습니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```console
kubectl apply -f nginx-nfs.yaml
```

[kubectl describe][kubectl-describe] 명령을 사용하여 Pod가 *Running* 인지 확인합니다.

```console
kubectl describe pod nginx-nfs
```

Pod에 연결하기 위해 [kubectl exec][kubectl-exec]를 사용하여 Pod에 해당 볼륨이 탑재되었는지 확인한 다음, `df -h` 명령으로 볼륨이 탑재되었는지 확인합니다.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>다음 단계

Azure NetApp Files에 대한 자세한 내용은 [Azure NetApp Files란?][anf]을 참조하세요. AKS에서 NFS를 사용하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)를 사용하여 NFS(네트워크 파일 시스템) Linux 서버 볼륨 수동 생성 및 사용][aks-nfs]을 참조하세요.


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
