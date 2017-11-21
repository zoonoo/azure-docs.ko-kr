---
title: "Azure Container Instances 자습서 - 앱 준비 "
description: "Azure Container Instances에 배포할 앱 준비"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 5de53266e1dbadecb9fabb1649615fa9f4ba8b5f
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="create-container-for-deployment-to-azure-container-instances"></a>Azure Container Instances에 배포를 위한 컨테이너 만들기

Azure Container Instances를 통해 어떠한 가상 컴퓨터를 프로비전하지 않고 또 더 높은 수준의 서비스를 채택하지 않고도 Azure로 Docker 컨테이너를 배포할 수 있습니다. 이 자습서에서는 Node.js에서 작은 웹 응용 프로그램을 빌드하고 Azure Container Instances를 사용하여 실행할 수 있는 컨테이너로 패키지합니다. 다음 항목에 대해서 다룹니다.

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제
> * 응용 프로그램 소스에서 컨테이너 이미지 만들기
> * 로컬 Docker 환경에서 이미지 테스트

후속 자습서에서는 이미지를 Azure Container Registry에 업로드한 후 Azure Container Instances에 배포합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

이 자습서에서는 컨테이너, 컨테이너 이미지 및 기본 `docker` 명령과 같은 핵심 Docker 개념에 대한 기본적인 지식이 있다고 가정합니다. 필요한 경우 컨테이너 기본 사항에 대한 입문서는 [Get started with Docker(Docker 시작)]( https://docs.docker.com/get-started/)를 참조하세요.

이 자습서를 완료하려면 Docker 개발 환경이 필요합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

Azure Cloud Shell에는 이 자습서의 모든 단계를 완료하는 데 필요한 Docker 구성 요소가 포함되어 있지 않습니다. 따라서 Azure CLI 및 Docker 개발 환경을 로컬로 설치하는 것이 좋습니다.

## <a name="get-application-code"></a>응용 프로그램 코드 가져오기

이 자습서의 샘플에는 [Node.js](http://nodejs.org)에서 빌드한 간단한 웹 응용 프로그램이 포함되어 있습니다. 앱은 정적 HTML 페이지로 사용되고 다음과 유사합니다.

![브라우저에 표시된 자습서 앱][aci-tutorial-app]

git을 사용하여 샘플 다운로드:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

## <a name="build-the-container-image"></a>컨테이너 이미지 빌드

샘플 리포지토리에서 제공된 Dockerfile은 컨테이너가 빌드되는 방식을 보여 줍니다. 컨테이너에 사용하기에 적합한 소규모 배포인 [Alpine Linux](https://alpinelinux.org/) 기반의 [공식 Node.js 이미지][dockerhub-nodeimage]로 시작합니다. 그런 다음 응용 프로그램 파일을 컨테이너에 복사하고 노드 패키지 관리자를 사용하여 종속성을 설치한 후 마지막으로 응용 프로그램을 시작합니다.

```Dockerfile
FROM node:8.2.0-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

`docker build` 명령을 사용하여 *aci-tutorial-app*으로 태그가 지정된 컨테이너 이미지를 만듭니다.

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

`docker build` 명령의 출력은 다음과 비슷합니다(가독성을 위해 잘림).

```bash
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.2.0-alpine
8.2.0-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.2.0-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

`docker images`를 사용하여 빌드된 이미지를 표시합니다.

```bash
docker images
```

출력:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

## <a name="run-the-container-locally"></a>컨테이너를 로컬로 실행

컨테이너를 Azure Container Instances로 배포하기 전에 로컬로 실행하여 작동 상태를 확인합니다. `-d` 스위치를 통해 컨테이너를 백그라운드로 실행할 수 있으며 `-p`를 통해 컴퓨터의 임의 포트를 컨테이너의 포트 80에 매핑할 수 있습니다.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

http://localhost:8080 을 브라우저에서 열어 컨테이너가 실행 중인지 확인합니다.

![브라우저에서 앱을 로컬로 실행][aci-tutorial-app-local]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Instances에 배포할 수 있는 컨테이너 이미지를 만들었습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제
> * 응용 프로그램 소스에서 컨테이너 이미지 만들기
> * 컨테이너를 로컬로 테스트

다음 자습서로 이동하여 Azure Container Registry에 컨테이너 이미지 저장에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Container Registry에 이미지 밀어넣기](./container-instances-tutorial-prepare-acr.md)

<!-- LINKS -->
[dockerhub-nodeimage]: https://store.docker.com/images/node

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png