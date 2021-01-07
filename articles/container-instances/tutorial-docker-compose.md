---
title: 자습서 - Docker Compose를 사용하여 다중 컨테이너 그룹 배포
description: Docker Compose를 사용하여 다중 컨테이너 애플리케이션을 빌드 및 실행한 다음, 애플리케이션을 Azure Container Instances로 가져옵니다.
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913843"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>자습서: Docker Compose를 사용하여 다중 컨테이너 그룹 배포 

이 자습서에서는 [Docker Compose](https://docs.docker.com/compose/)를 사용하여 다중 컨테이너 애플리케이션을 정의하고, 로컬로 실행한 다음, Azure Container Instances에서 [컨테이너 그룹](container-instances-container-groups.md)으로 배포합니다. 

Docker를 사용하여 클라우드 네이티브 애플리케이션을 개발하고 로컬 개발에서 클라우드 배포로 원활하게 전환하려는 경우 Azure Container Instances 주문형에서 컨테이너를 실행합니다. 이 기능은 [Docker와 Azure 간의 통합](https://docs.docker.com/engine/context/aci-integration/)을 통해 사용하도록 설정됩니다. 기본 Docker 명령을 사용하여 Azure에서 [단일 컨테이너 인스턴스](quickstart-docker-cli.md) 또는 다중 컨테이너 그룹을 실행할 수 있습니다.

> [!IMPORTANT]
> Azure Container Instances의 모든 기능이 지원되는 것은 아닙니다. [Docker ACI 통합](https://github.com/docker/aci-integration-beta) GitHub 리포지토리에서 문제를 만들어 Docker-Azure 통합에 대한 피드백을 제공합니다.

> [!TIP]
> [Visual Studio Code용 Docker 확장](https://aka.ms/VSCodeDocker)을 통합 환경에 사용하여 컨테이너, 이미지 및 컨텍스트를 개발, 실행 및 관리할 수 있습니다.

이 문서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Container Registry 만들기
> * GitHub에서 애플리케이션 원본 코드 복제
> * Docker Compose를 사용하여 이미지를 빌드하고 다중 컨테이너 애플리케이션을 로컬로 실행
> * 컨테이너 레지스트리에 애플리케이션 이미지 푸시
> * Docker에 대한 Azure 컨텍스트 만들기
> * Azure Container Instances에서 애플리케이션 가져오기

## <a name="prerequisites"></a>사전 요구 사항

* **Azure CLI** - Azure CLI가 로컬 컴퓨터에 설치되어 있어야 합니다. 버전 2.10.1 이상을 사용하는 것이 좋습니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

* **Docker Desktop** - [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) 또는 [macOS](https://desktop.docker.com/mac/edge/Docker.dmg)에서 사용할 수 있는 Docker Desktop 버전 2.3.0.5 이상을 사용해야 합니다. 또는 [Linux용 Docker ACI 통합 CLI](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)를 설치합니다.

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>애플리케이션 코드 가져오기

이 자습서에서 사용되는 애플리케이션 예제는 기본 투표 앱입니다. 애플리케이션은 프런트 엔드 웹 구성 요소 및 백 엔드 Redis 인스턴스로 구성됩니다. 웹 구성 요소는 사용자 지정 컨테이너 이미지에 패키지됩니다. Redis 인스턴스는 Docker 허브에서 수정되지 않은 이미지를 사용합니다.

[git](https://git-scm.com/downloads)을 사용하여 애플리케이션 예제를 개발 환경에 복제합니다.

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

복제된 디렉터리로 변경합니다.

```console
cd azure-voting-app-redis
```

디렉터리 내에는 애플리케이션 소스 코드와 docker-compose.yaml(미리 만든 Docker Compose 파일)이 있습니다.

## <a name="modify-docker-compose-file"></a>Docker Compose 파일 수정

텍스트 편집기에서 docker-compose.yaml을 엽니다. 이 파일은 `azure-vote-back` 및 `azure-vote-front` 서비스를 구성합니다.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

`azure-vote-front` 구성에서 다음 두 가지 사항을 변경합니다.

1. `azure-vote-front` 서비스의 `image` 속성을 업데이트합니다. 이미지 이름 앞에 Azure Container Registry의 로그인 서버 이름인 \<acrName\>.azurecr.io를 붙입니다. 예를 들어 레지스트리의 이름이 *myregistry* 인 경우 로그인 서버 이름은 *myregistry.azurecr.io* (모두 소문자)이고 이미지 속성은 `myregistry.azurecr.io/azure-vote-front`입니다.
1. `ports` 매핑을 `80:80`으로 변경합니다. 파일을 저장합니다.

업데이트된 파일은 다음과 같습니다.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

이러한 대체 작업을 수행하면 Azure Container Registry에 대한 태그가 다음 단계에서 빌드하는 `azure-vote-front` 이미지에 지정되고, 이 이미지를 끌어와서 Azure Container Instances에서 실행할 수 있습니다.

> [!TIP]
> 이 시나리오에서는 Azure Container Registry를 사용할 필요가 없습니다. 예를 들어 Docker Hub에서 애플리케이션 이미지를 호스팅할 프라이빗 리포지토리를 선택할 수 있습니다. 다른 레지스트리를 선택하는 경우 이미지 속성을 적절하게 업데이트합니다.

## <a name="run-multi-container-application-locally"></a>로컬로 다중 컨테이너 애플리케이션 실행

`docker-compose.yaml` 파일 샘플을 사용하여 컨테이너 이미지를 빌드하고, Redis 이미지를 다운로드하고, 애플리케이션을 시작하는 [docker-compose up](https://docs.docker.com/compose/reference/up/)을 실행합니다.

```console
docker-compose up --build -d
```

완료되면 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 만든 이미지를 확인합니다. 3개 이미지가 다운로드되거나 생성되었는지 확인합니다. `azure-vote-front` 이미지에는 `uwsgi-nginx-flask` 이미지를 기본으로 사용하는 프런트 엔드 애플리케이션이 포함되어 있습니다. `redis` 이미지는 Redis 인스턴스를 시작하는 데 사용됩니다.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

[docker ps](https://docs.docker.com/engine/reference/commandline/ps/) 명령을 실행하여 실행 중인 컨테이너를 확인합니다.

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

실행 중인 애플리케이션을 보려면 로컬 웹 브라우저에 `http://localhost:80`을 입력합니다. 다음 예제처럼 샘플 애플리케이션이 로드됩니다.

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="투표 앱 이미지":::

로컬 애플리케이션을 시도한 후 [docker-compose down](https://docs.docker.com/compose/reference/down/)을 실행하여 애플리케이션을 중지하고 컨테이너를 제거합니다.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>컨테이너 레지스트리에 이미지 푸시

애플리케이션을 Azure Container Instances에 배포하려면 `azure-vote-front` 이미지를 컨테이너 레지스트리에 푸시해야 합니다. [docker-compose push](https://docs.docker.com/compose/reference/push)를 실행하여 이미지를 푸시합니다.

```console
docker-compose push
```

레지스트리에 푸시하는 데 몇 분 정도 걸릴 수 있습니다.

이미지가 레지스트리에 저장되었는지 확인하려면 [az acr repository show](/cli/azure/acr/repository#az-acr-repository-show) 명령을 실행합니다.

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Azure Container Instances에 애플리케이션 배포

다음으로 ACI 컨텍스트를 변경합니다. 후속 Docker 명령은 이 컨텍스트에서 실행됩니다.

```console
docker context use myacicontext
```

`docker compose up`을 실행하여 Azure Container Instances에서 애플리케이션을 시작합니다. 컨테이너 레지스트리에서 `azure-vote-front` 이미지를 끌어오고, 컨테이너 그룹이 Azure Container Instances에 만들어집니다.

```console
docker compose up
```

> [!NOTE]
> 현재 ACI 컨텍스트에서 사용할 수 있는 Docker Compose 명령은 `docker compose up` 및 `docker compose down`입니다. 이러한 명령에서 `docker` 및 `compose` 사이에는 하이픈이 없습니다.

컨테이너 그룹이 짧은 시간 내에 배포됩니다. 샘플 출력:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

`docker ps`를 실행하여 컨테이너 그룹에 할당된 실행 중인 컨테이너와 IP 주소를 확인합니다.

```console
docker ps
```

샘플 출력:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

클라우드에서 실행 중인 애플리케이션을 확인하려면 로컬 웹 브라우저에 표시된 IP 주소를 입력합니다. 이 예제에서는 `52.179.23.131`을 입력합니다. 다음 예제처럼 샘플 애플리케이션이 로드됩니다.

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="투표 앱 이미지":::

프런트 엔드 컨테이너의 로그를 확인하려면 [docker logs](https://docs.docker.com/engine/reference/commandline/logs) 명령을 실행합니다. 다음은 그 예입니다. 

```console
docker logs azurevotingappredis_azure-vote-front
```

또한 Azure Portal 또는 다른 Azure 도구를 사용하여 배포한 컨테이너 그룹의 속성과 상태를 확인할 수도 있습니다.

애플리케이션 시도가 완료되면 `docker compose down`을 사용하여 애플리케이션과 컨테이너를 중지합니다.

```console
docker compose down
```

이 명령은 Azure Container Instances에서 컨테이너 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Docker Compose를 사용하여 다중 컨테이너 애플리케이션을 로컬로 실행하는 것에서 Azure Container Instances에서 실행하는 것으로 전환했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Container Registry 만들기
> * GitHub에서 애플리케이션 원본 코드 복제
> * Docker Compose를 사용하여 이미지를 빌드하고 다중 컨테이너 애플리케이션을 로컬로 실행
> * 컨테이너 레지스트리에 애플리케이션 이미지 푸시
> * Docker에 대한 Azure 컨텍스트 만들기
> * Azure Container Instances에서 애플리케이션 가져오기

[Visual Studio Code용 Docker 확장](https://aka.ms/VSCodeDocker)은 컨테이너, 이미지 및 컨텍스트를 개발, 실행 및 관리하는 통합 환경에 사용할 수도 있습니다.

Azure Container Instances에서 더 많은 기능을 활용하려면 Azure 도구를 사용하여 다중 컨테이너 그룹을 지정합니다. 예를 들어 [YAML 파일](container-instances-multi-container-yaml.md)과 함께 Azure CLI를 사용하여 컨테이너 그룹을 배포하거나 [Azure Resource Manager 템플릿](container-instances-multi-container-group.md)을 사용하여 배포하는 자습서를 참조하세요. 