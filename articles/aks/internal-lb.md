---
title: AKS(Azure Kubernetes Service) 내부 부하 분산 장치 만들기
description: 내부 부하 분산 장치를 만들고 사용하여 AKS(Azure Kubernetes Service)를 통해 서비스를 노출하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/12/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 123fc08995416e0ff9c7e12a526deadc34b3a4a2
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001398"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치 사용

내부 부하 분산은 Kubernetes 서비스가 Kubernetes 클러스터와 동일한 가상 네트워크에서 실행되는 응용 프로그램에 액세스할 수 있게 합니다. 이 문서에서는 AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치를 만들고 사용하는 방법을 보여줍니다. Azure Load Balancer는 기본 및 표준이라는 두 SKU에서 사용할 수 있습니다. AKS는 기본 SKU를 사용합니다.

## <a name="create-an-internal-load-balancer"></a>내부 부하 분산 장치 만들기

내부 부하 분산 장치를 만들려면 다음 예제와 같이 *azure-load-balancer-internal* 주석 및 *LoadBalancer* 서비스 형식으로 서비스 매니페스트를 만듭니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

일단 `kubetctl apply`를 통해 배포되면 AKS 클러스터와 동일한 가상 네트워크에서 Azure 부하 분산 장치가 만들어지고 사용할 수 있게 됩니다.

![AKS 내부 부하 분산 장치 이미지](media/internal-lb/internal-lb.png)

서비스 세부 정보에서 *EXTERNAL-IP* 열의 IP 주소는 내부 부하 분산 장치의 IP 주소입니다. 다음 예제를 참조하세요.

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>IP 주소 지정

내부 부하 분산 장치를 통해 특정 IP 주소를 사용하려는 경우 부하 분산 장치 사양에 *loadBalancerIP* 속성을 추가합니다. 지정된 IP 주소는 AKS 클러스터와 동일한 서브넷에 상주해야 하며 아직 리소스에 할당되면 안 됩니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

서비스 세부 정보에서 *EXTERNAL-IP*의 IP 주소는 지정된 IP 주소를 반영합니다.

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>개인 네트워크 사용

AKS 클러스터를 만들 때 고급 네트워킹 설정을 지정할 수 있습니다. 이 방법을 통해 클러스터를 기존 Azure 가상 네트워크 및 서브넷에 배포할 수 있습니다. 한 가지 시나리오는 온-프레미스 환경에 연결된 개인 네트워크에 AKS 클러스터를 배포하여 내부에서 액세스할 수 있는 서비스만 실행하는 것입니다. 자세한 내용은 [AKS의 고급 네트워크 구성][advanced-networking]을 참조하세요.

개인 네트워크를 사용하는 AKS 클러스터에 내부 부하 분산 장치를 배포하려면 이전 단계를 변경하지 않아도 됩니다. 부하 분산 장치는 AKS 클러스터와 동일한 리소스 그룹에 만들어지지만 다음 예와 같이 개인 가상 네트워크 및 서브넷에 연결됩니다.

```
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS 클러스터의 서비스 주체에 Azure 가상 네트워크 리소스가 배포되어 있는 리소스 그룹에 대한 *네트워크 기여자* 역할을 부여해야 할 수도 있습니다. [az aks show][az-aks-show]를 사용하여 서비스 주체를 확인합니다. 예: `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`. 역할 할당을 만들려면 [az role assignment create][az-role-assignment-create] 명령을 사용합니다.

## <a name="specify-a-different-subnet"></a>다른 서브넷 지정

부하 분산 장치에 대한 서브넷을 지정하려면 서비스에 *azure-load-balancer-internal-subnet* 주석을 추가합니다. 지정된 서브넷은 AKS 클러스터와 동일한 가상 네트워크에 있어야 합니다. 배포된 경우, 부하 분산 장치 *EXTERNAL-IP* 주소가 지정된 서브넷에 속합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="delete-the-load-balancer"></a>부하 분산 장치 삭제하기

내부 부하 분산 장치를 사용하는 모든 서비스가 삭제되면 부하 분산 장치 자체도 삭제됩니다.

## <a name="next-steps"></a>다음 단계

[Kubernetes 서비스 설명서][kubernetes-services]에서 Kubernetes 서비스를 알아봅니다.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[advanced-networking]: networking-overview.md
[deploy-advanced-networking]: networking-overview.md#configure-networking---cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create