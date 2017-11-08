---
title: "AKS에서 Azure 파일 사용 | Microsoft Docs"
description: "AKS에서 Azure 디스크 사용"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Kubernetes에서 Azure Files 사용

컨테이너 기반 응용 프로그램은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Azure 파일은 외부 데이터 저장소로 사용할 수 있습니다. 이 문서에서는 Azure Container Service에서 Azure 파일을 Kubernetes 볼륨으로 사용하는 방법을 자세히 설명합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [Kubernetes 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="creating-a-file-share"></a>파일 공유 만들기

기존 Azure 파일 공유를 Azure Container Service에서 사용할 수 있습니다. 만들어야 할 경우에는 다음 명령 집합을 사용합니다.

[az group create][az-group-create] 명령을 사용하여 Azure 파일 공유에 대한 리소스 그룹을 만듭니다. 저장소 계정의 리소스 그룹 및 HDInsight 클러스터는 동일한 지역에 있어야 합니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

[az storage account create][az-storage-create] 명령을 사용하여 Azure Storage 계정을 만들 수 있습니다. 저장소 계정 이름은 고유해야 합니다. `--name` 인수 값을 고유한 값으로 업데이트합니다.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

[az storage account keys list ][az-storage-key-list] 명령을 사용하여 저장소 계정 키를 반환합니다. `--account-name` 인수 값을 고유한 저장소 계정 이름으로 업데이트합니다.

이후 단계에서 필요하므로 키 값 중 하나를 적어둡니다.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

[az storage share create][az-storage-share-create] 명령을 사용하여 Azure 파일 공유를 만듭니다. `--account-key` 값을 값과 마지막 단계에서 수집한 값으로 업데이트합니다.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Kubernetes 비밀 만들기

Kubernetes에는 파일 공유에 액세하기 위해 자격 증명이 필요합니다. 각 Pod와 함께 Azure Storage 계정 이름 및 키를 저장하는 대신, [Kubernetes 비밀][kubernetes-secret]에 한 번 저장된 후 각 Azure Files 볼륨에서 참조됩니다. 

Kubernetes 비밀 매니페스트의 값은 base64로 인코딩되어야 합니다. 다음 명령을 사용하여 인코딩된 값을 반환합니다.

먼저 저장소 계정의 이름을 인코딩합니다. `storage-account`를 Azure Storage 계정 이름으로 바꿉니다.

```azurecli-interactive
echo -n <storage-account> | base64
```

다음에는 저장소 계정 액세스 키가 필요합니다. 다음 명령을 실행하여 인코딩된 키를 반환합니다. `storage-key`를 이전 단계에서 수집한 키로 바꿉니다.

```azurecli-interactive
echo -n <storage-key> | base64
```

파일 `azure-secret.yml`을 만들고 다음 YAML에 복사합니다. `azurestorageaccountname` 및 `azurestorageaccountkey` 값을 마지막 단계에서 검색한 base64 인코딩 값으로 업데이트합니다.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

[kubectl apply][kubectl-apply] 명령을 사용하여 비밀을 만듭니다.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>파일 공유를 볼륨으로 탑재

해당 사양에서 볼륨을 구성하여 Azure 파일 공유를 Pod에 탑재할 수 있습니다. 다음 내용이 포함된 새 파일 `azure-files-pod.yml`을 만듭니다. `share-name`을 Azure 파일 공유에 지정된 이름으로 업데이트합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

kubectl을 사용하여 Pod를 만듭니다.

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

이제 Azure 파일 공유가 `/mnt/azure` 디렉터리에 탑재된 컨테이너가 실행되고 있습니다. `kubectl describe pod azure-files-pod`를 통해 Pod를 검사하여 볼륨 탑재를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Files를 사용하는 Kubernetes 볼륨에 대해 자세히 알아보기

> [!div class="nextstepaction"]
> [Azure Files용 Kubernetes 플러그 인](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create