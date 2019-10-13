---
title: 앱에서 Azure Container Registry 이미지 빌드
description: Az acr pack build 명령을 사용 하 여 앱에서 컨테이너 이미지를 빌드하고 Dockerfile을 사용 하지 않고 Azure Container Registry에 푸시합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 10/10/2019
ms.author: danlep
ms.openlocfilehash: b544820a0c496e0814de44790ea9c28878031a7d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293909"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>클라우드 네이티브 Buildpack을 사용 하 여 앱에서 이미지 빌드 및 푸시

Azure CLI 명령 @no__t는 [Buildpacks](https://buildpacks.io/)에서 [`pack`](https://github.com/buildpack/pack) CLI 도구를 사용 하 여 앱을 빌드하고 해당 이미지를 Azure container registry로 푸시합니다. 이 기능은 Dockerfile을 정의 하지 않고도 node.js, Java 및 기타 언어의 응용 프로그램 소스 코드에서 컨테이너 이미지를 신속 하 게 빌드할 수 있는 옵션을 제공 합니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 문서의 예제를 실행할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.70 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="use-the-build-command"></a>빌드 명령 사용

클라우드 네이티브 Buildpacks을 사용 하 여 컨테이너 이미지를 빌드하고 푸시 하려면 [az acr pack build][az-acr-pack-build] 명령을 실행 합니다. [Az acr build][az-acr-build] 명령은 @no__t를 사용 하 여 Dockerfile 소스 및 관련 코드에서 이미지를 빌드하고 푸시합니다. 1은 응용 프로그램 소스 트리를 직접 지정 합니다.

최소한 @no__t를 실행 하는 경우 다음을 지정 합니다.

* 명령을 실행 하는 Azure container registry
* 결과 이미지에 대 한 이미지 이름 및 태그
* 로컬 디렉터리, GitHub 리포지토리 또는 원격 tarball 같이 ACR 작업에 대해 [지원 되는 컨텍스트 위치](container-registry-tasks-overview.md#context-locations) 중 하나입니다.
* @No__t-0과 같은 Buildpack builder 이미지의 이름입니다.  

`az acr pack build`는 스트리밍 및 나중에 검색할 수 있도록 저장 된 [변수 실행](container-registry-tasks-reference-yaml.md#run-variables) 및 [작업 실행 로그](container-registry-tasks-overview.md#view-task-logs) 를 비롯 하 여 ACR 작업 명령의 다른 기능을 지원 합니다.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>예제: Cloud Foundry builder를 사용 하 여 node.js 이미지 빌드

다음 예제에서는 `cloudfoundry/cnb:0.0.12-bionic` 작성기를 사용 하 여 [Azure Samples/nodejs-docs-hello-세계](https://github.com/Azure-Samples/nodejs-docs-hello-world) 리포지토리의 node.js 앱에서 컨테이너 이미지를 빌드합니다.

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:0.0.12-bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

이 예제에서는 `1.0` 태그를 사용 하 여 `node-app` 이미지를 빌드하고 *myregistry* 컨테이너 레지스트리에 푸시합니다. 여기서는 대상 레지스트리 이름이 이미지 이름에 명시적으로 앞에 붙습니다. 지정 하지 않으면 레지스트리 URL이 이미지 이름에 자동으로 붙습니다.

@No__t-0 매개 변수는 명령이 최신 빌더 이미지를 끌어오는 것을 지정 합니다.

명령 출력에 이미지를 빌드하고 푸시하는 진행률이 표시 됩니다. 

이미지가 성공적으로 빌드되면 Docker를 사용 하 여 실행할 수 있습니다 (설치 된 경우). 먼저 레지스트리에 로그인 합니다.

```azurecli
az acr login --name myregistry
```

이미지를 실행 합니다.

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

선호 하는 브라우저에서 `localhost:1337`으로 이동 하 여 샘플 웹 앱을 확인 합니다. 컨테이너를 중지 하려면 `[Ctrl]+[C]`을 누릅니다.

## <a name="example-build-java-image-with-heroku-builder"></a>예제: Heroku builder를 사용 하 여 Java 이미지 빌드

다음 예제에서는 `heroku/buildpacks:18` 작성기를 사용 하 여 [buildpack/샘플 java 앱](https://github.com/buildpack/sample-java-app) 리포지토리의 java 앱에서 컨테이너 이미지를 빌드합니다.

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

이 예제에서는 명령의 실행 ID로 태그가 지정 된 `java-app` 이미지를 빌드하고 *myregistry* 컨테이너 레지스트리에 푸시합니다.

@No__t-0 매개 변수는 명령이 최신 빌더 이미지를 끌어오는 것을 지정 합니다.

명령 출력에 이미지를 빌드하고 푸시하는 진행률이 표시 됩니다. 

이미지가 성공적으로 빌드되면 Docker를 사용 하 여 실행할 수 있습니다 (설치 된 경우). 먼저 레지스트리에 로그인 합니다.

```azurecli
az acr login --name myregistry
```

이미지를 실행 하 고 *runid*에 대 한 이미지 태그를 대체 합니다.

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

선호 하는 브라우저에서 `localhost:8080`으로 이동 하 여 샘플 웹 앱을 확인 합니다. 컨테이너를 중지 하려면 `[Ctrl]+[C]`을 누릅니다.


## <a name="next-steps"></a>다음 단계

@No__t-0으로 컨테이너 이미지를 빌드하고 푸시한 후 원하는 대상에 이미지를 배포할 수 있습니다. Azure 배포 옵션은 [App Service](../app-service/containers/tutorial-custom-docker-image.md) 또는 [azure Kubernetes 서비스](../aks/tutorial-kubernetes-deploy-cluster.md)에서 실행 하는 것을 포함 합니다.

ACR 작업 기능에 대 한 자세한 내용은 [Acr 작업을 사용 하 여 컨테이너 이미지 빌드 및 유지 관리 자동화](container-registry-tasks-overview.md)를 참조 하세요.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
