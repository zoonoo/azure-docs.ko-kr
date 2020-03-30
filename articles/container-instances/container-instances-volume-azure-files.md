---
title: 컨테이너 그룹에 Azure 파일 볼륨 마운트
description: Azure Container Instances를 사용하여 상태가 유지되도록 Azure Files 볼륨을 탑재하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 12/30/2019
ms.custom: mvc
ms.openlocfilehash: f66890c503de8de9160f11fb28795012ae57daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561340"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>Azure Container Instances에서 Azure 파일 공유 탑재

Azure Container Instances는 기본적으로 상태 비저장 방식으로 작동합니다. 컨테이너의 작동이 중단되거나 중지되면 모든 상태가 손실됩니다. 컨테이너 수명이 지난 후에도 상태를 유지하려면 외부 저장소에서 볼륨을 탑재해야 합니다. 이 문서에 표시된 것처럼 Azure 컨테이너 인스턴스는 Azure [Files.](../storage/files/storage-files-introduction.md)로 만든 Azure 파일 공유를 탑재할 수 있습니다. Azure Files는 SMB(업계 표준 서버 메시지 블록) 프로토콜을 통해 액세스할 수 있는 Azure Storage에서 호스팅되는 완전히 관리되는 파일 공유를 제공합니다. Azure Container Instances에서 Azure 파일 공유를 사용하면 Azure Virtual Machines에서 Azure 파일 공유를 사용하는 것과 유사한 파일 공유 기능을 제공합니다.

