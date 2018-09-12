---
title: AKS(Azure Kubernetes Service) 클러스터의 송신 트래픽 허용 목록
description: AKS(Azure Kubernetes Service) 클러스터의 송신 트래픽 허용 목록
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/23/2018
ms.author: iainfou
ms.openlocfilehash: e2793a72fcbc20b79bdd564e331426fedf1ae34b
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347803"
---
# <a name="azure-kubernetes-service-aks-egress"></a>AKS(Azure Kubernetes Service) 송신

기본적으로 AKS(Azure Kubernetes Service) 클러스터의 송신 주소는 임의로 할당됩니다. 이러한 구성은 외부 서비스에 액세스하기 위한 IP 주소를 식별해야 하는 경우 이상적이지 않습니다. 이 문서에서는 AKS 클러스터에서 정적으로 할당되는 송신 IP 주소를 만들고 유지 관리하는 방법을 자세히 설명합니다.

## <a name="egress-overview"></a>송신 개요

AKS 클러스터의 아웃바운드 트래픽은 [여기][outbound-connections]에 설명된 Azure Load Balancer 규칙을 따릅니다. `LoadBalancer` 형식의 첫 번째 Kubernetes 서비스가 만들어지기 전에는 에이전트 노드가 Azure Load Balancer 풀에 속하지 않습니다. 이 구성에서는 노드에 인스턴스 수준 공용 IP 주소가 없습니다. Azure에서 아웃바운드 흐름이 구성할 수 없거나 결정적이지 않은 공용 원본 IP 주소로 변환됩니다.

`LoadBalancer` 형식의 Kubernetes 서비스가 만들어지면 에이전트 노드가 Azure Load Balancer 풀에 추가됩니다. 아웃바운드 흐름은 Azure에서 부하 분산 장치에 구성된 첫 번째 공용 IP 주소로 변환됩니다.

## <a name="create-a-static-public-ip"></a>고정 공용 IP 만들기

임의 IP 주소가 사용되는 것을 막으려면 정적 IP 주소를 만들어서 부하 분산 장치에서 사용되도록 합니다. IP 주소는 AKS **노드** 리소스 그룹에 만들어야 합니다.

[az resource show][az-resource-show] 명령으로 리소스 그룹 이름을 가져옵니다. 리소스 그룹 이름 및 클러스터 이름을 환경에 맞게 업데이트합니다.

```
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

다음으로 [az network public-ip create][public-ip-create] 명령을 사용하여 정적 공용 IP 주소를 만듭니다. 마지막 단계에서 수집한 이름과 일치하도록 리소스 그룹 이름을 업데이트합니다.

```console
$ az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --allocation-method static --query publicIp.ipAddress -o table

Result
-------------
23.101.128.81
```

## <a name="create-a-service-with-the-static-ip"></a>정적 IP로 서비스 만들기

이제 IP 주소가 있으니 `LoadBalancer` 형식의 Kubernetes 서비스를 만들어서 이 서비스에 IP 주소를 할당합니다.

파일 `egress-service.yaml`을 만들고 다음 YAML에 복사합니다. 환경에 맞게 IP 주소를 업데이트합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-egress
spec:
  loadBalancerIP: 23.101.128.81
  type: LoadBalancer
  ports:
  - port: 8080
```

`kubectl apply` 명령을 사용하여 서비스와 배포를 만듭니다.

```console
$ kubectl apply -f egress-service.yaml

service "aks-egress" created
```

이 서비스를 만들면 Azure Load Balancer에 새 프런트 엔드 IP가 구성됩니다. 다른 IP를 구성하지 않은 경우에는 **모든** 송신 트래픽에 이 주소가 사용되어야 합니다. Azure Load Balancer에 여러 주소가 구성되어 있는 경우에는 Load Balancer의 첫 번째 IP가 송신에 사용됩니다.

## <a name="verify-egress-address"></a>송신 주소 확인

공용 IP 주소가 사용되는지 확인하려면 `checkip.dyndns.org`와 같은 서비스를 사용합니다.

시작하고 포드에 연결:

```console
$ kubectl run -it --rm aks-ip --image=debian
```

필요한 경우 컨테이너에 curl 설치:

```console
$ apt-get update && apt-get install curl -y
```

Curl `checkip.dyndns.org`를 실행하면 송신 IP 주소가 반환됩니다.

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 23.101.128.81</body></html>
```

IP 주소가 Azure Load Balancer에 연결된 정적 IP 주소와 일치하는 것을 볼 수 있습니다.

## <a name="ingress-controller"></a>수신 컨트롤러

Azure Load Balancer에서 공용 IP 주소를 여러 개 유지하지 않으려면 수신 컨트롤러를 사용하는 것이 좋습니다. 수신 컨트롤러는 부하 분산, SSL/TLS 종료, URI 재작성 지원, 업스트림 SSL/TLS 암호화와 같은 이점을 제공합니다. AKS의 수신 컨트롤러에 대한 자세한 내용은 [AKS 클러스터에서 NGINX 수신 컨트롤러 구성][ingress-aks-cluster] 가이드를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서 보여준 소프트웨어에 대해 자세히 알아보세요.

- [Helm CLI][helm-cli-install]
- [NGINX 수신 컨트롤러][nginx-ingress]
- [Azure Load Balancer 아웃바운드 연결][outbound-connections]

<!-- LINKS - internal -->
[az-resource-show]: /cli/azure/resource#az-resource-show
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[create-aks-cluster]: ./kubernetes-walkthrough.md
[helm-cli-install]: ./kubernetes-helm.md#install-helm-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create

<!-- LINKS - external -->
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
