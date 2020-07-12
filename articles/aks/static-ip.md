---
title: 부하 분산 장치와 함께 고정 IP 사용
titleSuffix: Azure Kubernetes Service
description: 고정 IP 주소를 만들어 AKS(Azure Kubernetes Service) 부하 분산 장치에서 사용하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 3055b5d32055d0ed0e3870f16f6af95407a68cd9
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243939"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>AKS (Azure Kubernetes Service) 부하 분산 장치에 고정 공용 IP 주소 및 DNS 레이블 사용

기본적으로 AKS 클러스터에서 만드는 부하 분산 장치 리소스에 할당되는 공용 IP 주소는 해당 리소스의 수명 동안만 유효합니다. Kubernetes 서비스를 삭제하면 연결된 부하 분산 장치 및 IP 주소도 삭제됩니다. 특정 IP 주소를 할당하거나 재배포된 Kubernetes 서비스의 IP 주소를 유지하려는 경우에는 고정 공용 IP 주소를 만들어 사용할 수 있습니다.

이 문서에서는 고정 공용 IP 주소를 만들어 Kubernetes 서비스에 할당하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치되고 구성되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

이 문서에서는 표준 sku 부하 분산 장치를 사용 *하는* *표준* sku IP를 사용 하는 방법을 설명 합니다. 자세한 내용은 [Azure에서 IP 주소 형식 및 할당 메서드][ip-sku]를 참조하세요.

## <a name="create-a-static-ip-address"></a>고정 IP 주소 만들기

[Az network public ip create][az-network-public-ip-create] 명령을 사용 하 여 고정 공용 ip 주소를 만듭니다. 다음은 *Myresourcegroup* 리소스 그룹에 *MYAKSPUBLICIP* 이라는 고정 IP 리소스를 만듭니다.

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> AKS 클러스터에서 *기본* sku 부하 분산 장치를 사용 하는 경우 공용 IP를 정의할 때 *sku* 매개 변수에 대 한 *기본* 를 사용 합니다. 기본 *Sku ip만* *기본* sku 부하 분산 장치에서 작동 하며 표준 *sku ip만* *표준* sku 부하 분산 장치에서 작동 합니다. 

IP 주소는 다음 압축 예제 출력과 같이 표시 됩니다.

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

나중에 [az network public-ip list][az-network-public-ip-list] 명령을 사용하여 공용 IP 주소를 가져올 수 있습니다. 만든 노드 리소스 그룹의 이름과 공용 IP 주소를 지정한 후 다음 예제와 같이 *ipAddress*를 쿼리합니다.

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>고정 IP 주소를 사용하여 서비스 만들기

서비스를 만들기 전에 AKS 클러스터에서 사용 하는 서비스 사용자에 게 다른 리소스 그룹에 대 한 위임 된 권한이 있는지 확인 합니다. 예:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

또는 서비스 주체 대신 시스템 할당 관리 id를 사용 권한에 사용할 수 있습니다. 자세한 내용은 [관리 ID 사용](use-managed-identity.md)을 참조하세요.

고정 공용 IP 주소를 사용 하 여 *LoadBalancer* 서비스를 만들려면 `loadBalancerIP` 고정 공용 ip 주소의 속성 및 값을 yaml 매니페스트에 추가 합니다. 파일 `load-balancer-service.yaml`을 만들고 다음 YAML에 복사합니다. 이전 단계에서 만든 공용 IP 주소를 입력합니다. 또한 다음 예제에서는 주석을 *Myresourcegroup*이라는 리소스 그룹에 설정 합니다. 사용자 고유의 리소스 그룹 이름을 제공 합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

`kubectl apply` 명령을 사용하여 서비스와 배포를 만듭니다.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>서비스에 DNS 레이블 적용

서비스에서 동적 또는 고정 공용 IP 주소를 사용 하는 경우 서비스 주석을 사용 `service.beta.kubernetes.io/azure-dns-label-name` 하 여 공용 DNS 레이블을 설정할 수 있습니다. 그러면 Azure의 공용 DNS 서버 및 최상위 도메인을 사용 하 여 서비스에 대 한 정규화 된 도메인 이름이 게시 됩니다. 주석 값은 Azure 위치 내에서 고유 해야 하므로 충분히 정규화 된 레이블을 사용 하는 것이 좋습니다.   

그러면 Azure에서 `<location>.cloudapp.azure.com` 사용자가 제공한 이름에 (여기서 location은 선택한 지역)와 같은 기본 서브넷을 자동으로 추가 하 여 정규화 된 DNS 이름을 만듭니다. 예:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> 사용자의 도메인에 서비스를 게시 하려면 [Azure DNS][azure-dns-zone] 및 [외부-DNS][external-dns] 프로젝트를 참조 하세요.

## <a name="troubleshoot"></a>문제 해결

Kubernetes service 매니페스트의 *loadBalancerIP* 속성에 정의 된 고정 IP 주소가 존재 하지 않거나 노드 리소스 그룹에 만들어지지 않고 추가 위임이 구성 되지 않은 경우 부하 분산 장치 서비스를 만들지 못합니다. 문제를 해결하려면 [kubectl describe][kubectl-describe] 명령을 사용하여 서비스 만들기 이벤트를 검토합니다. 다음 예제와 같이 YAML 매니페스트에 지정된 대로 서비스의 이름을 입력합니다.

```console
kubectl describe service azure-load-balancer
```

Kubernetes 서비스 리소스에 대한 정보가 표시 됩니다. 다음 예제 출력 끝부분의 *Events*는 *사용자가 입력한 IP 주소를 찾을 수 없음*을 나타냅니다. 이러한 시나리오에서는 노드 리소스 그룹에 고정 공용 IP 주소를 만들었으며 Kubernetes 서비스 매니페스트에 지정된 IP 주소가 올바른지 확인합니다.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
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
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>다음 단계

애플리케이션에 대한 네트워크 트래픽을 추가로 제어하려는 경우에는 [수신 컨트롤러 만들기][aks-ingress-basic]를 대신 수행할 수 있습니다. [고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기][aks-static-ingress]를 수행할 수도 있습니다.

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
