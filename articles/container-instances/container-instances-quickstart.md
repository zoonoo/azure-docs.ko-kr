---
title: "빠른 시작 - 첫 번째 Azure Container Instances 컨테이너 만들기"
description: "Azure Container Instances 배포 및 시작"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: f8fe53f834e4fcf7f16174222cb51d89e40305ec
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances에서 첫 번째 컨테이너 만들기
Azure Container Instances를 사용하면 가상 머신을 프로비전하거나 상위 수준 서비스를 도입하지 않고도 Azure에서 Docker 컨테이너를 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure에서 컨테이너를 만들어서 FQDN(정규화된 도메인 이름)으로 인터넷에 노출합니다. 이 작업은 단일 명령으로 완료됩니다. 몇 초 내에 브라우저에 다음과 같은 화면이 표시됩니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][aci-app-browser]

Azure 구독이 없는 경우 시작하기 전에 [체험 계정][azure-account]을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 빠른 시작을 완료할 수 있습니다. CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.27 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우 [Azure CLI 2.0 설치][azure-cli-install]를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure Container Instances는 Azure 리소스이며 Azure 리소스 그룹, Azure 리소스가 배포 및 관리되는 논리적 컬렉션에 배치되어야 합니다.

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>컨테이너 만들기

[az container create][az-container-create] 명령에 이름, Docker 이미지 및 Azure 리소스 그룹을 제공하여 컨테이너를 만들 수 있습니다. 필요에 따라 DNS 이름 레이블을 지정하여 컨테이너를 인터넷에 노출할 수 있습니다. 이 빠른 시작에서는 [Node.js][node-js]로 작성된 작은 웹앱을 호스팅하는 컨테이너를 배포합니다.

컨테이너 인스턴스를 시작하려면 다음 명령을 실행합니다. `--dns-name-label` 값은 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 따라서 고유성을 유지하기 위해 이 값을 수정해야 할 수도 있습니다.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

몇 초 안에 요청에 대한 응답을 얻게 됩니다. 처음에는 컨테이너가 **만드는 중** 상태가 되지만 몇 초 이내 시작됩니다. [az container show][az-container-show] 명령을 사용하여 상태를 확인할 수 있습니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

명령을 실행하면 컨테이너의 FQDN(정규화된 도메인 이름) 및 프로비전 상태가 표시됩니다.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

컨테이너가 **성공** 상태로 전환되면 해당 FQDN으로 이동하여 브라우저에서 컨테이너에 연결할 수 있습니다.

![Azure 컨테이너 인스턴스에서 실행되는 응용 프로그램을 보여주는 브라우저 스크린샷][aci-app-browser]

## <a name="pull-the-container-logs"></a>컨테이너 로그 끌어오기

[az container logs][az-container-logs] 명령을 사용하여 만든 컨테이너의 로그를 끌어올 수 있습니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

출력

```bash
listening on port 80
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
::ffff:10.240.255.107 - - [29/Nov/2017:20:48:50 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://52.224.178.107/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.94 Safari/537.36"
```

## <a name="delete-the-container"></a>컨테이너 삭제

컨테이너 작업을 완료했으면 [az container delete][az-container-delete] 명령을 사용하여 제거할 수 있습니다.

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

컨테이너가 삭제되었는지 확인하려면 [az container list](/cli/azure/container#az_container_list) 명령을 실행합니다.

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** 컨테이너가 명령의 출력에 나타나지 않아야 합니다. 리소스 그룹에 다른 컨테이너가 없는 경우 표시되는 출력이 없습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에 사용된 컨테이너의 모든 코드는 Dockerfile과 함께 [GitHub][app-github-repo]에서 사용할 수 있습니다. 직접 빌드하고 Azure Container Registry를 사용하여 Azure Container Instances에 배포하려면 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

Azure에서 오케스트레이션 시스템의 컨테이너 실행 옵션을 사용하려면 [Service Fabric][service-fabric] 또는 [AKS(Azure Container Service)][container-service] 빠른 시작을 참조하세요.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group?view=azure-cli-latest#az_group_create
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-delete]: /cli/azure/container?view=azure-cli-latest#az_container_delete
[az-container-list]: /cli/azure/container?view=azure-cli-latest#az_container_list
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
