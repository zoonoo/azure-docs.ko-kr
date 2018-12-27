---
title: 자습서 - 기본 이미지 업데이트 시 컨테이너 이미지 빌드 자동화 - Azure Container Registry 작업
description: 이 자습서에서는 기본 이미지가 업데이트될 때 클라우드에서 컨테이너 이미지 빌드를 자동으로 트리거하도록 Azure Container Registry 작업을 구성하는 방법을 알아봅니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: b3d8c3aea4955d6f95ead69d5bed147cc486e7c8
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254040"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>자습서: Azure Container Registry에서 기본 이미지가 업데이트될 때 컨테이너 이미지 빌드 자동화 

ACR 작업은 기본 이미지 중 하나에서 OS 또는 응용 프로그램 프레임워크를 패치할 때와 같이 컨테이너의 기본 이미지가 업데이트될 때 자동화된 빌드 실행을 지원합니다. 이 자습서에서는 컨테이너의 기본 이미지가 레지스트리에 푸시되었을 때 클라우드에서 빌드를 트리거하는 작업을 ACR 작업에 만드는 방법에 대해 알아봅니다.

이 자습서는 시리즈의 마지막 3부입니다.

> [!div class="checklist"]
> * 기본 이미지 빌드
> * 애플리케이션 이미지 빌드 작업 만들기
> * 응용 프로그램 이미지 작업을 트리거하도록 기본 이미지 업데이트
> * 트리거된 작업 표시
> * 업데이트된 애플리케이션 이미지 확인

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 사용하려면 Azure CLI 버전 **2.0.46** 이상이 설치되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="complete-the-previous-tutorials"></a>이전 자습서 완료

이 자습서에서는 시리즈의 처음 두 자습서의 단계를 이미 완료했다고 가정합니다.

* Azure 컨테이너 레지스트리 만들기
* 샘플 리포지토리 포크
* 샘플 리포지토리 복제
* GitHub 개인용 액세스 토큰 만들기

계속 진행하기 전에 처음 두 자습서를 완료하세요.

[자습서 - Azure Container Registry 작업을 사용하여 클라우드에 컨테이너 이미지 빌드](container-registry-tutorial-quick-task.md)

