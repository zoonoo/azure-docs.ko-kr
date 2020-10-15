---
title: 자습서 - 이미지를 배포할 컨테이너 레지스트리 준비
description: Azure Container Instances 자습서 2/3부 - Azure 컨테이너 레지스트리를 준비하여 이미지 푸시
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 44a7f21c067897b046413851ef5a2c73bfccc24f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708039"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>자습서: Azure 컨테이너 레지스트리를 만들고 컨테이너 이미지 푸시

세 부분으로 이루어진 자습서의 두 번째 부분입니다. 자습서의 [1부](container-instances-tutorial-prepare-app.md)에서는 Node.js 웹 애플리케이션에 사용할 Docker 컨테이너 이미지를 만들었습니다. 이 자습서에서는 이 이미지를 Azure Container Registry에 푸시합니다. 컨테이너 이미지를 만들지 않은 경우 [자습서 1 - 컨테이너 이미지 만들기](container-instances-tutorial-prepare-app.md)로 돌아갑니다.

Azure Container Registry는 Azure의 프라이빗 Docker 레지스트리입니다. 이 자습서의 시리즈 2부에서는 다음을 수행합니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry 인스턴스 만들기
> * Azure Container Registry에 컨테이너 이미지 태그 지정
> * 레지스트리에 이미지 업로드

시리즈의 마지막 과정인 그 다음 문서에서는 Azure Container Instances에 프라이빗 레지스트리의 컨테이너를 배포합니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>컨테이너 이미지 태그 지정

컨테이너 이미지를 Azure Container Registry 같은 프라이빗 레지스트리에 푸시하려면 먼저 레지스트리 로그인 서버의 전체 이름을 사용하여 이미지에 태그를 지정해야 합니다.

먼저 Azure 컨테이너 레지스트리의 전체 로그인 서버 이름을 가져옵니다. 다음 [az acr show][az-acr-show] 명령을 실행하고, `<acrName>`을 방금 만든 레지스트리 이름으로 바꿉니다.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

예를 들어 레지스트리 이름이 *mycontainerregistry082*인 경우:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

이제 [docker images][docker-images] 명령을 사용하여 로컬 이미지 목록을 표시합니다.

```bash
docker images
```

컴퓨터에 있는 다른 이미지와 함께, [이전 자습서](container-instances-tutorial-prepare-app.md)에서 작성한 *aci-tutorial-app* 이미지가 보일 것입니다.

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

*aci-tutorial-app* 이미지에 컨테이너 레지스트리의 로그인 서버로 태그를 지정합니다. 또한 이미지 이름 끝에 이미지 버전 번호를 나타내는 `:v1` 태그를 추가합니다. `<acrLoginServer>`를 앞에서 실행한 [az acr show][az-acr-show] 명령의 결과로 바꿉니다.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

`docker images` 명령을 다시 실행하여 태그 지정 작업을 확인합니다.

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시하기

이제 프라이빗 레지스트리의 전체 로그인 서버 이름으로 *aci-tutorial-app* 이미지에 태그를 지정했으므로 [docker push][docker-push] 명령을 사용하여 이미지를 레지스트리에 푸시할 수 있습니다. `<acrLoginServer>`를 이전 단계에서 얻은 전체 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` 작업은 인터넷 연결에 따라 몇 초에서 몇 분 정도 걸리고 출력은 다음과 유사합니다.

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Azure Container Registry에서 이미지 나열

방금 푸시한 이미지가 실제로 Azure 컨테이너 레지스트리에 있는지 확인하기 위해 [az acr repository list][az-acr-repository-list] 명령을 사용하여 레지스트리의 이미지를 나열합니다. `<acrName>`을 컨테이너 레지스트리의 이름으로 바꿉니다.

```azurecli
az acr repository list --name <acrName> --output table
```

예를 들면 다음과 같습니다.

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

특정 이미지의 *태그*를 보려면 [az acr repository show-tags][az-acr-repository-show-tags] 명령을 사용합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

다음과 비슷한 결과가 나타나야 합니다.

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances와 함께 사용할 Azure 컨테이너 레지스트리를 준비하고, 컨테이너 이미지를 레지스트리에 푸시했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure Container Registry 인스턴스 만들기
> * Azure Container Registry에 컨테이너 이미지 태그 지정
> * Azure Container Registry에 이미지 업로드

다음 자습서로 넘어가서 Azure Container Instances를 사용하여 컨테이너를 Azure에 배포하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Instances에 컨테이너 배포](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
