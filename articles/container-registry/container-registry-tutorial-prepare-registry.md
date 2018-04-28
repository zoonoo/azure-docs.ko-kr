---
title: Azure Container Registry 자습서 - 지리적 복제 Azure Container Registry 준비
description: Azure Container Registry를 만들고 지리적 복제를 구성하고 Docker 이미지를 준비한 다음 레지스트리에 배포합니다. 3부로 구성된 시리즈 중 제1부입니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 10/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 2e91a92d34131d0b35cfb7b0bfdca99637924552
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>자습서: 지역 복제 Azure Container Registry 준비

Azure Container Registry는 네트워크를 배포에 가깝게 유지할 수 있는 Azure에 배포된 개인 Docker 레지스트리입니다. 3부로 구성된 이 자습서에서는 지리적 복제를 사용하여 Linux 컨테이너에서 실행되는 ASP.NET Core 웹 응용 프로그램을 두 개의 [Web Apps for Containers](../app-service/containers/index.yml) 인스턴스에 배포하는 방법을 알아봅니다. Azure에서 가장 가까운 지리적 복제 리포지토리의 각 웹앱 인스턴스에 이미지를 자동으로 배포하는 방법을 확인할 수 있습니다.

총 3부 중 1부인 이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 지리적 복제 Azure Container Registry 만들기
> * GitHub에서 응용 프로그램 원본 코드 복제
> * 응용 프로그램 원본에서 Docker 컨테이너 이미지 만들기
> * 컨테이너 이미지를 레지스트리에 푸시하기

후속 자습서에서는 개인 레지스트리의 컨테이너를 두 개의 Azure 지역에서 실행되는 웹앱에 배포합니다. 그런 다음 응용 프로그램에서 코드를 업데이트하고 레지스트리에 대한 단일 `docker push`로 두 개의 웹앱 인스턴스를 업데이트합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

