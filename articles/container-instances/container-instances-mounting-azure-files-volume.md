---
title: "Azure Container Instances에서 Azure Files 볼륨 탑재"
description: "Azure Container Instances를 사용하여 상태가 유지되도록 Azure Files 볼륨을 탑재하는 방법에 대해 알아봅니다."
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/05/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b2e8e27cecb4d1225e378690063b42f5d5242868
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Azure Container Instances를 사용하여 Azure 파일 공유 탑재

Azure Container Instances는 기본적으로 상태 비저장 방식으로 작동합니다. 컨테이너의 작동이 중단되거나 중지되면 모든 상태가 손실됩니다. 컨테이너 수명이 지난 후에도 상태를 유지하려면 외부 저장소에서 볼륨을 탑재해야 합니다. 이 문서에서는 Azure Container Instances에서 사용할 수 있도록 Azure 파일 공유를 탑재하는 방법을 설명합니다.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure Container Instances에서 Azure 파일 공유를 사용하려면 먼저 파일 공유를 만들어야 합니다. 다음 스크립트를 실행하여 파일 공유를 호스팅할 저장소 계정과 파일 공유 자체를 만듭니다. Storage 계정 이름은 전역적으로 고유해야 하므로 이 스크립트는 기준 문자열에 임의 값을 추가합니다.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Storage 계정 액세스 세부 정보 가져오기

Azure Container Instances의 볼륨으로 Azure 파일 공유를 탑재하려면 Storage 계정 이름, 공유 이름, Storage 액세스 키의 세 가지 값이 필요합니다.

위의 스크립트를 사용한 경우 끝에 임의 값이 붙은 Storage 계정 이름이 생성되었을 것입니다. 임의 값 부분을 포함한 최종 문자열을 쿼리하려면 다음 명령을 사용합니다.

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

공유 이름은 이미 알려져 있으므로(위 스크립트에서 *acishare*로 정의됨) 저장소 계정 키만 확인하면 됩니다. 이 키는 다음 명령을 사용하여 확인할 수 있습니다.

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="deploy-the-container-and-mount-the-volume"></a>컨테이너 배포 및 볼륨 탑재

Azure 파일 공유를 컨테이너의 볼륨으로 탑재하려면 [az container create](/cli/azure/container#az_container_create)를 사용하여 컨테이너를 만들 때 공유 및 볼륨 탑재 지점을 지정합니다. 이전 단계를 수행한 경우 다음 명령으로 이전에 만든 공유를 탑재하여 컨테이너를 만들 수 있습니다.

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>탑재된 볼륨의 파일 관리

컨테이너가 시작되면 [seanmckenna/aci-hellofiles](https://hub.docker.com/r/seanmckenna/aci-hellofiles/) 이미지를 통해 사용자가 지정한 탑재 경로에서 Azure 파일 공유의 관리 파일로 배포된 간단한 웹앱을 사용할 수 있습니다. [az container show](/cli/azure/container#az_container_show) 명령을 사용하여 웹앱의 IP 주소를 가져옵니다.

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

[Azure Portal](https://portal.azure.com) 또는 [Microsoft Azure Storage 탐색기](https://storageexplorer.com)와 같은 도구를 사용하여 파일 공유에 작성된 파일을 검색하고 검사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Container Instances와 컨테이너 오케스트레이터 간의 관계](container-instances-orchestrator-relationship.md)에 대해 자세히 알아봅니다.
