---
title: "Azure Container Instances에서 Azure Files 볼륨 탑재"
description: "Azure Container Instances를 사용하여 상태가 유지되도록 Azure Files 볼륨을 탑재하는 방법에 대해 알아봅니다."
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0f824dad7ba5b661941e952383025e5171f32e55
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>Azure Container Instances를 사용하여 Azure 파일 공유 탑재

Azure Container Instances는 기본적으로 상태 비저장 방식으로 작동합니다. 컨테이너의 작동이 중단되거나 중지되면 모든 상태가 손실됩니다. 컨테이너 수명이 지난 후에도 상태를 유지하려면 외부 저장소에서 볼륨을 탑재해야 합니다. 이 문서에서는 Azure Container Instances에서 사용할 수 있도록 Azure 파일 공유를 탑재하는 방법을 설명합니다.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure Container Instances에서 Azure 파일 공유를 사용하려면 먼저 파일 공유를 만들어야 합니다. 파일 공유를 호스트할 Storage 계정과 파일 공유 자체를 만들려면 다음 스크립트를 실행합니다. Storage 계정 이름은 전역적으로 고유해야 하므로 이 스크립트는 기준 문자열에 임의 값을 추가합니다.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Storage 계정 액세스 세부 정보 가져오기

Azure Container Instances의 볼륨으로 Azure 파일 공유를 탑재하려면 Storage 계정 이름, 공유 이름, Storage 액세스 키의 세 가지 값이 필요합니다.

위의 스크립트를 사용한 경우 끝에 임의 값이 붙은 Storage 계정 이름이 생성되었을 것입니다. 임의 값 부분을 포함한 최종 문자열을 쿼리하려면 다음 명령을 사용합니다.

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

공유 이름은 이미 확인되었으므로(위 스크립트에서는 *acishare*) Storage 계정 키만 확인하면 됩니다. 다음 명령을 사용하면 키를 확인할 수 있습니다.

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Azure Key Vault를 사용하여 저장소 계정 액세스 세부 정보 저장

Storage 계정 키는 데이터 액세스를 보호하므로 Azure Key Vault에 저장하는 것이 좋습니다.

Azure CLI를 사용하여 Key Vault를 만듭니다.

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

`enabled-for-template-deployment` 스위치를 사용하면 Azure Resource Manager가 배포 시에 Key Vault에서 비밀을 가져올 수 있습니다.

Storage 계정 키를 Key Vault에 새 비밀로 저장합니다.

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>볼륨 탑재

Azure 파일 공유를 컨테이너에 볼륨으로 탑재할 때는 두 단계를 수행합니다. 먼저 컨테이너 그룹 정의의 일부분으로 공유 세부 정보를 제공한 다음, 그룹에 있는 하나 이상의 컨테이너 내에 볼륨을 탑재할 방법을 지정합니다.

탑재에 사용할 수 있도록 하려는 볼륨을 정의하려면 Azure Resource Manager 템플릿의 컨테이너 그룹 정의에 `volumes` 배열을 추가한 다음 개별 컨테이너 정의에서 해당 배열을 참조합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

템플릿에는 Storage 계정 이름과 키가 매개 변수로 포함됩니다. 별도의 매개 변수 파일에서 이러한 매개 변수를 제공할 수 있습니다. 매개 변수 파일에 내용을 입력하려면 Storage 계정 이름, Azure Key Vault의 리소스 ID, 그리고 저장소 키를 저장하는 데 사용한 Key Vault 비밀 이름의 세 가지 값이 필요합니다. 이전 단계를 수행한 경우 다음 스크립트를 사용하여 이러한 값을 가져올 수 있습니다.

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

매개 변수 파일에 값을 삽입합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>컨테이너 배포 및 파일 관리

템플릿을 정의한 후에는 Azure CLI를 사용하여 컨테이너를 만들고 해당 볼륨을 탑재할 수 있습니다. 템플릿 파일 이름은 *azuredeploy.json*이고 매개 변수 파일 이름은 *azuredeploy.parameters.json*이라고 가정할 때의 명령줄은 다음과 같습니다.

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group $ACI_PERS_RESOURCE_GROUP
```

컨테이너가 시작되면 **seanmckenna/aci-hellofiles** 이미지를 통해 사용자가 지정한 탑재 경로에서 Azure 파일 공유의 관리 파일로 배포된 간단한 웹앱을 사용할 수 있습니다. [az container show](/cli/azure/container#az_container_show) 명령을 사용하여 웹앱의 IP 주소를 가져옵니다.

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

[Microsoft Azure Storage 탐색기](https://storageexplorer.com)와 같은 도구를 사용하여 파일 공유에 쓰여진 파일을 검색하고 검사할 수 있습니다.

>[!NOTE]
> Azure Resource Manager 템플릿과 매개 변수 파일을 사용하는 방법 및 Azure CLI를 사용한 배포 방법에 대해 자세히 알아보려면 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Container Instances [빠른 시작](container-instances-quickstart.md)을 사용하여 첫 번째 컨테이너의 배포를 수행합니다.
- [Azure Container Instances와 Container Orchestrator 간의 관계](container-instances-orchestrator-relationship.md)에 대해 자세히 알아봅니다.
