---
title: "개인 Docker 컨테이너 레지스트리 만들기 - Azure CLI | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 개인 Docker 컨테이너 레지스트리 만들기 및 관리 시작"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Azure CLI를 사용하여 관리되는 컨테이너 레지스트리 만들기

Azure Container Registry는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 관리되는 Docker 컨테이너 레지스트리 서비스입니다. 이 가이드에서는 Azure CLI를 사용하여 관리되는 Azure Container Registry 인스턴스를 만드는 방법에 대해 자세히 설명합니다.

관리되는 Azure 컨테이너 레지스트리는 미리 보기 상태이며 일부 지역에서는 사용할 수 없습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 *westcentralus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

[az acr create](/cli/azure/acr#create) 명령을 사용하여 ACR 인스턴스를 만듭니다.

> [!NOTE]
> 레지스트리를 만들 때 전역적으로 고유한 최상위 도메인 이름(문자 및 숫자만 포함)을 지정합니다.

 이 예제의 레지스트리 이름은 *myContainerRegistry1*이며, 자신만의 고유한 이름으로 바꿉니다.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

레지스트리를 만들면 출력은 다음과 비슷합니다.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>ACR 인스턴스에 로그인

컨테이너 이미지를 밀어넣고 끌어오려면 먼저 ACR 인스턴스에 로그인해야 합니다. 이렇게 하려면 [az acr login](/cli/azure/acr#login) 명령을 사용합니다.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

이 명령은 완료되면 '로그인했습니다.'라는 메시지를 반환합니다.

## <a name="use-azure-container-registry"></a>Azure Container Registry 사용

### <a name="list-container-images"></a>컨테이너 이미지 나열

리포지토리의 이미지 및 태그를 쿼리하려면 `az acr` CLI 명령을 사용합니다.

> [!NOTE]
> 현재 Container Registry는 `docker search` 명령으로 이미지 및 태그를 쿼리하도록 지원하지 않습니다.

### <a name="list-repositories"></a>리포지토리 나열

다음 예제는 레지스트리의 리포지토리를 JSON(JavaScript Object Notation) 형식으로 나열합니다.

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>태그 나열

다음 예제는 **samples/nginx** 리포지토리의 태그를 JSON 형식으로 나열합니다.

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 관리되는 Azure Container Registry 인스턴스를 만들었습니다.

> [!div class="nextstepaction"]
> [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)

