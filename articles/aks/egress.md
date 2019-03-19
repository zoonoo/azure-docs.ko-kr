---
title: AKS(Azure Kubernetes Service)의 송신 트래픽용 고정 IP 주소
description: AKS(Azure Kubernetes Service) 클러스터의 송신 트래픽용으로 고정 공용 IP 주소를 만들어 사용하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: 6612d801804cdd1e092b50977230f24b378e64ba
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407139"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 송신 트래픽에 고정 공용 IP 주소 사용

기본적으로 AKS(Azure Kubernetes Service) 클러스터의 송신 IP 주소는 임의로 할당됩니다. 하지만 외부 서비스 액세스용 IP 주소를 확인해야 하는 등의 경우에는 이러한 구성이 적합하지 않습니다. 대신 서비스 액세스용 허용 목록에 포함할 수 있는 고정 IP 주소를 할당해야 할 수 있습니다.

이 문서에서는 AKS 클러스터의 송신 트래픽에 사용할 고정 공용 IP 주소를 만들어 사용하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure cli 버전 2.0.59 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="egress-traffic-overview"></a>송신 트래픽 개요

AKS 클러스터의 아웃바운드 트래픽은 [Azure Load Balancer 규칙][outbound-connections]을 따릅니다. `LoadBalancer` 형식의 첫 번째 Kubernetes 서비스가 만들어지기 전에는 AKS 클러스터의 에이전트 노드가 Azure Load Balancer 풀에 포함되지 않습니다. 이 구성에서는 노드에 인스턴스 수준 공용 IP 주소가 없습니다. Azure에서 아웃바운드 흐름이 구성할 수 없거나 결정적이지 않은 공용 원본 IP 주소로 변환됩니다.

`LoadBalancer` 형식의 Kubernetes 서비스가 만들어지면 에이전트 노드가 Azure Load Balancer 풀에 추가됩니다. 아웃바운드 흐름은 Azure에서 부하 분산 장치에 구성된 첫 번째 공용 IP 주소로 변환됩니다. 이 공용 IP 주소는 해당 리소스의 수명 동안에만 유효합니다. Kubernetes LoadBalancer 서비스를 삭제하면 연결된 부하 분산 장치 및 IP 주소도 삭제됩니다. 특정 IP 주소를 할당하거나 재배포된 Kubernetes 서비스의 IP 주소를 유지하려는 경우에는 고정 공용 IP 주소를 만들어 사용할 수 있습니다.

## <a name="create-a-static-public-ip"></a>고정 공용 IP 만들기

AKS에서 사용할 고정 공용 IP 주소를 만들 때는 **노드** 리소스 그룹에서 IP 주소 리소스를 만들어야 합니다. [az aks show][az-aks-show] 명령을 사용하여 리소스 그룹 이름을 가져온 다음 `--query nodeResourceGroup` 쿼리 매개 변수를 추가합니다. 다음 예제는 *myResourceGroup* 리소스 그룹에서 AKS 클러스터 *myAKSCluster*의 노드 리소스 그룹을 가져옵니다.

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

이제 [az network public ip create][az-network-public-ip-create] 명령을 사용하여 고정 공용 IP 주소를 만듭니다. 이전 명령에서 가져온 노드 리소스 그룹 이름을 지정한 다음 IP 주소 리소스의 이름을 *myAKSPublicIP*와 같이 지정합니다.

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

다음의 축소된 예제 출력에 나와 있는 것처럼 IP 주소가 표시됩니다.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

나중에 [az network public-ip list][az-network-public-ip-list] 명령을 사용하여 공용 IP 주소를 가져올 수 있습니다. 노드 리소스 그룹의 이름을 지정한 후에 다음 예제와 같이 *ipAddress*를 쿼리합니다.

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>정적 IP로 서비스 만들기

고정 공용 IP 주소를 사용하여 서비스를 만들려면 고정 공용 IP 주소의 값과 `loadBalancerIP` 속성을 YAML 매니페스트에 추가합니다. 파일 `egress-service.yaml`을 만들고 다음 YAML에 복사합니다. 이전 단계에서 만든 공용 IP 주소를 입력합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

`kubectl apply` 명령을 사용하여 서비스와 배포를 만듭니다.

```console
kubectl apply -f egress-service.yaml
```

이 서비스는 Azure Load Balancer에서 새 프런트 엔드 IP를 구성합니다. 다른 IP를 구성하지 않은 경우에는 **모든** 송신 트래픽에 이 주소가 사용되어야 합니다. Azure Load Balancer에 여러 주소가 구성되어 있는 경우에는 Load Balancer의 첫 번째 IP가 송신에 사용됩니다.

## <a name="verify-egress-address"></a>송신 주소 확인

고정 공용 IP 주소가 사용되고 있는지 확인하려는 경우 `checkip.dyndns.org` 등의 DNS 조회 서비스를 사용할 수 있습니다.

기본 *Debian* Pod를 시작하여 해당 Pod에 연결합니다.

```console
kubectl run -it --rm aks-ip --image=debian --generator=run-pod/v1
```

컨테이너 내에서 웹 사이트에 액세스하려면 `apt-get`을 사용하여 컨테이너에 `curl`을 설치합니다.

```console
apt-get update && apt-get install curl -y
```

이제 curl을 사용하여 *checkip.dyndns.org* 사이트에 액세스합니다. 다음 예제 출력에 나와 있는 것처럼 송신 IP 주소가 표시됩니다. 이 IP 주소는 loadBalancer 서비스용으로 작성 및 정의된 고정 공용 IP 주소와 일치합니다.

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>다음 단계

Azure Load Balancer에 공용 IP 주소가 여러 개 유지되지 않도록 하려는 경우 수신 컨트롤러를 대신 사용할 수 있습니다. 수신 컨트롤러는 SSL/TLS 종료, URI 재작성 지원, 업스트림 SSL/TLS 암호화와 같은 추가적인 이점을 제공합니다. 자세한 내용은 [AKS에서 기본 수신 컨트롤러 만들기][ingress-aks-cluster]를 참조하세요.

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli