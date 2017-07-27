---
title: "Azure Container Service 자습서 - 앱 준비 | Microsoft Docs"
description: "Azure Container Service 자습서 - 앱 준비"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Azure Container Service에 사용할 컨테이너 이미지 만들기

이 자습서에서는 Kubernetes에 대해 응용 프로그램을 준비합니다. 완료되는 단계는 다음과 같습니다.  

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제  
> * 응용 프로그램 소스에서 컨테이너 이미지 만들기
> * 로컬 Docker 환경에서 이미지 테스트

이후 자습서에서는 이러한 컨테이너 이미지를 Azure Container Registry에 업로드한 다음 Azure에서 호스트되는 Kubernetes 클러스터에서 실행합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서에서는 컨테이너, 컨테이너 이미지 및 기본 Docker 명령과 같은 핵심 Docker 개념에 대한 기본적인 지식이 있다고 가정합니다. 필요한 경우 컨테이너 기본 사항에 대한 입문서는 [Get started with Docker]( https://docs.docker.com/get-started/)(Docker 시작)를 참조하세요. 

이 자습서를 완료하려면 Docker 개발 환경이 필요합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

## <a name="get-application-code"></a>응용 프로그램 코드 가져오기

이 자습서에서 사용되는 응용 프로그램 예제는 기본 투표 앱입니다. 이 응용 프로그램은 프런트 엔드 웹 구성 요소 및 백 엔드 데이터베이스로 구성됩니다. 

Git을 사용하여 개발 환경에 응용 프로그램 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

응용 프로그램 디렉터리 내에서 미리 만들어진 Dockerfile 및 Kubernetes 매니페스트 파일을 찾을 수 있습니다. 이러한 파일은 자습서 집합 전체에서 자산을 만드는 데 사용됩니다. 

## <a name="create-container-images"></a>컨테이너 이미지 만들기

응용 프로그램 프런트 엔드용 컨테이너 이미지를 만들려면 [Docker 빌드](https://docs.docker.com/engine/reference/commandline/build/) 명령을 사용합니다.

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

이번에는 백 엔드 컨테이너 이미지에 대해 명령을 반복합니다.

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

완료되면 `docker images` 명령을 사용하여 만든 이미지를 확인합니다. 

```bash
docker images
```

출력:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>응용 프로그램 테스트

두 개의 컨테이너 이미지를 만들었으므로 로컬 개발 환경에서 이러한 이미지를 테스트합니다. 

먼저 Docker 네트워크를 만듭니다. 이 네트워크는 컨테이너 간의 통신에 사용됩니다.  

```bash
docker network create azure-vote
```

`docker run` 명령을 사용하여 백 엔드 컨테이너 이미지의 인스턴스를 실행합니다.

이 예제에서는 컨테이너 내에 mysql 데이터베이스 파일을 저장합니다. 이 응용 프로그램을 Kubernete 클러스터를 이동하고 나면 외부 데이터 볼륨은 데이터베이스 파일을 저장하는 데 사용됩니다. 또한 환경 변수는 MySQL 자격 증명을 설정하는 데 사용됩니다.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

프런트 엔드 컨테이너 이미지의 인스턴스를 실행합니다.

환경 변수는 데이터베이스 연결 정보를 구성하는 데 사용됩니다.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

완료되면 `docker ps`를 실행하여 실행 중인 컨테이너를 확인합니다.  

```bash
docker ps
```

출력:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

`http://localhost:8080`으로 이동하여 실행 중인 응용 프로그램을 확인합니다. 응용 프로그램을 초기화하는 데 몇 초가 걸립니다. 오류가 발생하면 다시 시도합니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>리소스 정리

응용 프로그램 기능의 유효성을 검사했으므로 실행 중인 컨테이너를 중지하고 제거할 수 있습니다. 컨테이너 이미지를 삭제하지 마세요. 이러한 이미지는 다음 자습서에서는 Azure Container Registry 인스턴스에 업로드됩니다.

[docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 명령을 사용하여 프런트 엔드 컨테이너를 중지하고 삭제합니다. 

```bash
docker rm -f azure-vote-front
```

[docker rm](https://docs.docker.com/engine/reference/commandline/rm/) 명령을 사용하여 백 엔드 컨테이너를 중지하고 삭제합니다. 

```bash
docker rm -f azure-vote-back
```

[docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/) 명령을 사용하여 네트워크를 삭제합니다.

```bash
docker network rm azure-vote
```

완료되면 Azure 투표 응용 프로그램을 구성하는 두 개의 컨테이너 이미지가 있는 상태가 됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 응용 프로그램을 테스트하고 응용 프로그램에 대한 컨테이너 이미지를 만들었습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * GitHub에서 응용 프로그램 소스 복제  
> * 응용 프로그램 소스에서 컨테이너 이미지 만들기
> * 로컬 Docker 환경에서 이미지 테스트

다음 자습서로 이동하여 Azure Container Registry에 컨테이너 이미지 저장에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Container Registry에 이미지 밀어넣기](./container-service-tutorial-kubernetes-prepare-acr.md)