이 자습서에서는 컨테이너, 컨테이너 이미지 및 기본 Docker 명령과 같은 핵심 Docker 개념에 대한 기본적인 지식이 있다고 가정합니다. 필요한 경우 컨테이너 기본 사항에 대한 입문서는 [Get started with Docker(Docker 시작)]( https://docs.docker.com/get-started/)를 참조하세요.

이 자습서를 완료하려면 Docker 개발 환경이 필요합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

Azure Cloud Shell에는 이 자습서의 모든 단계를 완료하는 데 필요한 Docker 구성 요소가 포함되어 있지 않습니다. 따라서 Azure CLI 및 Docker 개발 환경을 로컬로 설치하는 것이 좋습니다.

> [!IMPORTANT]
> Azure Container Registry의 지리적 복제 기능은 현재 **미리 보기** 상태입니다. [부속 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

[Azure 포털](http://portal.azure.com)에 로그인합니다.

**리소스 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][tut-portal-01]

새 레지스트리를 다음 설정으로 구성합니다.

* **레지스트리 이름**: Azure 내에서 전역적으로 고유하며 5~50개의 영숫자가 포함된 레지스트리 이름을 만듭니다.
* **리소스 그룹**: **새로 만들기** > `myResourceGroup`
* **위치**: `West US`
* **관리 사용자**: `Enable`(Web App for Containers에서 이미지를 끌어오는 데 필요)
* **SKU**: `Premium` (지역에서 복제에 필요)

**만들기**를 선택하여 ACR 인스턴스를 배포합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기][tut-portal-02]

이 자습서의 나머지 부분에서는 선택한 컨테이너 **레지스트리 이름**의 자리 표시자로 `<acrName>`을 사용합니다.

> [!TIP]
> Azure Container Registry는 일반적으로 여러 컨테이너 호스트에서 사용되는 수명이 긴 리소스이기 때문에 자체 리소스 그룹에 레지스트리를 만드는 것이 좋습니다. 지리적 복제 레지스트리와 webhook를 구성할 때 이러한 추가 리소스는 동일한 리소스 그룹에 배치됩니다.
>

## <a name="configure-geo-replication"></a>지역에서 복제 구성

프리미엄 레지스트리가 있으니 이제 지역에서 복제를 구성할 수 있습니다. 두 지역에서 실행되도록 다음 자습서에서 구성하는 웹앱은 가장 가까운 레지스트리에서 컨테이너 이미지를 끌어올 수 있습니다.

Azure Portal에서 새 컨테이너 레지스트리로 이동하여 **서비스** 아래에서 **복제**를 선택합니다.

![Azure Portal 컨테이너 레지스트리 UI의 복제][tut-portal-03]

지역에서 복제에 사용할 수 있는 Azure 영역을 나타내는 녹색 육각형이 지도에 표시됩니다.

 ![Azure Portal의 지역 지도][tut-map-01]

녹색 육각형을 선택하여 미국 동부 지역에 레지스트리를 복제한 다음 **복제 만들기**에서 **만들기**를 선택합니다.

 ![Azure Portal에서 복제 UI 만들기][tut-portal-04]

복제가 완료되면 포털에서 두 영역 모두에 *준비*가 반영됩니다. **새로 고침** 단추를 사용하여 복제 상태를 새로 고치세요. 복제본을 만들고 동기화하는 데 1~2분 정도 걸릴 수 있습니다.

![Azure Portal의 복제 상태 UI][tut-portal-05]

## <a name="container-registry-login"></a>컨테이너 레지스트리 로그인

지역에서 복제를 구성했으니 이제 컨테이너 이미지를 작성한 다음 레지스트리로 푸시합니다. 이미지를 푸시하기 전에 우선 ACR 인스턴스에 로그인해야 합니다. [Basic, Standard 및 Premium SKU](container-registry-skus.md)를 사용하는 경우 Azure ID를 사용하여 인증할 수 있습니다.

[az acr login](https://docs.microsoft.com/cli/azure/acr#az_acr_login) 명령을 사용하여 인증하고 레지스트리에 대한 자격 증명을 캐시합니다. `<acrName>`을 이전 단계에서 만든 레지스트리 이름으로 바꿉니다.

```azurecli
az acr login --name <acrName>
```

완료되면 이 명령은 `Login Succeeded`를 반환합니다.

## <a name="get-application-code"></a>응용 프로그램 코드 가져오기

이 자습서의 샘플에는 [ASP.NET Core](http://dot.net)로 작성한 작은 웹 응용 프로그램이 포함되어 있습니다. 이 응용 프로그램은 Azure Container Registry에서 이미지가 배포된 지역을 표시하는 HTML 페이지를 제공합니다.

![브라우저에 표시된 자습서 앱][tut-app-01]

Git을 사용하여 샘플을 로컬 디렉터리에 다운로드하고 `cd`를 디렉터리에 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

## <a name="update-dockerfile"></a>Dockerfile 업데이트

샘플에 포함된 Dockerfile은 컨테이너가 어떻게 작성되었는지 보여줍니다. 공식 [aspnetcore](https://store.docker.com/community/images/microsoft/aspnetcore) 이미지에서 시작하여 응용 프로그램 파일을 컨테이너에 복사하고, 종속성을 설치하고, 공식 [aspnetcore-build](https://store.docker.com/community/images/microsoft/aspnetcore-build) 이미지를 사용하여 출력을 컴파일하고 마지막으로 최적화된 aspnetcore 이미지를 만듭니다.

Dockerfile은 복제된 소스의 `./AcrHelloworld/Dockerfile`에 있습니다.

```dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

*acr-helloworld* 이미지의 응용 프로그램은 레지스트리의 로그인 서버에 대한 정보를 DNS에 쿼리하여 컨테이너가 배포된 영역을 확인하려고 합니다. Dockerfile의 `DOCKER_REGISTRY` 환경 변수에 레지스트리의 로그인 서버 URL을 지정해야 합니다.

먼저 `az acr show` 명령으로 레지스트리의 로그인 서버 URL을 가져옵니다. `<acrName>`을 이전 단계에서 만든 레지스트리 이름으로 바꿉니다.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

출력

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

다음으로, 레지스트리의 로그인 서버 URL로 `DOCKER_REGISTRY` 줄을 업데이트합니다. 이 예에서는 예제 레지스트리 이름 *uniqueregistryname*이 반영되도록 줄을 업데이트합니다.

```dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>컨테이너 이미지 만들기

Dockerfile을 레지스트리 URL로 업데이트했으니 이제 `docker build`를 사용하여 컨테이너 이미지를 만들 수 있습니다. 다음 명령을 실행하여 이미지를 작성하고 개인 레지스트리의 URL로 태그를 지정합니다. 다시 `<acrName>`을 레지스트리 이름으로 바꿉니다.

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Docker 이미지가 빌드되면 여러 줄의 출력이 표시됩니다(여기서는 잘려 표시됨).

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer
...
Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

빌드된 이미지를 보려면 `docker images` 명령을 사용합니다.

```bash
docker images
```

출력

```bash
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
...
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시하기

마지막으로 `docker push` 명령을 사용하여 *acr-helloworld* 이미지를 레지스트리로 푸시합니다. `<acrName>`을 레지스트리 이름으로 바꿉니다.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

지역에서 복제를 위해 레지스트리를 구성했으므로 단일 `docker push` 명령으로 *미국 서부* 및 *미국 동부* 지역 모두에 이미지가 자동으로 복제됩니다.

출력

```bash
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 개인 지리적 복제 컨테이너 레지스트리를 작성하고 컨테이너 이미지를 빌드한 다음 해당 이미지를 레지스트리로 푸시했습니다. 이 자습서의 단계에 따라:

> [!div class="checklist"]
> * 지리적 복제 Azure Container Registry를 만들었습니다.
> * GitHub에서 응용 프로그램 원본 코드를 복제했습니다.
> * 응용 프로그램 원본에서 Docker 컨테이너 이미지를 만들었습니다.
> * 컨테이너 이미지를 레지스트리에 푸시했습니다.

다음 자습서로 이동하여 지역에서 복제를 사용하여 여러 Web App for Containers 인스턴스에 컨테이너를 배포하여 이미지를 로컬로 제공하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry에서 웹앱 배포](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png
