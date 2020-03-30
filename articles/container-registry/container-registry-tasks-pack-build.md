---
title: 클라우드 네이티브 빌드팩으로 이미지 구축
description: az acr pack 빌드 명령을 사용하여 앱에서 컨테이너 이미지를 빌드하고 Dockerfile을 사용하지 않고 Azure 컨테이너 레지스트리로 푸시합니다.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087073"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>클라우드 네이티브 빌드팩을 사용하여 앱에서 이미지 빌드 및 푸시

Azure CLI `az acr pack build` 명령은 [`pack`](https://github.com/buildpack/pack) [Buildpacks에서](https://buildpacks.io/)CLI 도구를 사용하여 앱을 빌드하고 이미지를 Azure 컨테이너 레지스트리로 푸시합니다. 이 기능은 Dockerfile을 정의할 필요 없이 Node.js, Java 및 기타 언어의 응용 프로그램 소스 코드에서 컨테이너 이미지를 빠르게 빌드하는 옵션을 제공합니다.

Azure 클라우드 셸 또는 Azure CLI의 로컬 설치를 사용하여 이 문서의 예제를 실행할 수 있습니다. 로컬에서 사용하려면 버전 2.0.70 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하십시오.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="use-the-build-command"></a>빌드 명령 사용

클라우드 네이티브 빌드팩을 사용하여 컨테이너 이미지를 빌드하고 푸시하려면 [az acr pack 빌드][az-acr-pack-build] 명령을 실행합니다. az [acr 빌드][az-acr-build] 명령은 Dockerfile 소스 및 관련 코드에서 이미지를 `az acr pack build` 빌드하고 푸시하는 반면 응용 프로그램 소스 트리를 직접 지정합니다.

최소한 실행할 `az acr pack build`때 다음을 지정합니다.

* 명령을 실행하는 Azure 컨테이너 레지스트리
* 결과 이미지의 이미지 이름 및 태그
* 로컬 디렉터리, GitHub 리포지토리 또는 원격 타르볼과 같은 ACR 작업에 [지원되는 컨텍스트 위치](container-registry-tasks-overview.md#context-locations) 중 하나
* 응용 프로그램에 적합한 Buildpack 빌더 이미지의 이름입니다. Azure 컨테이너 레지스트리는 더 빠른 `cloudfoundry/cnb:0.0.34-cflinuxfs3` 빌드를 위해 빌더 이미지를 캐시합니다.  

`az acr pack build`[실행 변수](container-registry-tasks-reference-yaml.md#run-variables) 및 [작업 실행 로그를](container-registry-tasks-logs.md) 포함하여 ACR 작업 명령의 다른 기능을 지원하며 나중에 검색할 수 있도록 저장됩니다.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>예: 클라우드 파운드리 빌더를 통해 Node.js 이미지 빌드

다음 예제에서는 `cloudfoundry/cnb:0.0.34-cflinuxfs3` 빌더를 사용하여 [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) 리포지토리에서 Node.js 앱에서 컨테이너 이미지를 빌드합니다. 이 빌더는 Azure 컨테이너 레지스트리에 의해 `--pull` 캐시되므로 매개 변수가 필요하지 않습니다.

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

이 예제는 `node-app` `1.0` 태그를 사용하여 이미지를 빌드하고 *myregistry* 컨테이너 레지스트리로 푸시합니다. 이 예제에서는 대상 레지스트리 이름이 이미지 이름으로 명시적으로 준비됩니다. 지정하지 않으면 레지스트리 로그인 서버 이름이 자동으로 이미지 이름으로 준비됩니다.

명령 출력은 이미지를 빌드하고 푸시하는 진행 률을 보여 주며 

이미지가 성공적으로 빌드된 후 설치한 경우 Docker를 통해 이미지를 실행할 수 있습니다. 레지스트리에 첫 번째 로그인:

```azurecli
az acr login --name myregistry
```

이미지 실행:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

샘플 `localhost:1337` 웹 응용 프로그램을 보려면 즐겨 찾는 브라우저에서 검색합니다. 용기를 `[Ctrl]+[C]` 중지하려면 누릅니다.

## <a name="example-build-java-image-with-heroku-builder"></a>예: Heroku 빌더로 자바 이미지 만들기

다음 예제에서는 빌더를 사용하여 [빌드팩/샘플 자바 앱](https://github.com/buildpack/sample-java-app) 리포지토리에서 Java `heroku/buildpacks:18` 앱에서 컨테이너 이미지를 빌드합니다. 매개 `--pull` 변수는 명령이 최신 빌더 이미지를 가져와야 한다고 지정합니다. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

이 예제는 `java-app` 명령의 실행 ID로 태그가 지정된 이미지를 빌드하고 *myregistry* 컨테이너 레지스트리로 푸시합니다.

명령 출력은 이미지를 빌드하고 푸시하는 진행 률을 보여 주며 

이미지가 성공적으로 빌드된 후 설치한 경우 Docker를 통해 이미지를 실행할 수 있습니다. 레지스트리에 첫 번째 로그인:

```azurecli
az acr login --name myregistry
```

이미지를 실행하여 이미지 태그를 *runid로*대체합니다.

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

샘플 `localhost:8080` 웹 응용 프로그램을 보려면 즐겨 찾는 브라우저에서 검색합니다. 용기를 `[Ctrl]+[C]` 중지하려면 누릅니다.


## <a name="next-steps"></a>다음 단계

을 사용하여 `az acr pack build`컨테이너 이미지를 빌드하고 푸시한 후 원하는 대상에 모든 이미지처럼 배포할 수 있습니다. Azure 배포 옵션에는 [앱 서비스](../app-service/containers/tutorial-custom-docker-image.md) 또는 [Azure Kubernetes 서비스](../aks/tutorial-kubernetes-deploy-cluster.md)등에서 실행하는 것이 포함됩니다.

ACR 작업 기능에 대한 자세한 내용은 [ACR 작업을 통해 컨테이너 이미지 빌드 및 유지 관리 자동화를](container-registry-tasks-overview.md)참조하십시오.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
