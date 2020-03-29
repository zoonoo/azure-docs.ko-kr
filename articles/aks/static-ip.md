---
title: AZURE Kubernetes 서비스(AKS) 로드 밸러버와 함께 정적 IP 주소 및 DNS 레이블 사용
description: 고정 IP 주소를 만들어 AKS(Azure Kubernetes Service) 부하 분산 장치에서 사용하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 6c219976db21fb05ea1ad313b4effdf95906f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047964"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>AZURE Kubernetes 서비스(AKS) 로드 밸런서와 함께 정적 공용 IP 주소 및 DNS 레이블 사용

기본적으로 AKS 클러스터에서 만드는 부하 분산 장치 리소스에 할당되는 공용 IP 주소는 해당 리소스의 수명 동안만 유효합니다. Kubernetes 서비스를 삭제하면 연결된 부하 분산 장치 및 IP 주소도 삭제됩니다. 특정 IP 주소를 할당하거나 재배포된 Kubernetes 서비스의 IP 주소를 유지하려는 경우에는 고정 공용 IP 주소를 만들어 사용할 수 있습니다.

이 문서에서는 고정 공용 IP 주소를 만들어 Kubernetes 서비스에 할당하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure CLI 버전 2.0.59 이상설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

이 문서에서는 *표준* SKU 로드 밸레인저가 있는 *표준* SKU IP를 사용하는 것을 다룹니다. 자세한 내용은 Azure 의 [IP 주소 유형 및 할당 메서드를][ip-sku]참조하십시오.

## <a name="create-a-static-ip-address"></a>고정 IP 주소 만들기

az 네트워크 공용 IP [create][az-network-public-ip-create] 명령을 사용하여 정적 공용 IP 주소를 만듭니다. 다음은 *myResourceGroup* 리소스 그룹에 *myAKSPublicIP라는* 정적 IP 리소스를 만듭니다.

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> AKS 클러스터에서 *기본* SKU 로드 밸런서를 사용하는 경우 공용 IP를 정의할 때 *sku* 매개 변수에 *Basic을* 사용합니다. *기본* SKU IP만 *기본* SKU 로드 밸레인저와 함께 작동하며 *표준* SKU IP만 *표준* SKU 로드 밸레인저와 함께 작동합니다. 

IP 주소는 다음과 같은 압축된 예제 출력에 표시된 대로 표시됩니다.

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

서비스를 만들기 전에 AKS 클러스터에서 사용하는 서비스 주체가 다른 리소스 그룹에 권한을 위임했는지 확인합니다. 예를 들어:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

또는 서비스 주체 대신 관리되는 ID를 사용 권한에 할당된 시스템을 사용할 수 있습니다. 자세한 내용은 [관리되는 ID 사용을](use-managed-identity.md)참조하십시오.

정적 공용 IP 주소로 *LoadBalancer* 서비스를 만들려면 YAML 매니페스트에 `loadBalancerIP` 정적 공용 IP 주소의 속성 및 값을 추가합니다. 파일 `load-balancer-service.yaml`을 만들고 다음 YAML에 복사합니다. 이전 단계에서 만든 공용 IP 주소를 입력합니다. 다음 예제는 *myResourceGroup이라는*리소스 그룹에 대한 추가 도 설정합니다. 고유한 리소스 그룹 이름을 제공합니다.

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

서비스가 동적 또는 정적 공용 IP 주소를 사용하는 경우 서비스 지정을 `service.beta.kubernetes.io/azure-dns-label-name` 사용하여 공용 DNS 레이블을 설정할 수 있습니다. 이렇게 하면 Azure의 공용 DNS 서버 및 최상위 도메인을 사용하여 서비스에 대해 정규화된 도메인 이름을 게시합니다. 참조 값은 Azure 위치 내에서 고유해야 하므로 충분히 정규화된 레이블을 사용하는 것이 좋습니다.   

그러면 Azure는 기본 `<location>.cloudapp.azure.com` 서브넷(예: 위치가 선택한 지역)을 제공한 이름에 자동으로 적용하여 정규화된 DNS 이름을 만듭니다. 예를 들어:

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
> 사용자 고유의 도메인에 서비스를 게시하려면 [Azure DNS][azure-dns-zone] 및 [외부 dns][external-dns] 프로젝트를 참조하십시오.

## <a name="troubleshoot"></a>문제 해결

Kubernetes 서비스 매니페스트의 *loadBalancerIP* 속성에 정의된 정적 IP 주소가 없거나 노드 리소스 그룹에 생성되지 않았으며 추가 위임이 구성되지 않은 경우 로드 밸런서 서비스 생성에 실패합니다. 문제를 해결하려면 [kubectl describe][kubectl-describe] 명령을 사용하여 서비스 만들기 이벤트를 검토합니다. 다음 예제와 같이 YAML 매니페스트에 지정된 대로 서비스의 이름을 입력합니다.

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
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