[Azure Container Registry 작업을 사용하여 컨테이너 이미지 빌드 자동화](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>환경 구성

이러한 셸 환경 변수를 사용자 환경에 적합한 값으로 채웁니다. 이 단계는 반드시 필요한 것이 아니지만, 여러 줄로 된 Azure CLI 명령을 이 자습서에서 좀 더 쉽게 실행할 수 있게 합니다. 이러한 환경 변수를 채우지 않는 경우 명령 예제에서 표시되는 모든 위치에서 각 값을 수동으로 바꿔야 합니다.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>기본 이미지

대부분의 컨테이너 이미지를 정의하는 Dockerfile은 이미지의 기반이 되는 부모 이미지를 지정하며, 종종 이를 *기본 이미지*라고 합니다. 기본 이미지에는 대개 컨테이너의 나머지 계층이 적용되는 운영 체제(예: [Alpine Linux][base-alpine] 또는 [Windows Nano Server][base-windows])가 포함됩니다. [Node.js][base-node] 또는 [.NET Core][base-dotnet]와 같은 응용 프로그램 프레임워크가 포함될 수도 있습니다.

### <a name="base-image-updates"></a>기본 이미지 업데이트

이미지의 OS 또는 프레임워크의 새로운 기능이나 향상된 기능을 포함하기 위해 이미지 유지 관리자에서 기본 이미지를 업데이트하는 경우가 많습니다. 보안 패치는 기본 이미지 업데이트에 대한 또 다른 일반적인 원인입니다.

기본 이미지가 업데이트되면 새로운 기능 및 수정 프로그램이 포함되도록 레지스트리에 있는 모든 컨테이너 이미지를 다시 빌드해야 합니다. ACR 작업에는 컨테이너의 기본 이미지가 업데이트될 때 이미지를 자동으로 빌드하는 기능이 포함되어 있습니다.

### <a name="base-image-update-scenario"></a>기본 이미지 업데이트 시나리오

이 자습서에서는 기본 이미지 업데이트 시나리오를 안내합니다. [코드 샘플][code-sample]에는 두 개의 Docker 파일, 즉 응용 프로그램 이미지와 해당 응용 프로그램에서 기본으로 지정하는 이미지가 포함되어 있습니다. 다음 섹션에서는 새 버전의 기본 이미지가 컨테이너 레지스트리에 푸시될 때 응용 프로그램 이미지의 빌드를 자동으로 트리거하는 ACR 작업을 만듭니다.

[Dockerfile-app][dockerfile-app]: 기반이 되는 Node.js 버전을 표시하는 정적 웹 페이지를 렌더링하는 작은 Node.js 웹 애플리케이션입니다. 버전 문자열이 시뮬레이션되며, 기본 이미지에 정의된 `NODE_VERSION` 환경 변수의 내용을 표시합니다.

[Dockerfile-base][dockerfile-base]: `Dockerfile-app`에서 기본으로 지정하는 이미지입니다. 이미지 자체는 [노드][base-node] 이미지를 기반으로 하며 `NODE_VERSION` 환경 변수를 포함합니다.

다음 섹션에서는 작업을 만들고, Dockerfile 기본 이미지의 `NODE_VERSION` 값을 업데이트한 다음, ACR 작업을 사용하여 기본 이미지를 빌드합니다. ACR 작업에서 새 기본 이미지를 레지스트리에 푸시하면 응용 프로그램 이미지의 빌드가 자동으로 트리거됩니다. 필요에 따라 애플리케이션 컨테이너 이미지를 로컬로 실행하여 빌드된 이미지에 다른 버전 문자열을 표시합니다.

## <a name="build-the-base-image"></a>기본 이미지 빌드

먼저 ACR 작업 *빠른 빌드*를 사용하여 기본 이미지를 빌드합니다. 시리즈의 [첫 번째 자습서](container-registry-tutorial-quick-task.md)에서 설명한 대로 이 프로세스는 이미지를 빌드할 뿐만 아니라 빌드가 성공하면 컨테이너 레지스트리에 푸시합니다.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>작업을 만듭니다.

다음으로, [az acr task create][az-acr-task-create]를 사용하여 작업을 만듭니다.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 이전에 미리 보기 중에 `az acr build-task` 명령을 사용하여 작업을 만든 경우 [az acr task][az-acr-task] 명령을 사용하여 해당 작업을 다시 만들어야 합니다.

이 작업은 [이전 자습서](container-registry-tutorial-build-task.md)에서 만든 빠른 작업과 비슷합니다. 커밋이 `--context`에 지정된 리포지토리에 푸시될 때 ACR 작업에서 이미지 빌드를 트리거하도록 지시합니다.

*기본 이미지*가 업데이트될 때 이미지의 빌드도 트리거한다는 점에서 동작상의 차이점이 있습니다. `--file` 인수로 지정된 Dockerfile([Dockerfile-app][dockerfile-app])은 동일한 레지스트리 내에 있는 이미지를 기본으로 지정하도록 지원합니다.

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

작업을 실행하면 ACR 작업에서 이미지의 종속성을 검색합니다. `FROM` 문에 지정된 기본 이미지가 동일한 레지스트리 또는 공용 Docker 허브 리포지토리에 있는 경우 기본 이미지가 업데이트될 때마다 이 이미지가 다시 빌드되도록 후크를 추가합니다.

## <a name="build-the-application-container"></a>응용 프로그램 컨테이너 빌드

ACR 작업에서 기본 이미지가 포함된 컨테이너 이미지의 종속성을 결정하고 추적할 수 있게 하려면 작업을 **한 번 이상** **트리거해야 합니다**.

[az acr task run][az-acr-task-run]을 사용하여 작업을 수동으로 트리거하고 응용 프로그램 이미지를 빌드합니다.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

작업이 완료된 후 다음의 선택적 단계를 수행하려면 **실행 ID**(예: "da6")를 적어 둡니다.

### <a name="optional-run-application-container-locally"></a>선택 사항: 로컬로 애플리케이션 컨테이너 실행

로컬(Cloud Shell이 아님)에서 작업 중이고 Docker가 설치되어 있는 경우, 기본 이미지를 다시 빌드하기 전에 컨테이너를 실행하여 웹 브라우저에서 렌더링된 애플리케이션을 확인합니다. Cloud Shell을 사용하는 경우 이 섹션을 건너뜁니다(Cloud Shell에서는 `az acr login` 또는 `docker run`을 지원하지 않음).

먼저 [az acr login][az-acr-login]을 사용하여 컨테이너 레지스트리에 로그인합니다.

```azurecli
az acr login --name $ACR_NAME
```

이제 `docker run`을 사용하여 컨테이너를 로컬로 실행합니다. **\<run-id\>** 를 이전 단계의 출력에 있는 실행 ID(예: "da6")로 바꿉니다.

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

브라우저에서 http://localhost:8080으로 이동하면 다음과 비슷하게 웹 페이지에 렌더링된 Node.js 버전 번호가 표시됩니다. 이후 단계에서 버전 문자열에 "a"를 추가하여 버전을 범프합니다.

![브라우저에서 렌더링된 샘플 애플리케이션의 스크린샷][base-update-01]

## <a name="list-the-builds"></a>빌드 나열

다음으로, [az acr task list-runs][az-acr-task-list-runs] 명령을 사용하여 ACR 작업에서 레지스트리에 대해 완료한 작업 실행을 나열합니다.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

이전 자습서를 완료했으면(레지스트리를 삭제하지 않은 경우) 다음과 비슷한 출력이 표시됩니다. 다음 섹션에서 기본 이미지를 업데이트한 후 출력을 비교할 수 있도록 작업 실행 횟수와 최신 RUN ID를 적어 둡니다.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>기본 이미지 업데이트

여기서는 기본 이미지의 프레임워크 패치를 시뮬레이션합니다. **Dockerfile-base**를 편집하고, `NODE_VERSION`에 정의된 버전 번호 뒤에 "a"를 추가합니다.

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

빠른 작업을 실행하여 수정된 기본 이미지를 빌드합니다. 출력에 있는 **실행 ID**를 적어 둡니다.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

빌드가 완료되고 ACR 작업에서 새 기본 이미지를 레지스트리에 푸시하면 응용 프로그램 이미지의 빌드가 트리거됩니다. 새로 빌드되고 푸시된 기본 이미지를 검색해야 하므로 이전에 만든 작업에서 응용 프로그램 이미지 빌드를 트리거하는 데 약간의 시간이 걸릴 수 있습니다.

## <a name="list-updated-build"></a>업데이트된 빌드 나열

이제 기본 이미지를 업데이트했으므로 작업 실행을 다시 나열하여 이전 목록과 비교합니다. 처음에 출력이 다르지 않으면 명령을 정기적으로 실행하여 새 작업 실행이 목록에 표시되는지 확인합니다.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

출력은 다음과 유사합니다. 마지막으로 실행된 빌드에 대한 TRIGGER는 기본 이미지의 빠른 작업에서 작업을 시작했음을 나타내는 "Image Update"(이미지 업데이트)여야 합니다.

```console
$ az acr task list-builds --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

새로 빌드한 컨테이너를 실행하여 업데이트된 버전 번호를 확인하는 다음의 선택적 단계를 수행하려면 Image Update가 트리거된 빌드에 대한 **RUN ID** 값을 적어 둡니다(앞의 출력에서 "da8"임).

### <a name="optional-run-newly-built-image"></a>선택 사항: 새로 빌드된 이미지 실행

로컬(Cloud Shell이 아님)에서 작업 중이고 Docker가 설치되어 있는 경우, 빌드가 완료되면 새 애플리케이션 이미지를 실행합니다. `<run-id>`를 이전 단계에서 얻은 RUN ID로 바꿉니다. Cloud Shell을 사용하는 경우 이 섹션을 건너뜁니다(Cloud Shell에서는 `docker run`을 지원하지 않음).

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

브라우저에서 http://localhost:8081로 이동하면 웹 페이지에 업데이트된 Node.js 버전 번호("a" 포함)가 표시됩니다.

![브라우저에서 렌더링된 샘플 애플리케이션의 스크린샷][base-update-02]

중요한 것은 **기본** 이미지를 새 버전 번호로 업데이트했지만 마지막으로 빌드된 **응용 프로그램** 이미지에 새 버전이 표시된다는 것입니다. ACR 작업에서 기본 이미지에 대한 변경 내용을 선택하고 응용 프로그램 이미지를 자동으로 다시 빌드했습니다.

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 레지스트리, 컨테이너 인스턴스, 키 자격 증명 모음 및 서비스 주체를 포함하여 이 자습서 시리즈에서 만든 모든 리소스를 제거하려면 다음 명령을 실행합니다.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 이미지의 기본 이미지가 업데이트되었을 때 작업을 사용하여 컨테이너 이미지 빌드를 자동으로 트리거하는 방법을 알아보았습니다. 이제 컨테이너 레지스트리 인증에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Container Registry의 인증](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png