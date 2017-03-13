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
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2464901d22bb91cbf396ef60f4bda6d979b578b7
ms.openlocfilehash: 49602804cc6b4d4f98c802c1a3b651dda2634bb7
ms.lasthandoff: 03/02/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Container Service에서 Kubernetes 클러스터 시작


이 문서의 지침은 Azure CLI 2.0 명령을 사용하여 Kubernetes 클러스터를 만드는 방법을 보여줍니다. 그런 다음 `kubectl` 명령줄 도구를 사용하여 클러스터에서 컨테이너 작업을 시작할 수 있습니다.

다음 이미지는&1;개의 마스터와&2;개의 에이전트가 있는 컨테이너 서비스 클러스터의 아키텍처를 보여 줍니다. 마스터 노드는 Kubernetes REST API를 제공합니다. 에이전트 노드는 Azure 가용성 집합에서 그룹화되고 컨테이너를 실행합니다. 모든 VM은 동일한 개인 가상 네트워크에 있으며 서로 완벽하게 액세스할 수 있습니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>필수 조건
이 연습에서는[Azure CLI v. 2.0](/cli/azure/install-az-cli2)이 설치 및 설정되어 있다고 가정합니다. `~/.ssh/id_rsa.pub`에 SSH RSA 공개 키도 있어야 합니다. 준비되지 않은 경우 [OS X 및 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)의 단계를 참조하세요.






## <a name="create-your-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

다음은 Azure CLI 2.0을 사용하여 클러스터를 만드는 간단한 셸 명령입니다. 자세한 내용은 [Azure CLI 2.0을 사용하여 Azure Container Service 클러스터 만들기](container-service-create-acs-cluster-cli.md)를 참조하세요.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
클러스터를 만들려면 먼저 특정 위치에 리소스 그룹을 만들어야 합니다. 다음과 유사한 명령을 실행합니다.

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>클러스터 만들기
리소스 그룹이 있으면 해당 그룹에 클러스터를 만들 수 있습니다.

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> 배포 중에 CLI는 `~/.ssh/id_rsa.pub`를 Linux VM에 업로드합니다.
>

명령이 완료되면 Kubernetes 클러스터가 작동해야 합니다.

### <a name="connect-to-the-cluster"></a>클러스터에 연결

다음은 `kubectl`이라는 Kubernetes 명령줄 클라이언트를 사용하여 클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하는 Azure CLI 명령입니다. 자세한 내용은 [Azure Container Service 클러스터에 연결](container-service-connect.md)을 참조하세요.

`kubectl`이 아직 설치되지 않은 경우 다음을 사용하여 설치할 수 있습니다.

```console
az acs kubernetes install-cli
```

`kubectl`이 설치되면 다음 명령을 실행하여 마스터 Kubernetes 클러스터 구성을 ~/.kube/config 파일에 다운로드합니다.

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

이제 컴퓨터에서 클러스터에 액세스할 준비가 되었습니다. 다음을 실행해 보세요.
```console
kubectl get nodes
```

클러스터의 컴퓨터 목록이 표시되는지 확인하세요.

## <a name="create-your-first-kubernetes-service"></a>첫 번째 Kubernetes 서비스 만들기

이 연습을 완료하면 다음 방법을 알 수 있게 됩니다.
 * Docker 응용 프로그램 배포 및 전 세계에 공개
 * `kubectl exec`를 사용하여 컨테이너에서 명령 실행 
 * Kubernetes 대시보드에 액세스

### <a name="start-a-simple-container"></a>간단한 컨테이너 시작
다음을 실행하면 간단한 컨테이너(이 경우 Nginx 웹 서버)를 실행할 수 있습니다.

```console
kubectl run nginx --image nginx
```

이 명령은 노드 중 하나에 있는 Pod에서 Nginx Docker 컨테이너를 시작합니다.

실행 중인 컨테이너를 보려면 다음을 실행하세요.

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>전 세계에 서비스 공개
전 세계에 서비스를 공개하려면 `LoadBalancer` 유형의 Kubernetes `Service`를 만듭니다.

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

그러면 Kubernetes에서 공용 IP 주소를 사용하여 Azure Load Balancer가 생성됩니다. 변경 내용이 부하 분산 장치로 전파되는 데 몇 분 정도 걸립니다. 자세한 내용은 [Azure Container Service의 Kubernetes 클러스터에서 컨테이너 부하 분산](container-service-kubernetes-load-balancing.md)을 참조하세요.

다음 명령을 실행하여 `pending`에서 외부 IP 주소를 표시하도록 서비스가 변경되는 것을 확인합니다.

```console
watch 'kubectl get svc'
```

  ![pending에서 외부 IP 주소로 전환을 확인하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

외부 IP 주소가 표시되면 브라우저에서 해당 IP로 이동할 수 있습니다.

  ![Nginx로 이동하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes UI 찾아보기
Kubernetes 웹 인터페이스를 보려면 다음을 사용할 수 있습니다.

```console
kubectl proxy
```
이렇게 하면 [Kubernetes 웹 UI](http://localhost:8001/ui)를 보는 데 사용할 수 있는 localhost에서 간단한 인증된 프록시가 실행됩니다. 자세한 내용은 [Azure Container Service에서 Kubernetes 웹 UI 사용](container-service-kubernetes-ui.md)을 참조하세요.

![Kubernetes 대시보드 이미지](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>컨테이너 내 원격 세션
Kubernetes를 사용하면 클러스터에서 실행 중인 원격 Docker 컨테이너에서 명령을 실행할 수 있습니다.

```console
# Get the name of your nginx pods
kubectl get pods
```

pod 이름을 사용하여 pod에서 원격 명령을 실행할 수 있습니다.  예:

```console
kubectl exec nginx-701339712-retbj date
```

`-it` 플래그를 사용하여 완전한 대화형 세션을 가져올 수도 있습니다.

```console
kubectl exec nginx-701339712-retbj -it bash
```

![컨테이너 내 원격 세션](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터로 더 많은 작업을 하려면 다음 리소스를 참조하세요.

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 컨테이너화된 응용프로그램을 배포, 크기 조정, 업데이트 및 디버그하는 방법을 보여 줍니다.
* [Kubernetes 사용자 가이드](http://kubernetes.io/docs/user-guide/) - 기존 Kubernetes 클러스터에서 실행되는 프로그램에 대한 정보를 제공합니다.
* [Kubernetes 예제](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes로 실제 응용 프로그램을 실행하는 방법에 대한 예제를 제공합니다.

