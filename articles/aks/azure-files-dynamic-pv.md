---
title: "AKS에서 Azure File 사용"
description: "AKS에서 Azure 디스크 사용"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ce37cfdd70f95822a912f6ea71b9e4a3f9a30a14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Azure Files가 포함된 영구적 볼륨

영구적 볼륨은 Kubernetes 클러스터에서 사용하도록 프로비전된 저장소 부분을 나타냅니다. 하나 이상의 Pod에서 영구적 볼륨을 사용할 수 있으며 동적 또는 정적으로 프로비전할 수 있습니다. 이 문서에서는 Azure 파일 공유를 AKS 클러스터에 있는 Kubernetes 영구적 볼륨으로 동적으로 프로비전하는 과정을 자세히 설명합니다. 

Kubernetes 영구적 볼륨에 대한 자세한 내용은 [Kubernetes 영구적 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure 파일 공유를 Kubernetes 볼륨으로 동적으로 프로비전하는 경우 AKS 클러스터와 동일한 리소스 그룹에 포함된 모든 저장소 계정을 사용할 수 있습니다. 필요한 경우 AKS 클러스터와 동일한 리소스 그룹에 저장소 계정을 만듭니다. 

적절한 리소스 그룹을 식별하려면 [az group list][az-group-list] 명령을 사용합니다.

```azurecli-interactive
az group list --output table
```

다음 예제 출력은 둘 다 AKS 클러스터와 연관된 리소스 그룹을 보여 줍니다. *MC_myAKSCluster_myAKSCluster_eastus* 등의 이름을 사용하는 리소스 그룹에는 AKS 클러스터 리소스가 포함되며, 여기서 저장소 계정을 만들어야 합니다. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

리소스 그룹이 식별되면 [az storage account create][az-storage-account-create] 명령을 사용하여 저장소 계정을 만듭니다.

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>저장소 클래스 만들기

저장소 클래스는 동적으로 생성되는 영구적 볼륨의 구성 방법을 정의하는 데 사용됩니다. Azure 저장소 계정 이름, SKU, 지역 등의 항목은 저장소 클래스 개체에 정의됩니다. Kubernetes 저장소 클래스에 대한 자세한 내용은 [Kubernetes 저장소 클래스][kubernetes-storage-classes]를 참조하세요.

다음 예제에서는 저장소를 요청할 때 `eastus` 지역에 있는 SKU 유형 `Standard_LRS`의 모든 저장소 계정을 사용할 수 있도록 지정합니다. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

특정 저장소 계정을 사용하려면 `storageAccount` 매개 변수를 사용할 수 있습니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

영구적 볼륨 클레임은 저장소 클래스 개체를 사용하여 저장소 부분을 동적으로 프로비전합니다. Azure Files를 사용하는 경우 저장소 클래스 개체에서 선택 또는 지정된 저장소 계정에 Azure 파일 공유가 생성됩니다.

>  [!NOTE]
>   AKS 클러스터 리소스와 동일한 리소스 그룹에 적합한 저장소 계정이 미리 생성되어 있어야 합니다. 이 리소스 그룹에는 *MC_myAKSCluster_myAKSCluster_eastus* 같은 이름이 지정됩니다. 저장소 계정을 사용할 수 없는 경우 영구적 볼륨 클레임을 통한 Azure 파일 공유 프로비전에 실패합니다. 

다음 매니페스트를 사용하여 `ReadWriteOnce` 액세스 권한으로 크기가 `5GB`인 영구적 볼륨 클레임을 만들 수 있습니다. PVC 액세스 모드에 대한 자세한 내용은 [액세스 모드][access-modes]를 참조하세요.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 저장소가 성공적으로 프로비전되면 볼륨에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 영구적 볼륨 클레임 `azurefile`을 사용하여 `/var/www/html` 경로에 Azure 파일 공유를 탑재하는 Pod를 만듭니다. 

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

## <a name="mount-options"></a>탑재 옵션

다음 표에 설명된 대로 Kubernetes 버전마다 기본 fileMode 및 dirMode 값이 다릅니다. 

| 버전 | 값 |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 이상 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 이상 | 0755 |

버전 1.8.5 이상의 클러스터를 사용하는 경우 저장소 클래스 개체에 탑재 옵션을 지정할 수 있습니다. 다음 예제에서는 `0777`을 설정합니다. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

버전 1.8.0 - 1.8.4의 클러스터를 사용하는 경우 `runAsUser` 값을 `0`으로 설정하여 보안 컨텍스트를 지정할 수 있습니다. Pod 보안 컨텍스트에 대한 자세한 내용은 [보안 컨텍스트 구성][kubernetes-security-context]을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Files를 사용하는 Kubernetes 영구적 볼륨에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Files용 Kubernetes 플러그 인][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create