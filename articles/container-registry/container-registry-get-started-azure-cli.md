---
title: "빠른 시작 - Azure CLI를 사용하여 Azure에서 개인 Docker 레지스트리 만들기"
description: "Azure CLI를 사용한 개인 Docker 컨테이너 레지스트리 만들기에 대해 빠르게 알아봅니다."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91f0aa093e0a1f7ed4d54a0cdf5ef53bc41cb6be
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Azure CLI를 사용하여 컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 관리되는 Docker 컨테이너 레지스트리 서비스입니다. 이 가이드에서는 Azure CLI를 사용하여 Azure Container Registry 인스턴스 만들기에 대해 자세히 설명합니다.

이 빠른 시작에서는 Azure CLI 버전 2.0.12 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

이 빠른 시작에서는 *Basic* 레지스트리를 만듭니다. Azure Container Registry는 다음 표에 간략하게 설명된 몇 개의 다른 SKU에서 사용할 수 있습니다. 각각에 대해 확장된 세부 정보를 보려면 [컨테이너 레지스트리 SKU](container-registry-skus.md)를 참조하세요.

Azure Container Registry는 `Basic`, `Managed_Basic`, `Managed_Standard` 및 `Managed_Premium`의 몇몇 SKU에서 사용할 수 있습니다. `Managed_*` SKU는 관리되는 저장소 및 웹후크와 같은 고급 기능을 제공하긴 하지만, 현재 Azure CLI를 사용할 경우 일부 Azure 지역에서 사용할 수 없습니다. 이 빠른 시작에서는 모든 지역에서 사용할 수 있는 `Basic` SKU를 선택합니다.

>[!NOTE]
> 관리되는 레지스트리는 현재 모든 지역에서 사용할 수 있습니다. 그러나 최신 버전의 Azure CLI는 모든 지역에 관리되는 레지스트리를 만들 수 있도록 아직 지원하지 않습니다. 이러한 지원은 Azure CLI의 다음 버전에서 사용할 수 있습니다. 해당 릴리스 전에는 [Azure Portal](container-registry-get-started-portal.md)을 사용하여 관리되는 레지스트리를 만드세요.

[az acr create](/cli/azure/acr#create) 명령을 사용하여 ACR 인스턴스를 만듭니다.

레지스트리의 이름은 **고유해야 합니다**. 다음 예제에서는 *myContainerRegistry007*을 사용합니다. 이를 고유한 값으로 업데이트합니다.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
```

레지스트리를 만들면 출력은 다음과 비슷합니다.

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

이 빠른 시작의 나머지 부분에서는 컨테이너 레지스트리 이름에 자리 표시자로 `<acrname>`을 사용합니다.

## <a name="log-in-to-acr"></a>ACR에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 이렇게 하려면 [az acr login](/cli/azure/acr#login) 명령을 사용합니다.

```azurecli
az acr login --name <acrname>
```

이 명령은 완료되면 '로그인했습니다.'라는 메시지를 반환합니다.

## <a name="push-image-to-acr"></a>ACR에 이미지 푸시

Azure Container Registry에 이미지를 푸시하려면 먼저 이미지가 있어야 합니다. 필요한 경우에 다음 명령을 실행하여 미리 만든 이미지를 Docker Hub에서 끌어옵니다.

```bash
docker pull microsoft/aci-helloworld
```

이미지는 ACR 로그인 서버 이름으로 태그가 지정되어야 합니다. 다음 명령을 실행하여 ACR 인스턴스의 로그인 서버 이름을 반환합니다.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

[docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 명령을 사용하여 이미지에 태그를 지정합니다. *<acrLoginServer>*를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

마지막으로 [docker push](https://docs.docker.com/engine/reference/commandline/push/)를 사용하여 ACR 인스턴스로 이미지를 푸시합니다. *<acrLoginServer>*를 ACR 인스턴스의 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>컨테이너 이미지 나열

다음 예제는 레지스트리의 리포지토리를 나열합니다.

```azurecli
az acr repository list -n <acrname> -o table
```

출력:

```bash
Result
----------------
aci-helloworld
```

다음 예제는 **aci-helloworld** 리포지토리에서의 태그를 나열합니다.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

출력:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, ACR 인스턴스 및 모든 컨테이너 이미지를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들었습니다. Azure Container Instances와 함께 Azure Container Registry를 사용하려는 경우 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](../container-instances/container-instances-tutorial-prepare-app.md)