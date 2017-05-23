---
title: "Azure의 Kubernetes 클러스터 빠른 시작 | Microsoft Docs"
description: "Azure Container Service에서 Kubernetes 클러스터 배포 및 시작"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2ec155129374c03ba7e0ecaa5d2bf29a1d3111aa
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Container Service에서 Kubernetes 클러스터 시작


이 연습에서는 Azure CLI 2.0 명령을 사용하여 Azure Container Service에서 Kubernetes 클러스터를 만드는 방법을 보여줍니다. 그런 다음 `kubectl` 명령줄 도구를 사용하여 클러스터에서 컨테이너 작업을 시작할 수 있습니다.

다음 이미지는 1개의 Linux 마스터와 2개의 Linux 에이전트가 있는 Container Service 클러스터의 아키텍처를 보여 줍니다. 마스터 노드는 Kubernetes REST API를 제공합니다. 에이전트 노드는 Azure 가용성 집합에서 그룹화되고 컨테이너를 실행합니다. 모든 VM은 동일한 개인 가상 네트워크에 있으며 서로 완벽하게 액세스할 수 있습니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-walkthrough/kubernetes.png)

자세한 배경 정보는 [Azure Container Service 소개](container-service-intro.md) 및 [Kubernetes 설명서](https://kubernetes.io/docs/home/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
Azure CLI 2.0을 사용하여 Azure Container Service 클러스터를 만들려면 다음을 수행해야 합니다.
* Azure 계정([무료 평가판 받기](https://azure.microsoft.com/pricing/free-trial/))이 있어야 합니다.
* [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 설정해야 합니다.

또한 다음이 필요합니다(또는 Azure CLI를 사용하여 클러스터 배포 중에 자동으로 생성할 수 있음).

* **SSH RSA 공용 키**: SSH(보안 셸) RSA 키를 미리 만들려면 [macOS 및 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/linux/ssh-from-windows.md) 지침을 참조하세요. 

* **서비스 주체 클라이언트 ID 및 비밀**: Azure Active Directory 서비스 주체를 만드는 단계 및 추가 정보는 [Kubernetes 클러스터에 대한 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.

 이 문서의 명령 예제에서는 SSH 키 및 서비스 주체를 자동으로 생성합니다.

## <a name="create-your-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

다음은 Azure CLI 2.0을 사용하여 클러스터를 만드는 간단한 bash 셸 명령입니다. 

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
클러스터를 만들려면 Azure Container Service를 [사용할 수 있는](https://azure.microsoft.com/regions/services/) 위치에 리소스 그룹을 먼저 만들어야 합니다. 다음과 유사한 명령을 실행합니다.

```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>클러스터 만들기
`--orchestrator-type=kubernetes`에서 `az acs create` 명령을 사용하여 리소스 그룹에서 Kubernetes 클러스터를 만듭니다. 명령 구문의 경우 `az acs create` [도움말](/cli/azure/acs#create)을 참조하세요.

이 버전의 명령은 Kubernetes 클러스터에 대한 SSH RSA 키 및 서비스 주체를 자동으로 생성합니다.



```azurecli
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX --generate-ssh-keys
```

몇 분 후 명령이 완료되면 Kubernetes 클러스터가 작동해야 합니다.

> [!IMPORTANT]
> 계정에 Azure AD 서비스 주체를 만들 수 있는 권한이 없으면 명령은 `Insufficient privileges to complete the operation.`과 유사한 오류를 생성합니다. 자세한 내용은 [Kubernetes 클러스터의 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.
> 



### <a name="connect-to-the-cluster"></a>클러스터에 연결

클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)을 사용합니다. 

`kubectl`이 아직 설치되지 않은 경우 `az acs kubernetes install-cli`를 사용하여 설치할 수 있습니다. ([Kubernetes 사이트](https://kubernetes.io/docs/tasks/kubectl/install/)에서 다운로드할 수도 있습니다.)

```azurecli
sudo az acs kubernetes install-cli
```

> [!TIP]
> 기본적으로 이 명령은 `kubectl` 이진 파일을 Linux 또는 macOS 시스템의 `/usr/local/bin/kubectl`이나 Windows의 `C:\Program Files (x86)\kubectl.exe`에 설치합니다. 다른 설치 경로를 지정하려면 `--install-location` 매개 변수를 사용합니다.
>
> `kubectl`이 설치된 후에는 시스템 경로에서 해당 디렉터리를 확인하거나 해당 경로에 추가합니다. 
>


그런 후 다음 명령을 실행하여 마스터 Kubernetes 클러스터 구성을 `~/.kube/config` 파일에 다운로드합니다.

```azurecli
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

`kubectl`을 설치하고 구성하는 추가 옵션은 [Azure Container Service 클러스터에 연결](container-service-connect.md)을 참조하세요.

이제 컴퓨터에서 클러스터에 액세스할 준비가 되었습니다. 다음을 실행해 보세요.

```bash
kubectl get nodes
```

클러스터의 컴퓨터 목록이 표시되는지 확인하세요.

## <a name="create-your-first-kubernetes-service"></a>첫 번째 Kubernetes 서비스 만들기

이 연습을 완료하면 다음 방법을 알 수 있게 됩니다.
* Docker 응용 프로그램 배포 및 전 세계에 공개
* `kubectl exec`를 사용하여 컨테이너에서 명령 실행 
* Kubernetes 대시보드에 액세스

### <a name="start-a-container"></a>컨테이너 시작
다음을 실행하면 컨테이너(이 경우 Nginx 웹 서버)를 실행할 수 있습니다.

```bash
kubectl run nginx --image nginx
```

이 명령은 노드 중 하나에 있는 Pod에서 Nginx Docker 컨테이너를 시작합니다.

실행 중인 컨테이너를 보려면 다음을 실행하세요.

```bash
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>전 세계에 서비스 공개
전 세계에 서비스를 공개하려면 `LoadBalancer` 유형의 Kubernetes `Service`를 만듭니다.

```bash
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

이 명령으로 Kubernetes에서 공용 IP 주소를 사용하여 Azure Load Balancer 규칙이 생성됩니다. 변경 내용이 부하 분산 장치로 전파되는 데 몇 분 정도 걸립니다. 자세한 내용은 [Azure Container Service의 Kubernetes 클러스터에서 컨테이너 부하 분산](container-service-kubernetes-load-balancing.md)을 참조하세요.

다음 명령을 실행하여 `pending`에서 외부 IP 주소를 표시하도록 서비스가 변경되는 것을 확인합니다.

```bash
watch 'kubectl get svc'
```

  ![pending에서 외부 IP 주소로 전환을 확인하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

외부 IP 주소가 표시되면 브라우저에서 해당 IP로 이동할 수 있습니다.

  ![Nginx로 이동하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes UI 찾아보기
Kubernetes 웹 인터페이스를 보려면 다음을 사용할 수 있습니다.

```bash
kubectl proxy
```
이 명령은 [http://localhost:8001/ui](http://localhost:8001/ui)에서 실행 중인 Kubernetes 웹 UI를 보는 데 사용할 수 있는 localhost에서 인증된 프록시를 실행합니다. 자세한 내용은 [Azure Container Service에서 Kubernetes 웹 UI 사용](container-service-kubernetes-ui.md)을 참조하세요.

![Kubernetes 대시보드 이미지](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>컨테이너 내 원격 세션
Kubernetes를 사용하면 클러스터에서 실행 중인 원격 Docker 컨테이너에서 명령을 실행할 수 있습니다.

```bash
# Get the name of your nginx pods
kubectl get pods
```

pod 이름을 사용하여 pod에서 원격 명령을 실행할 수 있습니다. 예:

```bash
kubectl exec <pod name> date
```

`-it` 플래그를 사용하여 완전한 대화형 세션을 가져올 수도 있습니다.

```bash
kubectl exec <pod name> -it bash
```

![컨테이너 내 원격 세션](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터로 더 많은 작업을 하려면 다음 리소스를 참조하세요.

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 컨테이너화된 응용프로그램을 배포, 크기 조정, 업데이트 및 디버그하는 방법을 보여 줍니다.
* [Kubernetes 사용자 가이드](http://kubernetes.io/docs/user-guide/) - 기존 Kubernetes 클러스터에서 실행되는 프로그램에 대한 정보를 제공합니다.
* [Kubernetes 예제](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes로 실제 응용 프로그램을 실행하는 방법에 대한 예제를 제공합니다.

