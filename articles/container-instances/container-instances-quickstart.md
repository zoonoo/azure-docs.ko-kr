---
title: 빠른 시작 - Azure Container Instances에서 애플리케이션 실행 - CLI
description: 이 빠른 시작에서는 Azure CLI를 사용하여 격리된 컨테이너에서 실행할 Docker 컨테이너 애플리케이션을 Azure Container Instances에 배포함
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 70d1bc9003d98f0154b9f38738f1b8e82b0c506d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189611"
---
# <a name="quickstart-run-a-container-application-in-azure-container-instances-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure Container Instances에서 컨테이너 애플리케이션 실행

Azure Container Instances를 사용하여 Azure에서 Docker 컨테이너를 간단하고 빠르게 실행합니다. 가상 머신을 배포하거나 Kubernetes와 같은 전체 컨테이너 오케스트레이션 플랫폼을 사용할 필요가 없습니다. 이 빠른 시작에서는 Azure CLI를 사용하여 Azure에서 컨테이너를 만들고 FQDN(정규화된 도메인 이름)을 통해 해당 애플리케이션을 사용할 수 있도록 합니다. 단일 배포 명령을 실행한 후 몇 초 내에 실행 중인 응용 프로그램을 찾아볼 수 있습니다.

![Azure Container Instances에 배포되어 브라우저에 표시된 응용 프로그램][aci-app-browser]

Azure 구독이 없는 경우 시작하기 전에 [체험 계정][azure-account]을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 빠른 시작을 완료할 수 있습니다. 로컬로 사용하려면 2.0.27 버전 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

모든 Azure 리소스와 마찬가지로 Azure 컨테이너 인스턴스는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

먼저, 다음 [az group create][az-group-create] 명령을 사용하여 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>컨테이너 만들기

이제 리소스 그룹이 있으므로 Azure에서 컨테이너를 실행할 수 있습니다. Azure CLI를 사용하여 컨테이너 인스턴스를 만들려면 [az container create][az-container-create] 명령에 리소스 그룹 이름, 컨테이너 인스턴스 이름 및 Docker 컨테이너 이미지를 제공합니다. 열려는 하나 이상의 포트, DNS 이름 레이블 또는 둘 다를 지정하여 컨테이너를 인터넷에 공개할 수 있습니다. 이 빠른 시작에서는 Node.js로 작성된 작은 웹앱을 호스팅하는 DNS 이름 레이블이 있는 컨테이너를 배포합니다.

컨테이너 인스턴스를 시작하려면 다음 명령을 실행합니다. `--dns-name-label` 값은 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. "DNS 이름 레이블을 사용할 수 없습니다"라는 오류 메시지가 표시되면 다른 DNS 이름 레이블을 사용해 보세요.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/aci-helloworld --dns-name-label aci-demo --ports 80
```

몇 초 내에 Azure CLI로부터 배포가 완료되었음을 알려주는 응답을 받습니다. [az container show][az-container-show] 명령을 사용하여 상태를 확인합니다.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

명령을 실행하면 컨테이너의 FQDN(정규화된 도메인 이름) 및 해당 프로비전 상태가 표시됩니다.

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

컨테이너의 `ProvisioningState`가 **Succeeded**(성공)이면 브라우저에서 해당 FQDN으로 이동합니다. 다음과 비슷한 웹 페이지가 표시됩니다. Docker 컨테이너에서 실행되는 응용 프로그램이 Azure에 성공적으로 배포되었습니다.

![Azure 컨테이너 인스턴스에서 실행되는 응용 프로그램을 보여주는 브라우저 스크린샷][aci-app-browser]

처음에 응용 프로그램이 표시되지 않으면 DNS가 전파되는 동안 잠시 기다린 후에 브라우저를 새로 고쳐야 할 수 있습니다.

## <a name="pull-the-container-logs"></a>컨테이너 로그 끌어오기

컨테이너 또는 컨테이너가 실행되는 응용 프로그램의 문제를 해결해야 하거나 해당 문제에 대한 출력만 보려는 경우 컨테이너 인스턴스의 로그를 확인하면서 시작합니다.

[az container logs][az-container-logs] 명령을 사용하여 컨테이너 인스턴스 로그를 끌어옵니다.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

출력은 컨테이너의 로그를 표시하며, 브라우저에서 응용 프로그램을 볼 때 생성된 HTTP GET 요청을 표시해야 합니다.

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.105 - - [01/Oct/2018:18:25:51 +0000] "GET / HTTP/1.0" 200 1663 "-" "-"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
::ffff:10.240.255.106 - - [01/Oct/2018:18:31:04 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```

## <a name="attach-output-streams"></a>출력 스트림 연결

로그를 확인하는 것 외에도 로컬 표준 출력과 표준 오류 스트림을 컨테이너의 해당 표준 출력 스트림에 연결할 수 있습니다.

먼저 [az container attach][az-container-attach] 명령을 실행하여 로컬 콘솔을 컨테이너의 출력 스트림에 연결합니다.

```azurecli-interactive
az container attach --resource-group myResourceGroup -n mycontainer
```

연결되면 브라우저를 몇 번 새로 고쳐 추가 출력을 생성합니다. 완료되면 `Control+C`를 사용하여 콘솔을 분리합니다. 다음과 비슷한 결과가 나타나야 합니다.

```console
$ az container attach --resource-group myResourceGroup -n mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-15 21:17:59+00:00) pulling image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Successfully pulled image "microsoft/aci-helloworld"
(count: 1) (last timestamp: 2018-03-15 21:18:05+00:00) Created container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45
(count: 1) (last timestamp: 2018-03-15 21:18:06+00:00) Started container with id 3534a1e2ee392d6f47b2c158ce8c1808d1686fc54f17de3a953d356cf5f26a45

Start streaming logs:
listening on port 80
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.105 - - [15/Mar/2018:21:18:26 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:44 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
::ffff:10.240.255.107 - - [15/Mar/2018:21:18:47 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.146 Safari/537.36"
```

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 작업을 완료했으면 [az container delete][az-container-delete] 명령을 사용하여 제거합니다.

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

컨테이너가 삭제되었는지 확인하려면 [az container list](/cli/azure/container#az-container-list) 명령을 실행합니다.

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** 컨테이너가 명령의 출력에 나타나지 않아야 합니다. 리소스 그룹에 다른 컨테이너가 없는 경우 표시되는 출력이 없습니다.

*myResourceGroup* 리소스 그룹 및 이 그룹에 포함된 모든 리소스 작업이 완료되었으면 [az group delete][az-group-delete] 명령을 사용하여 해당 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 공용 Docker Hub 리포지토리의 이미지를 사용하여 Azure 컨테이너 인스턴스를 만들었습니다. 컨테이너 이미지를 빌드하고 개인 Azure 컨테이너 레지스트리에서 배포하려면 Azure Container Instances 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

Azure에서 오케스트레이션 시스템의 컨테이너 실행 옵션을 사용하려면 [Service Fabric][service-fabric] 또는 [AKS(Azure Kubernetes Service)][container-service] 빠른 시작을 참조하세요.

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: http://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
[service-fabric]: ../service-fabric/service-fabric-quickstart-containers.md
