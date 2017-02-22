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
ms.date: 11/15/2016
ms.author: anhowe
translationtype: Human Translation
ms.sourcegitcommit: 7ed8fb75f057d5a7cfde5436e72e8fec52d07156
ms.openlocfilehash: f3b2fc301bf7083f192c0ec872c4e032472eef97


---

# <a name="microsoft-azure-container-service-engine---kubernetes-walkthrough"></a>Microsoft Azure Container Service 엔진 - Kubernetes 연습

## <a name="prerequisites"></a>필수 조건
이 연습에서는 ['azure-cli' 명령줄 도구](https://github.com/azure/azure-cli#installation)가 설치되어 있고 `~/.ssh/id_rsa.pub`에서 [SSH 공용 키](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)를 만들었다고 가정합니다.

## <a name="overview"></a>개요

아래 지침에 따라 하나의 마스터 노드와 두 개의 작업자 노드를 포함한 Kubernetes 클러스터를 만듭니다.
마스터 노드는 Kubernetes REST API를 제공합니다.  작업자 노드는 Azure 가용성 집합에서 그룹화되고 컨테이너를 실행합니다. 모든 VM은 동일한 개인 VNET에 있으며 서로 완벽하게 액세스할 수 있습니다.

> [!NOTE]
> Azure Container Service의 Kubernetes 지원은 현재 미리 보기로 제공됩니다.
>

다음 이미지는&1;개의 마스터와&2;개의 에이전트가 있는 컨테이너 서비스 클러스터의 아키텍처를 보여 줍니다.

![Azure의 Kubernetes 클러스터 이미지](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="creating-your-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

### <a name="create-a-resource-group"></a>리소스 그룹 만들기
클러스터를 만들려면 먼저 특정 위치에 리소스 그룹을 만들어야 합니다. 다음을 사용하여 이 작업을 수행할 수 있습니다.
```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>클러스터 만들기
리소스 그룹이 있으면 다음을 사용하여 해당 그룹에서 클러스터를 만들 수 있습니다.
```console
DNS_PREFIX=some-unique-value
SERVICE_NAME=any-acs-service-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$SERVICE_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> azure-cli는 Linux VM에 `~/.ssh/id_rsa.pub`을 업로드합니다.
>

명령이 완료되면 Kubernetes 클러스터가 작동해야 합니다.

### <a name="configure-kubectl"></a>kubectl 구성
`kubectl`은 Kubernetes 명령줄 클라이언트입니다.  이 클라이언트를 아직 설치하지 않은 경우 다음을 사용하여 설치할 수 있습니다.

```console
az acs kubernetes install-cli
```

`kubectl`가 설치되면 아래 명령을 실행하여 마스터 Kubernetes 클러스터 구성을 ~/.kube/config 파일에 다운로드합니다.
```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$SERVICE_NAME
```

이 시점에서 컴퓨터에서 클러스터에 액세스할 수 있어야 합니다. 다음을 실행해 봅니다.
```console
kubectl get nodes
```

그리고 클러스터의 컴퓨터를 볼 수 있는지 확인합니다.

## <a name="create-your-first-kubernetes-service"></a>첫 번째 Kubernetes 서비스 만들기

이 연습을 완료하면 다음 방법을 알 수 있게 됩니다.
 * Docker 응용 프로그램 배포 및 전 세계에 공개
 * `kubectl exec`를 사용하여 컨테이너에서 명령 실행 
 * Kubernetes 대시보드에 액세스

### <a name="start-a-simple-container"></a>간단한 컨테이너 시작
다음을 실행하면 간단한 컨테이너(이 경우 `nginx` 웹 서버)를 실행할 수 있습니다.

```console
kubectl run nginx --image nginx
```

이 명령은 노드 중 하나에 있는 pod에서 nginx Docker 컨테이너를 시작합니다.

다음을 실행합니다.
```console
kubectl get pods
```

그러면 실행 중인 컨테이너를 볼 수 있습니다.

### <a name="expose-the-service-to-the-world"></a>전 세계에 서비스 공개
전 세계에 서비스를 공개하려면  다음은 `LoadBalancer` 형식의 `Service` Kubernetes를 만듭니다.

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

이제 Kubernetes는 공용 IP를 사용하여 Azure 부하 분산 장치를 만듭니다. 변경 내용이 부하 분산 장치로 전파되는 데 2-3분 정도 걸립니다.

"pending"에서 external ip 형식으로 변경된 서비스를 확인하려면 다음을 수행합니다.
```console
watch 'kubectl get svc'
```

  ![pending에서 외부 IP로의 전환을 확인하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

외부 IP가 표시되면 브라우저에서 해당 IP로 이동할 수 있습니다.

  ![nginx 찾아보기 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Kubernetes UI 찾아보기
Kubernetes 웹 인터페이스를 보려면 다음을 사용할 수 있습니다.

```console
kubectl proxy
```
이렇게 하면 [Kubernetes UI](http://localhost:8001/ui)를 볼 수 있는 localhost에서 간단한 인증된 프록시가 실행됩니다.

![Kubernetes 대시보드 이미지](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>컨테이너 내 원격 세션
Kubernetes를 사용하면 클러스터에서 실행 중인 원격 Docker 컨테이너에서 명령을 실행할 수 있습니다.

```console
# Get the name of your nginx pod
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

![podIP에 대한 curl 이미지](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)


## <a name="details"></a>세부 정보
### <a name="installing-the-kubectl-configuration-file"></a>kubectl 구성 파일 설치
`az acs kubernetes get-credentials`를 실행했을 때 원격 액세스를 위한 kube 구성 파일은 ~/.kube/config 홈 디렉터리에 저장되었습니다.

직접 다운로드해야 하는 경우 Linux 또는 OS X에서는 `ssh`, Windows에서는 `Putty`를 사용할 수 있습니다.

#### <a name="windows"></a>Windows
[putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)의 pscp를 사용합니다.  [pageant](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#key-management-and-agent-forwarding-with-windows-pageant)를 통해 인증서가 노출되었는지 확인합니다.
  ```
  # MASTERFQDN is obtained in step1
  pscp azureuser@MASTERFQDN:.kube/config .
  SET KUBECONFIG=%CD%\config
  kubectl get nodes
  ```

#### <a name="os-x-or-linux"></a>OS X 또는 Linux:
  ```
  # MASTERFQDN is obtained in step1
  scp azureuser@MASTERFQDN:.kube/config .
  export KUBECONFIG=`pwd`/config
  kubectl get nodes
  ```
## <a name="learning-more"></a>자세히 알아보기

### <a name="azure-container-service"></a>Azure Container Service

1. [Azure Container Service 설명서](https://azure.microsoft.com/en-us/documentation/services/container-service/)
2. [Azure Container Service 오픈 소스 엔진](https://github.com/azure/acs-engine)

### <a name="kubernetes-community-documentation"></a>Kubernetes 커뮤니티 설명서

1. [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) - 컨테이너화된 응용프로그램을 배포, 크기 조정, 업데이트 및 디버그하는 방법을 보여 줍니다.
2. [Kubernetes 사용자 가이드](http://kubernetes.io/docs/user-guide/) - 기존 Kubernetes 클러스터에서 실행되는 프로그램에 대한 정보를 제공합니다.
3. [Kubernetes 예제](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes로 실제 응용 프로그램을 실행하는 방법에 대한 여러 가지 예제를 제공합니다.



<!--HONumber=Jan17_HO4-->


