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
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: bae60e7f78934deacac173767ca3013ce93cf9ad
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Kubernetes에서 Azure Files 사용

컨테이너 기반 응용 프로그램은 종종 외부 데이터 볼륨의 데이터를 액세스하고 유지해야 합니다. Azure 파일은 외부 데이터 저장소로 사용할 수 있습니다. 이 문서에서는 Azure Container Service에서 Azure 파일을 Kubernetes 볼륨으로 사용하는 방법을 자세히 설명합니다.

Kubernetes 볼륨에 대한 자세한 내용은 [Kubernetes 볼륨][kubernetes-volumes]을 참조하세요.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure 파일 공유를 Kubernetes 볼륨을 사용하려면 먼저 Azure Storage 계정과 파일 공유를 만들어야 합니다. 이 작업에는 다음 스크립트를 사용할 수 있습니다. 매개 변수 값을 기록해 두세요. Kubernetes 볼륨을 만들 때 이 값이 필요합니다.

```azurecli-interactive
# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)
```

## <a name="create-kubernetes-secret"></a>Kubernetes 비밀 만들기

Kubernetes에는 파일 공유에 액세하기 위해 자격 증명이 필요합니다. 자격 증명은 [Kubernetes 비밀][kubernetes-secret]에 저장됩니다. Kubernetes 비밀은 Kubernetes Pod를 만들 때 참조됩니다.

Kubernetes 비밀을 만들 때는 비밀 값을 base64로 인코딩해야 합니다.

먼저 저장소 계정의 이름을 인코딩합니다. 필요한 경우 `$AKS_PERS_STORAGE_ACCOUNT_NAME`을 Azure Storage 계정 이름으로 바꿉니다.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

그런 다음 저장소 계정 키를 인코딩합니다. 필요한 경우 `$STORAGE_KEY`를 Azure Storage 계정 키 이름으로 바꿉니다.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
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

[kubectl creat][kubectl-create] 명령을 사용하여 비밀을 만듭니다.

```azurecli-interactive
kubectl create -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>파일 공유를 볼륨으로 탑재

해당 사양에서 볼륨을 구성하여 Azure 파일 공유를 Pod에 탑재할 수 있습니다. 다음 내용이 포함된 새 파일 `azure-files-pod.yml`을 만듭니다. `aksshare`을 Azure 파일 공유에 지정된 이름으로 업데이트합니다.

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
      shareName: aksshare
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
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create