> [!NOTE]
> 현재 Azure 파일 공유를 탑재하는 작업은 Linux 컨테이너로만 제한되어 있습니다. 개요에서 현재 플랫폼 차이점을 찾을 수 [있습니다.](container-instances-overview.md#linux-and-windows-containers)
>
> Azure 파일 공유를 컨테이너 인스턴스에 탑재하는 것은 Docker [바인딩 마운트와](https://docs.docker.com/storage/bind-mounts/)유사합니다. 파일이나 디렉터리가 있는 컨테이너 디렉터리에 공유를 탑재하면 이러한 파일이나 디렉터리는 마운트에 의해 가려지며 컨테이너가 실행되는 동안 액세스할 수 없습니다.
>

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

* **저장소 계정 이름** - 이전 스크립트를 사용한 경우 저장소 계정 `$ACI_PERS_STORAGE_ACCOUNT_NAME` 이름이 변수에 저장되었습니다. 계정 이름을 확인하려면 다음을 입력하세요.

  ```console
  echo $ACI_PERS_STORAGE_ACCOUNT_NAME
  ```

* **이름 공유** - 이 값은 `acishare` 이미 알려져 있습니다(이전 스크립트에서와 같이 정의).

* **저장소 계정 키** - 이 값은 다음 명령을 사용하여 찾을 수 있습니다.

  ```azurecli-interactive
  STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $ACI_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" --output tsv)
  echo $STORAGE_KEY
  ```

## <a name="deploy-container-and-mount-volume---cli"></a>컨테이너 배포 및 마운트 볼륨 - CLI

Azure CLI를 사용하여 Azure 파일 공유를 컨테이너에 볼륨으로 탑재하려면 az 컨테이너를 사용하여 컨테이너를 만들 때 공유 및 볼륨 마운트 지점을 [지정합니다.][az-container-create] 이전 단계를 수행한 경우 다음 명령을 사용하여 컨테이너를 만들어 이전에 만든 공유를 탑재할 수 있습니다.

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

값은 `--dns-name-label` 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 명령을 실행한 결과 **DNS 이름 레이블** 오류 메시지가 표시되는 경우에는 이전 명령의 값을 업데이트합니다.

## <a name="manage-files-in-mounted-volume"></a>탑재된 볼륨의 파일 관리

컨테이너가 시작되면 Microsoft [aci-hellofiles][aci-hellofiles] 이미지를 통해 배포된 간단한 웹 앱을 사용하여 지정한 마운트 경로의 Azure 파일 공유에 작은 텍스트 파일을 만들 수 있습니다. 아래와 같이 [az container show][az-container-show] 명령을 사용하여 웹앱의 FQDN(정규화된 도메인 이름)을 확인합니다.

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP \
  --name hellofiles --query ipAddress.fqdn --output tsv
```

앱을 사용하여 텍스트를 저장한 후 [Azure 포털][portal] 또는 [Microsoft Azure 저장소 탐색기와][storage-explorer] 같은 도구를 사용하여 파일 공유에 기록된 파일 또는 파일을 검색하고 검사할 수 있습니다.

## <a name="deploy-container-and-mount-volume---yaml"></a>컨테이너 배포 및 마운트 볼륨 - YAML

컨테이너 그룹을 배포하고 Azure CLI 및 [YAML 템플릿을](container-instances-multi-container-yaml.md)사용할 수 있는 컨테이너에 볼륨을 탑재할 수도 있습니다. YAML 템플릿에 의한 배포는 여러 컨테이너로 구성된 컨테이너 그룹을 배포할 때 선호되는 방법입니다.

다음 YAML 템플릿은 이미지로 만든 하나의 컨테이너가 있는 컨테이너 그룹을 정의합니다. `aci-hellofiles` 컨테이너는 이전에 볼륨으로 만든 Azure 파일 공유 *acishare를* 탑재합니다. 표시된 경우 파일 공유를 호스팅하는 저장소 계정의 이름 및 저장소 키를 입력합니다. 

CLI 예제와 마찬가지로 `dnsNameLabel` 값은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 필요한 경우 YAML 파일의 값을 업데이트합니다.

```yaml
apiVersion: '2018-10-01'
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
## <a name="deploy-container-and-mount-volume---resource-manager"></a>컨테이너 배포 및 마운트 볼륨 - 리소스 관리자

CLI 및 YAML 배포 외에도 컨테이너 그룹을 배포하고 Azure [리소스 관리자 템플릿을](/azure/templates/microsoft.containerinstance/containergroups)사용하여 컨테이너에 볼륨을 탑재할 수 있습니다.

먼저 템플릿의 `volumes`컨테이너 그룹의 배열`properties` 섹션을 채웁니다. 

그런 다음 볼륨을 탑재하려는 각 컨테이너에 대해 컨테이너 `volumeMounts` 정의 섹션에 배열을 `properties` 채웁니다.

다음 리소스 관리자 템플릿은 이미지로 만든 하나의 `aci-hellofiles` 컨테이너가 있는 컨테이너 그룹을 정의합니다. 컨테이너는 이전에 볼륨으로 만든 Azure 파일 공유 *acishare를* 탑재합니다. 표시된 경우 파일 공유를 호스팅하는 저장소 계정의 이름 및 저장소 키를 입력합니다. 

이전 예제와 마찬가지로 `dnsNameLabel` 값은 컨테이너 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 필요한 경우 템플릿의 값을 업데이트합니다.

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
      "apiVersion": "2018-10-01",
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

Resource Manager 템플릿을 사용하여 배포하려면 위의 JSON을 `deploy-aci.json` 파일에 저장한 다음,  매개 변수를 사용하여 [az group deployment create`--template-file`][az-group-deployment-create] 명령을 실행해야 합니다.

```azurecli
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```


## <a name="mount-multiple-volumes"></a>여러 볼륨 탑재

컨테이너 인스턴스에 여러 볼륨을 탑재하려면 [Azure 리소스 관리자 템플릿,](/azure/templates/microsoft.containerinstance/containergroups)YAML 파일 또는 다른 프로그래밍 방법을 사용하여 배포해야 합니다. 템플릿 또는 YAML 파일을 사용하려면 공유 세부 정보를 제공하고 파일 `volumes` 섹션에 `properties` 배열을 채움으로써 볼륨을 정의합니다. 

예를 들어 저장소 계정 *myStorageAccount에서* *share1* 및 *share2라는* 두 `volumes` 개의 Azure Files 공유를 만든 경우 리소스 관리자 템플릿의 배열은 다음과 유사하게 나타납니다.

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

다음으로 볼륨을 탑재하려는 컨테이너 그룹에 있는 각 컨테이너의 경우 컨테이너 정의의 `properties` 섹션에서 `volumeMounts` 배열을 채웁니다. 예를 들어 다음은 이전에 정의한 *myvolume1* 및 *myvolume2*라는 두 개의 볼륨을 탑재합니다.

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
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create