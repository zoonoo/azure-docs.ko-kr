---
title: "Azure Container Instances 자습서 - Azure Container Registry 준비"
description: "Azure Container Instances 자습서 - Azure Container Registry 준비"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: mmacy
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 848f6cbde49efdcfe96fc58ebc4160e0ea39f3f2
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry 배포 및 사용

세 부분으로 이루어진 자습서의 두 번째 부분입니다. [이전 단계](container-instances-tutorial-prepare-app.md)에서 [Node.js](http://nodejs.org)로 작성된 간단한 웹 응용 프로그램에 컨테이너 이미지를 만들었습니다. 이 자습서에서는 이 이미지를 Azure Container Registry에 푸시합니다. 컨테이너 이미지를 만들지 않은 경우 [자습서 1 - 컨테이너 이미지 만들기](container-instances-tutorial-prepare-app.md)로 돌아갑니다.

Azure Container Registry는 Docker 컨테이너 이미지를 위한 Azure 기반의 개인 레지스트리입니다. 이 자습서에서는 Azure Container Registry 인스턴스를 배포하고 컨테이너 이미지를 이 인스턴스에 밀어넣는 과정을 안내합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 배포
> * Azure Container Registry에 컨테이너 이미지 태그 지정
> * Azure Container Registry에 이미지 업로드

후속 자습서에서는 Azure Container Instances에 개인 레지스트리의 컨테이너를 배포합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

이 자습서를 완료하려면 Docker 개발 환경이 필요합니다. Docker는 모든 [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 또는 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에서 쉽게 Docker를 구성하는 패키지를 제공합니다.

Azure Cloud Shell에는 이 자습서의 모든 단계를 완료하는 데 필요한 Docker 구성 요소가 포함되어 있지 않습니다. 따라서 Azure CLI 및 Docker 개발 환경을 로컬로 설치하는 것이 좋습니다.

## <a name="deploy-azure-container-registry"></a>Azure Container Registry 배포

Azure Container Registry를 배포할 때는 먼저 리소스 그룹이 필요합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az acr create](/cli/azure/acr#create) 명령으로 Azure Container Registry를 만듭니다. 컨테이너 레지스트리 이름은 Azure 내에서 **고유해야 하며**, 5-50자의 영숫자만 포함해야 합니다. `<acrName>`를 레지스트리의 고유한 이름으로 바꿉니다.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

예를 들어 *mycontainerregistry082*라는 Azure Container Registry를 만들려면 다음을 수행합니다.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

이 자습서의 나머지 부분에서는 선택한 컨테이너 레지스트리 이름의 자리 표시자로 `<acrName>`을 사용합니다.

## <a name="container-registry-login"></a>컨테이너 레지스트리 로그인

ACR 인스턴스에 이미지를 밀어넣기 전에 먼저 ACR 인스턴스에 로그인해야 합니다. [az acr login](/cli/azure/acr#az_acr_login) 명령을 사용하여 작업을 완료합니다. 컨테이너 레지스트리가 생성될 때 지정된 고유한 이름을 입력해야 합니다.

```azurecli
az acr login --name <acrName>
```

완료되면 이 명령은 `Login Succeeded` 메시지를 반환합니다.

## <a name="tag-container-image"></a>컨테이너 이미지 태그 지정

개인 레지스트리의 컨테이너 이미지를 배포하려면 이미지는 레지스트리 `loginServer` 이름으로 태그를 지정해야 합니다.

현재 이미지 목록을 보려면 `docker images` 명령을 사용합니다.

```bash
docker images
```

출력:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

loginServer 이름을 가져오려면 다음 명령을 실행합니다. `<acrName>`을 컨테이너 레지스트리의 이름으로 바꿉니다.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

예제 출력:

```
Result
------------------------
mycontainerregistry082.azurecr.io
```

*aci-tutorial-app* 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다. 또한 이미지 이름 끝에 `:v1`을 추가합니다. 이 태그는 이미지 버전 번호를 나타냅니다. `<acrLoginServer>`를 방금 실행한 `az acr show` 명령의 결과로 바꿉니다.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

태그가 지정되면 `docker images`를 실행하여 작업을 확인합니다.

```bash
docker images
```

출력:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Azure Container Registry에 이미지 푸시하기

`docker push` 명령을 사용하여 *aci-tutorial-app* 이미지를 레지스트리에 푸시합니다. `<acrLoginServer>`를 이전 단계에서 얻은 전체 로그인 서버 이름으로 바꿉니다.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push` 작업은 인터넷 연결에 따라 몇 초에서 몇 분 정도 걸리고 출력은 다음과 유사합니다.

```bash
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

Azure Container Registry로 푸시한 이미지 목록을 반환하려면 [az acr repository list](/cli/azure/acr/repository#list) 명령을 사용합니다. 이 명령을 컨테이너 레지스트리 이름으로 업데이트합니다.

```azurecli
az acr repository list --name <acrName> --output table
```

출력:

```azurecli
Result
----------------
aci-tutorial-app
```

그런 다음 특정 이미지에 대한 태그를 보려면 [az acr repository show-tags](/cli/azure/acr/repository#show-tags) 명령을 사용합니다.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

출력:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Container Registry를 Azure Container Instances와 함께 사용하도록 준비하고 컨테이너 이미지를 푸시했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 배포
> * Azure Container Registry에 컨테이너 이미지 태그 지정
> * Azure Container Registry에 이미지 업로드

Azure Container Instances를 사용하여 컨테이너를 Azure에 배포하는 방법에 대해 자세히 알아보려면 다음 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances에 컨테이너 배포](./container-instances-tutorial-deploy-app.md)
