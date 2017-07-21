---
title: "빠른 시작 - Linux용 Azure Kubernetes 클러스터 | Microsoft Docs"
description: "Azure Container Service에서 Azure CLI를 사용하여 Linux 컨테이너용 Kubernetes 클러스터를 빠르게 만드는 방법에 대해 알아봅니다."
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
ms.date: 05/31/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 25043f6bf5e5ab3def8563bd2c096b79706bfec1
ms.contentlocale: ko-kr
ms.lasthandoff: 06/20/2017

---

# <a name="deploy-kubernetes-cluster-for-linux-containers"></a>Linux 컨테이너용 Kubernetes 클러스터 배포

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 [Azure Container Service](container-service-intro.md)에서 [Kubernetes](https://kubernetes.io/docs/home/) 클러스터를 배포하는 방법에 대해 자세히 설명합니다. 클러스터가 배포되면 Kubernetes `kubectl` 명령줄 도구를 사용하여 해당 클러스터에 연결하고 첫 번째 Linux 컨테이너를 배포합니다.

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 

[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-kubernetes-cluster"></a>Kubernetes 클러스터 만들기
[az acs create](/cli/azure/acs#create) 명령을 사용하여 Azure Container Service에서 Kubernetes 클러스터를 만듭니다. 

다음 예제에서는 하나의 Linux 마스터 노드와 두 개의 Linux 에이전트 노드가 있는 *myK8sCluster* 클러스터를 만들고, SSH 키가 기본 위치에 없는 경우 해당 SSH 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다. 클러스터 이름을 사용자 환경에 적절한 이름으로 업데이트합니다. 



```azurecli-interactive 
az acs create --orchestrator-type=kubernetes \
    --resource-group myResourceGroup \
    --name=myK8sCluster \
    --generate-ssh-keys 
```

몇 분 후 명령이 완료되면 배포에 대한 정보가 표시됩니다.

## <a name="install-kubectl"></a>kubectl 설치

클라이언트 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/)을 사용합니다. 

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. 로컬로 설치하려면 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) 명령을 사용하면 됩니다.

다음 Azure CLI 예제에서는 `kubectl`을 시스템에 설치합니다. macOS 또는 Linux에서 Azure CLI를 실행하는 경우 `sudo`를 사용하여 이 명령을 실행해야 할 수도 있습니다.

```azurecli-interactive 
az acs kubernetes install-cli 
```

## <a name="connect-with-kubectl"></a>Kubectl로 연결

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) 명령을 실행합니다. 다음 예제에서는 Kubernetes 클러스터에 대한 클러스터 구성을 다운로드합니다.

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group=myResourceGroup --name=myK8sCluster
```

시스템에서 클러스터에 대한 연결을 확인하려면 다음을 실행합니다.

```azurecli-interactive
kubectl get nodes
```

`kubectl`에서 마스터 및 에이전트 노드를 나열합니다.

```azurecli-interactive
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.5.3
k8s-agent-98dc3136-1    Ready                      5m        v1.5.3
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.5.3

```


## <a name="deploy-an-nginx-container"></a>NGINX 컨테이너 배포

하나 이상의 컨테이너가 포함된 Kubernetes *Pod* 내부에서 Docker 컨테이너를 실행할 수 있습니다. 

다음 명령은 노드 중 하나에 있는 Kubernetes Pod에서 NGINX Docker 컨테이너를 시작합니다. 이 경우 컨테이너는 [Docker 허브](https://hub.docker.com/_/nginx/)의 이미지에서 끌어온 NGINX 웹 서버를 실행합니다.

```azurecli-interactive
kubectl run nginx --image nginx
```
컨테이너가 실행 중인지 확인하려면 다음을 실행합니다.

```azurecli-interactive
kubectl get pods
```

## <a name="view-the-nginx-welcome-page"></a>NGINX 시작 페이지 보기
공용 IP 주소로 NGINX 서버를 전 세계에 공개하려면 다음 명령을 입력합니다.

```azurecli-interactive
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

이 명령을 사용하면 Kubernetes에서 해당 공용 IP 주소를 사용하여 서비스 및 [Azure 부하 분산 장치 규칙](container-service-kubernetes-load-balancing.md)을 만듭니다. 

다음 명령을 실행하여 서비스의 상태를 확인합니다.

```azurecli-interactive
kubectl get svc
```

처음에는 IP 주소가 `pending`으로 표시됩니다. 몇 분 후에 다음과 같이 서비스의 외부 IP 주소가 설정됩니다.
  
```azurecli-interactive
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE       
kubernetes   10.0.0.1       <none>          443/TCP        21h       
nginx        10.0.111.25    52.179.3.96     80/TCP         22m
```

선택한 웹 브라우저를 사용하여 외부 IP 주소의 기본 NGINX 시작 페이지를 볼 수 있습니다.

![Nginx로 이동하는 이미지](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


## <a name="delete-cluster"></a>클러스터 삭제
클러스터가 더 이상 필요하지 않으면 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포하고, `kubectl`로 연결하고, NGINX 컨테이너가 있는 Pod를 배포했습니다. Azure Container Service에 대해 자세히 알아보려면 Kubernetes 클러스터 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [ACS Kubernetes 클러스터 관리](./container-service-tutorial-kubernetes-prepare-app.md)

