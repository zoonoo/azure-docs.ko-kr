---
title: AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소를 사용합니다.
description: AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소를 사용합니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: af1dffd681eaf7b2eb90ab4657cc25f2144a48d9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423626"
---
# <a name="use-a-static-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소를 사용합니다.

AKS(Azure Kubernetes Service) 부하 분산 장치가 만들어지거나 LoadBalancer 유형의 Kubernetes 서비스가 다시 만들어지는 경우와 비슷한 상황에서는 Kubernetes 서비스의 공용 IP 주소가 변경될 수 있습니다. 이 문서에서는 Kubernetes 서비스에 대한 고정 IP 주소를 구성하는 방법을 자세히 설명합니다.

## <a name="create-static-ip-address"></a>고정 IP 주소 만들기

Kubernetes 서비스에 대한 공용 고정 IP 주소를 만듭니다. IP 주소는 AKS **노드** 리소스 그룹에 만들어야 합니다. [az resource show][az-resource-show] 명령으로 리소스 그룹 이름을 가져옵니다.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

[az network public ip create][az-network-public-ip-create] 명령을 사용하여 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static
```

IP 주소를 기록해 둡니다.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    "ipConfiguration": null,
    "ipTags": [],
    "location": "eastus",
    "name": "myAKSPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Static",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "56ec8760-a3b8-4aeb-a89d-42e68d2cbc8c",
    "sku": {
      "name": "Basic"
    },
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses",
    "zones": null
  }
````

 필요한 경우 [az network public-ip list][az-network-public-ip-list] 명령을 사용하여 주소를 검색할 수 있습니다.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv
```

```console
40.121.183.52
```

## <a name="create-service-with-ip-address"></a>IP 주소를 사용하여 서비스 만들기

고정 IP 주소가 프로비전되면 `loadBalancerIP` 속성 및 고정 IP 주소 값으로 Kubernetes 서비스를 만들 수 있습니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

## <a name="troubleshooting"></a>문제 해결

고정 IP 주소가 만들어지지 않았거나 잘못된 리소스 그룹에 만들어진 경우 서비스 만들기가 실패합니다. 문제를 해결하려면 [kubectl describe][kubectl-describe] 명령을 사용하여 서비스 만들기 이벤트로 돌아갑니다.

```azurecli-interactive
kubectl describe service azure-vote-front
```

```console
Name:                     azure-vote-front
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-vote-front
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-vote-front: user supplied IP Address 40.121.183.52 was not found
```

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-resource-show]: /cli/azure/resource#az-resource-show
