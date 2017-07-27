---
title: "Azure Container Service 자습서 - ACR 준비 | Microsoft Docs"
description: "Azure Container Service 자습서 - ACR 준비"
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
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: adc00cb52d5805e152ca742f4cd6b9fab3ed3788
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017

---

# <a name="deploy-and-use-azure-container-registry"></a>Azure Container Registry 배포 및 사용

ACR(Azure Container Registry)은 Docker 컨테이너 이미지를 위한 Azure 기반의 개인 레지스트리입니다. 이 자습서에서는 Azure Container Registry 인스턴스를 배포하고 컨테이너 이미지를 이 인스턴스에 밀어넣는 과정을 안내합니다. 완료되는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 배포
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드

이후 자습서에서는 컨테이너 이미지를 안전하게 실행하기 위해 이 ACR 인스턴스를 Azure Container Service Kubernetes 클러스터와 통합합니다. 

## <a name="before-you-begin"></a>시작하기 전에

[이전 자습서](./container-service-tutorial-kubernetes-prepare-app.md)에서는 간단한 Azure 투표 응용 프로그램을 위한 컨테이너 이미지를 만들었습니다. 이 자습서에서는 이러한 이미지를 Azure Container Registry에 밀어넣습니다. Azure 투표 앱 이미지를 만들지 않은 경우 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 또는 여기에 자세히 설명된 단계는 모든 컨테이너 이미지와 작동합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="deploy-azure-container-registry"></a>Azure Container Registry 배포

Azure Container Registry를 배포할 때는 먼저 리소스 그룹이 필요합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az acr create](/cli/azure/acr#create) 명령으로 Azure Container Registry를 만듭니다. 컨테이너 레지스트리의 이름은 **고유해야 합니다**. 다음 예제를 사용하여 임의 문자로 이름을 업데이트합니다.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic --admin-enabled true
```

## <a name="get-acr-information"></a>ACR 정보 가져오기 

ACR 인스턴스를 만들고 나면 이름, 로그인 서버 이름 및 인증 암호가 필요합니다. 다음 코드는 이러한 각 값을 반환합니다. 이 자습서 전체에서 참조되므로 각 값을 기록해 둡니다.  

ACR 이름 및 로그인 서버:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrName:name,acrLoginServer:loginServer}" --output table
```

ACR 암호 - ACR 이름으로 업데이트합니다.

```azurecli-interactive
az acr credential show --name <acrName> --query passwords[0].value -o tsv
```

## <a name="container-registry-login"></a>컨테이너 레지스트리 로그인

ACR 인스턴스에 이미지를 밀어넣기 전에 먼저 ACR 인스턴스에 로그인해야 합니다. [Docker 로그인](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 작업을 완료합니다. Docker 로그인을 실행할 때 ACR 로그인 서버 이름 및 ACR 자격 증명을 제공해야 합니다.

```bash
docker login --username=<acrName> --password=<acrPassword> <acrLoginServer>
```

이 명령은 완료되면 ‘로그인했습니다.’ 메시지를 반환합니다.

## <a name="tag-container-images"></a>컨테이너 이미지 태그 지정

각 컨테이너 이미지에 레지스트리의 `loginServer` 이름으로 태그를 지정해야 합니다. 이 태그는 컨테이너 이미지를 이미지 레지스트리에 밀어넣을 때 라우팅에 사용됩니다.

현재 이미지 목록을 보려면 `docker images` 명령을 사용합니다.

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

*azure-vote-front* 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다. 또한 이미지 이름 끝에 `:v1`을 추가합니다. 이 태그는 이미지 버전 번호를 나타냅니다.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

*azure-vote-back* 이미지에 대해 명령을 반복합니다.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

태그가 지정되면 `docker images`를 실행하여 작업을 확인합니다.

```bash
docker images
```

출력:

```bash
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                                      latest              a9dace4e1a17        7 minutes ago       407 MB
mycontainerregistry082.azurecr.io/azure-vote-back    v1                  a9dace4e1a17        7 minutes ago       407 MB
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1                  eaf2b9c57e5e        8 minutes ago       716 MB
mysql                                                latest              e799c7f9ae9c        6 weeks ago         407 MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-acr"></a>ACR에 이미지 밀어넣기

레지스트리에 *azure-vote-front* 이미지를 밀어넣습니다. 

다음 예제를 사용하여 ACR loginServer 이름을 해당 환경의 loginServer로 바꿉니다. 이 작업은 완료되는 데 2~3분이 걸립니다.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

*azure-vote-back* 이미지에 대해 같은 작업을 수행합니다.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

## <a name="list-images-in-acr"></a>ACR의 이미지 나열 

Azure Container Registry에 밀어넣은 이미지 목록을 반환하려면 [az acr repository list](/cli/azure/acr/repository#list) 명령을 사용합니다. ACR 인스턴스 이름으로 명령을 업데이트합니다.

```azurecli-interactive
az acr repository list --name <acrName> --username <acrName> --password <acrPassword> --output table
```

출력:

```azurecli
Result
----------------
azure-vote-back
azure-vote-front
```

그런 다음 특정 이미지에 대한 태그를 보려면 [az acr repository show-tags](/cli/azure/acr/repository#show-tags) 명령을 사용합니다.

```azurecli-interactive
az acr repository show-tags --name <acrName> --username <acrName> --password <acrPassword> --repository azure-vote-front --output table
```

출력:

```azurecli
Result
--------
v1
```

자습서를 완료하면 전용 Azure Container Registry 인스턴스에 두 개의 컨테이너 이미지가 저장된 상태가 됩니다. 이후 자습서에서 이러한 이미지는 ACR에서 Kubernetes 클러스터로 배포됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ACS Kubernetes 클러스터에서 사용하기 위해 Azure Container Registry를 준비했습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * Azure Container Registry 인스턴스 배포
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드

다음 자습서로 이동하여 Azure에서 Kubernetes 클러스터 배포에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes 클러스터 배포](./container-service-tutorial-kubernetes-deploy-cluster.md)
