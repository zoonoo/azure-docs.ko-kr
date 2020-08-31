---
title: 클라우드 네이티브 Buildpack을 사용 하 여 이미지 빌드
description: Az acr pack build 명령을 사용 하 여 앱에서 컨테이너 이미지를 빌드하고 Dockerfile을 사용 하지 않고 Azure Container Registry에 푸시합니다.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: f99175165e01a9287a66c59957fc7a239b00393a
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88077223"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>클라우드 네이티브 Buildpack을 사용 하 여 앱에서 이미지 빌드 및 푸시

Azure CLI 명령은 `az acr pack build` [`pack`](https://github.com/buildpack/pack) [buildpacks](https://buildpacks.io/)에서 CLI 도구를 사용 하 여 앱을 빌드하고 해당 이미지를 Azure container registry로 푸시합니다. 이 기능은 Dockerfile을 정의 하지 않고도 Node.js, Java 및 기타 언어의 응용 프로그램 소스 코드에서 컨테이너 이미지를 신속 하 게 빌드할 수 있는 옵션을 제공 합니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 문서의 예제를 실행할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.70 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="use-the-build-command"></a>빌드 명령 사용

클라우드 네이티브 Buildpacks을 사용 하 여 컨테이너 이미지를 빌드하고 푸시 하려면 [az acr pack build][az-acr-pack-build] 명령을 실행 합니다. [Az acr build][az-acr-build] 명령은 `az acr pack build` 응용 프로그램 소스 트리를 직접 지정 하 여 dockerfile 원본 및 관련 코드에서 이미지를 빌드하고 푸시합니다.

최소한 `az acr pack build`를 실행하는 경우 다음을 지정합니다.

* 명령을 실행 하는 Azure container registry
* 결과 이미지에 대 한 이미지 이름 및 태그
* 로컬 디렉터리, GitHub 리포지토리 또는 원격 tarball 같이 ACR 작업에 대해 [지원 되는 컨텍스트 위치](container-registry-tasks-overview.md#context-locations) 중 하나입니다.
* 응용 프로그램에 적합 한 Buildpack builder 이미지의 이름입니다. `cloudfoundry/cnb:0.0.34-cflinuxfs3`더 빠른 빌드를 위해와 같은 Azure Container Registry 캐시 빌더 이미지  

`az acr pack build`는 [실행 변수](container-registry-tasks-reference-yaml.md#run-variables) 및 스트리밍되고 나중에 검색할 수 있도록 저장되는 [작업 실행 로그](container-registry-tasks-logs.md)를 포함한 ACR 작업 명령의 다른 기능을 지원합니다.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>예제: Cloud Foundry builder를 사용 하 여 Node.js 이미지 빌드

다음 예제에서는 작성기를 사용 하 여 [Azure Samples/nodejs-docs-hello-세계](https://github.com/Azure-Samples/nodejs-docs-hello-world) 리포지토리의 Node.js 앱에서 컨테이너 이미지를 빌드합니다 `cloudfoundry/cnb:0.0.34-cflinuxfs3` . 이 작성기는 Azure Container Registry에 의해 캐시 되므로 `--pull` 매개 변수는 필요 하지 않습니다.

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

이 예제에서는 `node-app` 태그로 이미지를 빌드하고 `1.0` *myregistry* 컨테이너 레지스트리에 푸시합니다. 이 예제에서는 대상 레지스트리 이름이 이미지 이름에 명시적으로 앞에 붙습니다. 지정 하지 않으면 레지스트리 로그인 서버 이름이 이미지 이름에 자동으로 붙습니다.

명령 출력에 이미지를 빌드하고 푸시하는 진행률이 표시 됩니다. 

이미지가 성공적으로 빌드되면 Docker를 사용 하 여 실행할 수 있습니다 (설치 된 경우). 먼저 레지스트리에 로그인 합니다.

```azurecli
az acr login --name myregistry
```

이미지를 실행 합니다.

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

`localhost:1337`즐겨 찾는 브라우저에서로 이동 하 여 샘플 웹 앱을 확인 합니다. 컨테이너를 중지 하려면 키를 누릅니다 `[Ctrl]+[C]` .

## <a name="example-build-java-image-with-heroku-builder"></a>예: Heroku builder를 사용 하 여 Java 이미지 빌드

다음 예제에서는 빌더를 사용 하 여 [buildpack/샘플-java 앱](https://github.com/buildpack/sample-java-app) 리포지토리의 java 앱에서 컨테이너 이미지를 빌드합니다 `heroku/buildpacks:18` . `--pull`매개 변수는 명령이 최신 빌더 이미지를 가져오도록 지정 합니다. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

이 예제에서는 `java-app` 명령의 실행 ID로 태그가 지정 된 이미지를 빌드하고 *myregistry* 컨테이너 레지스트리에 푸시합니다.

명령 출력에 이미지를 빌드하고 푸시하는 진행률이 표시 됩니다. 

이미지가 성공적으로 빌드되면 Docker를 사용 하 여 실행할 수 있습니다 (설치 된 경우). 먼저 레지스트리에 로그인 합니다.

```azurecli
az acr login --name myregistry
```

이미지를 실행 하 고 *runid*에 대 한 이미지 태그를 대체 합니다.

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

`localhost:8080`즐겨 찾는 브라우저에서로 이동 하 여 샘플 웹 앱을 확인 합니다. 컨테이너를 중지 하려면 키를 누릅니다 `[Ctrl]+[C]` .


## <a name="next-steps"></a>다음 단계

를 사용 하 여 컨테이너 이미지를 빌드하고 푸시한 후 원하는 `az acr pack build` 대상에 이미지를 배포할 수 있습니다. Azure 배포 옵션은 [App Service](../app-service/tutorial-custom-container.md) 또는 [azure Kubernetes 서비스](../aks/tutorial-kubernetes-deploy-cluster.md)에서 실행 하는 것을 포함 합니다.

ACR 작업 기능에 대 한 자세한 내용은 [Acr 작업을 사용 하 여 컨테이너 이미지 빌드 및 유지 관리 자동화](container-registry-tasks-overview.md)를 참조 하세요.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
