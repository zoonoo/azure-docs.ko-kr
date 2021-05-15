---
title: 컨테이너 그룹에 Azure Files 볼륨 탑재
description: Azure Container Instances를 사용하여 상태가 유지되도록 Azure Files 볼륨을 탑재하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 03/24/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c541d4faa8728d99fd07396bc056a3e69dc93fe8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763742"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances에서 Azure 파일 공유 탑재

기본적으로 Azure Container Instances는 비저장 상태입니다. 컨테이너가 다시 시작되거나, 충돌하거나 또는 중지되면 모든 상태가 손실됩니다. 컨테이너 수명이 지난 후에도 상태를 유지하려면 외부 저장소의 볼륨을 탑재해야 합니다. 이 문서에 표시된 대로 Azure Container Instances는 [Azure Files](../storage/files/storage-files-introduction.md)로 만든 Azure 파일 공유를 탑재할 수 있습니다. Azure Files는 업계 표준 SMB(서버 메시지 블록) 프로토콜을 통해 액세스 할 수 있는 Azure Storage에서 호스팅되는 완전 관리형 파일 공유를 제공합니다. Azure Container Instances에서 Azure 파일 공유를 사용하면 Azure Virtual Machines에서 Azure 파일 공유를 사용하는 것과 유사한 파일 공유 기능을 제공합니다.

## <a name="limitations"></a>제한 사항

* Azure Files 공유는 Linux 컨테이너에만 탑재할 수 있습니다. [개요](container-instances-overview.md#linux-and-windows-containers)에서 Linux 및 Windows 컨테이너 그룹에 대한 기능 지원의 차이점에 대해 자세히 검토합니다.
* Azure 파일 공유 볼륨 탑재에는 *루트* 로 실행되는 Linux 컨테이너가 필요합니다.
* Azure 파일 공유 볼륨 탑재는 CIFS 지원으로 제한됩니다.

> [!NOTE]
> Azure Files 공유를 컨테이너 인스턴스에 탑재하는 것은 Docker의 [바인드 탑재](https://docs.docker.com/storage/bind-mounts/)와 유사합니다. 파일 또는 디렉터리가 있는 컨테이너 디렉터리에 공유를 탑재하는 경우 탑재가 파일 또는 디렉터리를 가려 컨테이너가 실행되는 동안 액세스할 수 없도록 합니다.
>

> [!IMPORTANT]
> 컨테이너 그룹을 Azure Virtual Network에 배포하는 경우 Azure Storage 계정에 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 추가해야 합니다.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure Container Instances에서 Azure 파일 공유를 사용하려면 먼저 파일 공유를 만들어야 합니다. 다음 스크립트를 실행하여 파일 공유를 호스팅할 스토리지 계정과 파일 공유 자체를 만듭니다. Storage 계정 이름은 전역적으로 고유해야 하므로 이 스크립트는 기준 문자열에 임의 값을 추가합니다.

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Create the file share
az storage share create \
  --name $ACI_PERS_SHARE_NAME \
  --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME
```

## <a name="get-storage-credentials"></a>스토리지 자격 증명 가져오기

Azure Container Instances의 볼륨으로 Azure 파일 공유를 탑재하려면 Storage 계정 이름, 공유 이름, Storage 액세스 키의 세 가지 값이 필요합니다.

* **스토리지 계정 이름** - 이전 스크립트를 사용한 경우 스토리지 계정 이름이 `$ACI_PERS_STORAGE_ACCOUNT_NAME` 변수에 저장되었습니다. 계정 이름을 확인하려면 다음을 입력하세요.

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **공유 이름** - 이 값은 이미 알려져 있음(이전 스크립트에서 `acishare`으로 정의됨)

* **스토리지 계정 키** - 이 값은 다음 명령을 사용하여 확인할 수 있음:

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>컨테이너 및 탑재 볼륨 배포 - CLI

Azure CLI를 사용하여 Azure 파일 공유를 컨테이너의 볼륨으로 탑재하려면 [az container create][az-container-create]를 사용하여 컨테이너를 만들 때 공유 및 볼륨 탑재 지점을 지정합니다. 이전 단계를 수행한 경우 다음 명령으로 이전에 만든 공유를 탑재하여 컨테이너를 만들 수 있습니다:

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image mcr.microsoft.com/azuredocs/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

`--dns-name-label` 값은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 명령을 실행한 결과 **DNS 이름 레이블** 오류 메시지가 표시되는 경우에는 이전 명령의 값을 업데이트합니다.

## <a name="manage-files-in-mounted-volume"></a>탑재된 볼륨의 파일 관리

컨테이너가 시작되면 Microsoft [aci-hellofiles][aci-hellofiles] 이미지를 통해 배포된 간단한 웹앱을 사용하여 사용자가 지정한 탑재 경로에서 Azure 파일 공유의 작은 텍스트 파일을 만들 수 있습니다. 아래와 같이 [az container show][az-container-show] 명령을 사용하여 웹앱의 FQDN(정규화된 도메인 이름)을 확인합니다.

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

앱을 사용하여 텍스트를 저장한 후 [Azure Portal][portal] 또는 [Microsoft Azure Storage Explorer][storage-explorer]와 같은 도구를 사용하여 파일 공유에 기록된 파일을 검색하고 검사할 수 있습니다.

## <a name="deploy-container-and-mount-volume---yaml"></a>컨테이너 및 탑재 볼륨 배포 - YAML

Azure CLI 및 [YAML 템플릿](container-instances-multi-container-yaml.md)을 사용하여 컨테이너 그룹을 배포하고 컨테이너에 볼륨을 탑재할 수도 있습니다. 여러 컨테이너로 구성된 컨테이너 그룹을 배포할 때에는 YAML 템플릿을 사용하여 배포하는 것이 좋습니다.

다음 YAML 템플릿은 `aci-hellofiles` 이미지로 생성된 하나의 컨테이너로 컨테이너 그룹을 정의합니다. 컨테이너는 이전에 생성된 Azure 파일 공유 *acishare* 를 볼륨으로 탑재합니다. 표시된 곳에 파일 공유를 호스팅하는 스토리지 계정의 이름과 스토리지 키를 입력합니다. 

CLI 예제에서와 같이 `dnsNameLabel` 값은 컨테이너 인스턴스를 만드는 Azure 지역에서 고유해야 합니다. 필요한 경우 YAML 파일 값을 업데이트합니다.

```yaml
apiVersion: '2019-12-01'
location: eastus
name: file-share-demo
properties:
  containers:
  - name: hellofiles
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-hellofiles
      ports:
      - port: 80
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /aci/logs/
        name: filesharevolume
  osType: Linux
  restartPolicy: Always
  ipAddress:
    type: Public
    ports:
      - port: 80
    dnsNameLabel: aci-demo
  volumes:
  - name: filesharevolume
    azureFile:
      sharename: acishare
      storageAccountName: <Storage account name>
      storageAccountKey: <Storage account key>
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

YAML 템플릿을 사용하여 배포하려면 위의 YAML을 `deploy-aci.yaml` 파일에 저장한 다음,  매개 변수를 사용하여 [az container create`--file`][az-container-create] 명령을 실행해야 합니다.

```azurecli
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```
## <a name="deploy-container-and-mount-volume---resource-manager"></a>컨테이너 및 탑재 볼륨 배포 - 리소스 관리자

CLI 및 YAML 배포 외에도 Azure [Resource Manager 템플릿](/azure/templates/microsoft.containerinstance/containergroups)을 사용하여 컨테이너 그룹을 배포하고 컨테이너에 볼륨을 탑재할 수 있습니다.

먼저 템플릿의 `volumes`컨테이너 그룹의 배열`properties` 섹션을 채웁니다. 

그런 다음 볼륨을 탑재하려는 각 컨테이너에 대해 컨테이너 정의의 `properties` 섹션에 `volumeMounts` 배열을 채웁니다.

다음 Resource Manager 템플릿은 `aci-hellofiles` 이미지로 만든 하나의 컨테이너로 컨테이너 그룹을 정의합니다. 컨테이너는 이전에 생성된 Azure 파일 공유 *acishare* 를 볼륨으로 탑재합니다. 표시된 곳에 파일 공유를 호스팅하는 스토리지 계정의 이름과 스토리지 키를 입력합니다. 

이전 예제에서와 같이 `dnsNameLabel` 값은 컨테이너 인스턴스를 만드는 Azure 지역에서 고유해야 합니다. 필요한 경우 템플릿의 값을 업데이트합니다.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "container1name": "hellofiles",
    "container1image": "mcr.microsoft.com/azuredocs/aci-hellofiles"
  },
  "resources": [
    {
      "name": "file-share-demo",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "/aci/logs"
                }
              ]
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            }
          ],
          "dnsNameLabel": "aci-demo"
        },
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
                "shareName": "acishare",
                "storageAccountName": "<Storage account name>",
                "storageAccountKey": "<Storage account key>"
            }
          }
        ]
      }
    }
  ]
}
```

Resource Manager 템플릿을 사용하여 배포하려면 위의 JSON을 `deploy-aci.json` 파일에 저장한 다음, `--template-file` 매개 변수를 사용하여 [az deployment group create][az-deployment-group-create] 명령을 실행합니다:

```azurecli
# Deploy with Resource Manager template
az deployment group create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>여러 볼륨 탑재

