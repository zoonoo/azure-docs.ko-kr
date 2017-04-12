---
title: "Docker 컨테이너 클러스터 배포 - Azure CLI | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 Azure Container Service에 Kubernetes, DC/OS 또는 Docker Swarm 솔루션 배포"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1dad536939f179cd8231d0f8805c1ff4335850d5
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 Docker 컨테이너 호스팅 솔루션 배포

Azure CLI 2.0의 `az acs` 명령을 사용하여 Azure Container Service에서 클러스터를 만들고 관리합니다. [Azure Portal](container-service-deployment.md) 또는 Azure Container Service API를 사용하여 Azure Container Service 클러스터를 배포할 수도 있습니다.

`az acs` 명령에 대한 도움말은 `-h` 매개 변수를 명령에 전달합니다. 예: `az acs create -h`



## <a name="prerequisites"></a>필수 조건
Azure CLI 2.0을 사용하여 Azure Container Service 클러스터를 만들려면 다음을 수행해야 합니다.
* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))이 있어야 합니다.
* [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 설정해야 합니다.

## <a name="get-started"></a>시작 
### <a name="log-in-to-your-account"></a>계정에 로그인
```azurecli
az login 
```

프롬프트를 따라 대화형으로 로그인합니다. 로그인을 위한 다른 메서드는 [Azure CLI 2.0 시작](/cli/azure/get-started-with-az-cli2)을 참조하세요.

### <a name="set-your-azure-subscription"></a>Azure 구독 설정

Azure 구독이 두 개 이상인 경우 기본 구독을 설정합니다. 예:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>리소스 그룹 만들기
모든 클러스터에 대한 리소스 그룹을 만드는 것이 좋습니다. Azure Container Service가 [사용 가능](https://azure.microsoft.com/en-us/regions/services/)한 Azure 지역을 지정합니다. 예:

```azurecli
az group create -n acsrg1 -l "westus"
```
다음과 유사하게 출력됩니다.

![리소스 그룹 만들기](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Azure Container Service 클러스터 만들기

클러스터를 만들려면 `az acs create`를 사용합니다.
컨테이너의 이름, 이전 단계에서 만든 리소스 그룹의 이름은 필수 매개 변수입니다. 

해당 스위치를 사용하여 덮어쓰지 않는 한, 다른 입력은 기본값으로 설정됩니다(다음 화면 참조). 예를 들어 orchestrator는 기본으로 DC/OS로 설정됩니다. 지정하지 않는 경우 DNS 이름 접두사는 클러스터 이름에 따라 생성됩니다.

![az acs create 사용](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>기본값을 사용하는 빠른 `acs create`
기본 위치에 SSH RSA 공개 키 파일 `id_rsa.pub`가 있는 경우(또는 [OS X 및 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/linux/ssh-from-windows.md)용으로 만든 경우) 다음과 같은 명령을 사용합니다.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
SSH 공용 키가 없는 경우 다음 두 번째 명령을 사용합니다. `--generate-ssh-keys` 스위치가 있는 이 명령은 SSH 공용 키를 만듭니다.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

명령을 입력한 후 클러스터가 만들어질 때까지 10분 정도 기다립니다. 명령 출력은 마스터의 FQDN(정규화된 도메인 이름) 및 에이전트 노드 및 첫 번째 마스터에 연결하기 위한 SSH 명령을 포함합니다. 다음은 축약된 출력입니다.

![이미지 ACS create](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> [Kubernetes 연습](container-service-kubernetes-walkthrough.md)은 Kubernetes 클러스터를 만드는 기본값으로 `az acs create`를 사용하는 방법을 보여 줍니다.
>

## <a name="manage-acs-clusters"></a>ACS 클러스터 관리

추가 `az acs` 명령을 사용하여 클러스터를 관리합니다. 다음은 몇 가지 예제입니다.

### <a name="list-clusters-under-a-subscription"></a>구독 아래에 클러스터 나열

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>리소스 그룹의 클러스터 나열

```azurecli
az acs list -g acsrg1 --output table
```

![acs 목록](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>컨테이너 서비스 클러스터 세부 정보 표시

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs 표시](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>클러스터 크기 조정
에이전트 노드의 확장 및 축소가 모두 허용됩니다. `new-agent-count` 매개 변수는 ACS 클러스터의 새 에이전트 수입니다.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>컨테이너 서비스 클러스터 삭제
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
이 명령은 컨테이너 서비스를 만드는 동안 만들어진 모든 리소스(네트워크 및 저장소)를 삭제하지 않습니다. 모든 리소스를 쉽게 삭제하려면 고유한 리소스 그룹에 각 클러스터를 배포하는 것이 좋습니다. 그런 다음 클러스터가 더 이상 필요하지 않을 때 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계
이제 클러스터가 작동하기 시작했으니 연결 및 관리 정보는 다음 문서를 참조하세요.

* [Azure 컨테이너 서비스 클러스터에 연결](container-service-connect.md)
* [Azure 컨테이너 서비스 및 DC/OS로 작업](container-service-mesos-marathon-rest.md)
* [Azure 컨테이너 서비스 및 Docker Swarm으로 작업](container-service-docker-swarm.md)
* [Azure Container Service 및 Kubernetes로 작업](container-service-kubernetes-walkthrough.md)
