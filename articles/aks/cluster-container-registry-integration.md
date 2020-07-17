---
title: Azure Kubernetes Service와 Azure Container Registry 통합
description: ACR (Kubernetes Service) Azure Container Registry를 사용 하 여 AKS (Azure Service)를 통합 하는 방법 알아보기
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: a60f0e2f40fa1a4945198a4b5738b4f7b65b05ed
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251844"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증

AKS(Azure Kubernetes Service)에서 ACR(Azure Container Registry)을 사용할 때는 인증 메커니즘을 설정해야 합니다. 이 작업은 ACR에 필요한 권한을 부여 하 여 CLI 및 포털 환경의 일부로 구현 됩니다. 이 문서에서는 이러한 두 Azure 서비스 간의 인증을 구성 하는 예제를 제공 합니다. 

Azure CLI를 사용 하 여 몇 가지 간단한 명령에서 ACR 통합에 AKS를 설정할 수 있습니다. 이 통합은 AKS 클러스터에 연결 된 서비스 주체에 AcrPull 역할을 할당 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이러한 예제에는 다음이 필요합니다.

* **Azure 구독** 에 대 한 **소유자** 또는 **azure 계정 관리자** 역할
* Azure CLI 버전 합니다 이상

**소유자** 또는 **Azure 계정 관리자** 역할이 필요 하지 않도록 하려면 서비스 주체를 수동으로 구성 하거나 기존 서비스 주체를 사용 하 여 AKS에서 ACR을 인증할 수 있습니다. 자세한 내용은 [서비스 주체를 사용하여 ACR 인증](../container-registry/container-registry-auth-service-principal.md) 또는 [끌어오기 비밀을 사용하여 Kubernetes에서 인증](../container-registry/container-registry-auth-kubernetes.md)을 참조하세요.

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>ACR 통합을 사용 하 여 새 AKS 클러스터 만들기

AKS 클러스터를 처음 만들 때 AKS 및 ACR 통합을 설정할 수 있습니다.  AKS 클러스터가 ACR과 상호 작용할 수 있도록 Azure Active Directory **서비스 주체가** 사용 됩니다. 다음 CLI 명령을 사용 하 여 구독에서 기존 ACR에 권한을 부여 하 고 서비스 주체에 대 한 적절 한 **Acrpull** 역할을 구성할 수 있습니다. 아래 매개 변수에 대 한 유효한 값을 제공 합니다.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

또는 다음 형식의 ACR 리소스 ID를 사용 하 여 ACR 이름을 지정할 수 있습니다.

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

이 단계를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>기존 AKS 클러스터에 대 한 ACR 통합 구성

아래와 같이 **acr 이름** 또는 **acr-resource id** 에 대 한 유효한 값을 제공 하 여 기존 acr을 기존 AKS 클러스터와 통합 합니다.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

또는

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

다음을 사용 하 여 ACR과 AKS 클러스터 간의 통합을 제거할 수도 있습니다.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

또는

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>ACR & AKS 작업

### <a name="import-an-image-into-your-acr"></a>ACR로 이미지 가져오기

다음을 실행 하 여 docker 허브에서 ACR로 이미지를 가져옵니다.


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>ACR에서 AKS로 샘플 이미지 배포

적절 한 AKS 자격 증명이 있는지 확인 합니다.

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

다음을 포함 하는 **nginx** 라는 파일을 만듭니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

다음으로 AKS 클러스터에서이 배포를 실행 합니다.

```console
kubectl apply -f acr-nginx.yaml
```

다음을 실행 하 여 배포를 모니터링할 수 있습니다.

```console
kubectl get pods
```

실행 중인 pod 두 개가 있어야 합니다.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>문제 해결
* [ACR 진단](../container-registry/container-registry-diagnostics-audit-logs.md) 에 대 한 자세한 정보
* [ACR 상태](../container-registry/container-registry-check-health.md) 에 대 한 자세한 정보

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