컨테이너 인스턴스에 여러 볼륨을 탑재하려면 [Azure Resource Manager 템플릿](/azure/templates/microsoft.containerinstance/containergroups), YAML 파일 또는 다른 프로그래밍 방식을 사용하여 배포해야 합니다. 템플릿 또는 YAML 파일을 사용하려면 먼저 공유 세부 정보를 제공하고 파일의 `properties` 섹션에서 `volumes` 배열을 채워서 볼륨을 정의합니다. 

예를 들어 스토리지 계정 *myStorageAccount* 에서 *공유1* 및 *공유2* 라는 두 개의 Azure Files 공유를 만든 경우 Resource Manager 템플릿의 `volumes` 배열은 다음과 같이 비슷하게 표시됩니다:

```JSON
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

다음으로 볼륨을 탑재하려는 컨테이너 그룹에 있는 각 컨테이너의 경우 컨테이너 정의의 `properties` 섹션에서 `volumeMounts` 배열을 채웁니다. 예를 들어 다음은 이전에 정의한 *myvolume1* 및 *myvolume2* 라는 두 개의 볼륨을 탑재합니다.

```JSON
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

## <a name="next-steps"></a>다음 단계

Azure Container Instances에서 다른 볼륨 유형을 탑재하는 방법을 알아봅니다.

* [Azure Container Instances에서 emptyDir 볼륨 탑재](container-instances-volume-emptydir.md)
* [Azure Container Instances에서 gitRepo 볼륨 탑재](container-instances-volume-gitrepo.md)
* [Azure Container Instances에서 비밀 볼륨 탑재](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/_/microsoft-azuredocs-aci-hellofiles 
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
