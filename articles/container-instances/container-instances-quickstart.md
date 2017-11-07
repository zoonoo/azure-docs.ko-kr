---
title: "빠른 시작 - 첫 번째 Azure Container Instances 컨테이너 만들기"
description: "Azure Container Instances 배포 및 시작"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0aaf199d309be289b215a75bb4c222e76e1fb1b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances에서 첫 번째 컨테이너 만들기

Azure Container Instances를 사용하면 가상 컴퓨터를 프로비전하거나 상위 수준 서비스를 도입하지 않고도 Azure에서 Docker 컨테이너를 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure에서 컨테이너를 만들고 공용 IP 주소를 사용하여 인터넷에 공개합니다. 이 작업은 단일 명령으로 완료됩니다. 몇 초 내에 브라우저에 다음과 같은 화면이 표시됩니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-app-browser]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 빠른 시작에서 Azure CLI 버전 2.0.12 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure Container Instances는 Azure 리소스이며 Azure 리소스 그룹, Azure 리소스가 배포 및 관리되는 논리적 컬렉션에 배치되어야 합니다.

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>컨테이너 만들기

[az container create][az-container-create] 명령에 이름, Docker 이미지 및 Azure 리소스 그룹을 제공하여 컨테이너를 만들 수 있습니다. 선택적으로 공용 IP 주소로 컨테이너를 인터넷에 공개할 수 있습니다. 이 경우 [Node.js](http://nodejs.org)에 작성된 매우 간단한 웹앱을 호스트하는 컨테이너를 사용합니다.

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public
```

몇 초 안에 요청에 대한 응답을 얻게 됩니다. 처음에는 컨테이너가 **만드는 중** 상태가 되지만 몇 초 이내 시작됩니다. [az container show][az-container-show] 명령을 사용하여 상태를 확인할 수 있습니다.

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

출력 맨 아래에 컨테이너의 프로비전 상태와 해당 IP 주소가 표시됩니다.

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

컨테이너가 **성공** 상태로 전환되면 제공된 IP 주소를 사용하여 브라우저에서 해당 컨테이너에 연결할 수 있습니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-app-browser]

## <a name="pull-the-container-logs"></a>컨테이너 로그 끌어오기

[az container logs][az-container-logs] 명령을 사용하여 만든 컨테이너의 로그를 끌어올 수 있습니다.

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

출력:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>컨테이너 삭제

컨테이너 작업을 완료했으면 [az container delete][az-container-delete] 명령을 사용하여 제거할 수 있습니다.

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에 사용된 컨테이너의 모든 코드는 Dockerfile과 함께 [GitHub][app-github-repo]에서 사용할 수 있습니다. 직접 빌드하고 Azure Container Registry를 사용하여 Azure Container Instances에 배포하려면 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show


<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png