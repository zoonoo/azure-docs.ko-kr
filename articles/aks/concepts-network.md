---
title: 개념 - AKS(Azure Kubernetes Service)의 네트워킹
description: kubenet 및 Azure CNI 네트워킹, 수신 컨트롤러, 부하 분산 장치 및 고정 IP 주소를 포함하여 AKS(Azure Kubernetes Service)의 네트워킹에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: 459c11448280b63bafdfd54c13a6cad5983ef1b5
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615887"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>애플리케이션에 대한 AKS(Azure Kubernetes Service)의 네트워크 개념

애플리케이션 개발에 대한 컨테이너 기반 마이크로 서비스 접근 방식에서 애플리케이션 구성 요소는 함께 작동하여 해당 작업을 처리해야 합니다. Kubernetes는 이 애플리케이션 통신을 사용할 수 있게 하는 다양한 리소스를 제공합니다. 애플리케이션을 내부 또는 외부에 연결하고 공개할 수 있습니다. 고가용성 애플리케이션을 빌드하기 위해 애플리케이션의 부하를 분산시킬 수 있습니다. 더 복잡한 애플리케이션에는 SSL/TLS 종료 또는 여러 구성 요소의 라우팅에 대한 수신 트래픽 구성이 필요할 수 있습니다. 보안상의 이유로 Pod, 노드 또는 서로 간의 네트워크 트래픽 흐름을 제한해야 할 수도 있습니다.

이 문서에서는 AKS에서 네트워킹을 애플리케이션에 제공하는 핵심 개념을 소개합니다.

- [Services](#services)
- [Azure 가상 네트워크](#azure-virtual-networks)
- [수신 컨트롤러](#ingress-controllers)
- [네트워크 정책](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes 기본 사항

애플리케이션에 액세스하거나 애플리케이션 구성 요소가 서로 통신할 수 있도록 Kubernetes에서 추상화 계층을 가상 네트워킹에 제공합니다. Kubernetes 노드는 가상 네트워크에 연결되고, Pod에 대한 인바운드 및 아웃바운드 연결을 제공할 수 있습니다. *kube-proxy* 구성 요소가 각 노드에서 실행되어 이러한 네트워크 기능을 제공합니다.

Kubernetes에서 *Services*는 IP 주소 또는 DNS 이름을 통해 특정 포트에서 직접 액세스할 수 있도록 Pod를 논리적으로 그룹화합니다. 트래픽은 *부하 분산 장치*를 사용하여 분산시킬 수도 있습니다. 더 복잡한 애플리케이션 트래픽 라우팅은 *수신 컨트롤러*를 사용하여 수행할 수도 있습니다. pod에 대한 네트워크 트래픽 필터링과 보안은 Kubernetes *네트워크 정책*(AKS의 프리뷰)으로 가능합니다.

또한 Azure 플랫폼은 AKS 클러스터에 대한 가상 네트워킹 간소화에도 도움이 됩니다. Kubernetes 부하 분산 장치를 만들면 기본 Azure 부하 분산 장치 리소스가 만들어지고 구성됩니다. Pod에 네트워크 포트를 열면 해당 Azure 네트워크 보안 그룹 규칙이 구성됩니다. HTTP 애플리케이션 라우팅의 경우 새 수신 경로가 구성될 때 Azure에서 *외부 DNS*를 구성할 수도 있습니다.

## <a name="services"></a>서비스

애플리케이션 워크로드에 대한 네트워크 구성을 간소화하기 위해 Kubernetes는 *Service*를 사용하여 일련의 Pod를 논리적으로 그룹화하고 네트워크 연결을 제공합니다. 사용할 수 있는 Service 유형은 다음과 같습니다.

- **클러스터 IP** - AKS 클러스터 내에서 사용할 내부 IP 주소를 만듭니다. 클러스터 내의 다른 워크로드를 지원하는 내부 전용 애플리케이션에 적합합니다.

    ![AKS 클러스터의 클러스터 IP 트래픽 흐름을 보여 주는 다이어그램][aks-clusterip]

- **NodePort** - 노드 IP 주소와 포트를 사용하여 애플리케이션에 직접 액세스할 수 있도록 포트 매핑을 기본 노드에 만듭니다.

    ![AKS 클러스터의 NodePort 트래픽 흐름을 보여 주는 다이어그램][aks-nodeport]

- **LoadBalancer** - Azure 부하 분산 장치 리소스를 만들고, 외부 IP 주소를 구성하고, 요청된 Pod를 부하 분산 장치 백 엔드 풀에 연결합니다. 고객 트래픽이 애플리케이션에 도달할 수 있도록 하기 위해 부하 분산 규칙이 원하는 포트에 만들어집니다. 

    ![AKS 클러스터의 Load Balancer 트래픽 흐름을 보여 주는 다이어그램][aks-loadbalancer]

    인바운드 트래픽을 추가로 제어하고 라우팅하려면 [수신 컨트롤러](#ingress-controllers)를 대신 사용할 수 있습니다.

- **ExternalName** - 애플리케이션에 쉽게 액세스하기 위한 특정 DNS 항목을 만듭니다.

부하 분산 장치 및 서비스에 대한 IP 주소는 동적으로 할당하거나 사용할 기존 고정 IP 주소를 지정할 수 있습니다. 내부 및 외부 고정 IP 주소를 모두 할당할 수 있습니다. 이 기존 고정 IP 주소는 종종 DNS 항목에 연결됩니다.

*내부* 및 *외부* 부하 분산 장치를 모두 만들 수 있습니다. 내부 부하 분산 장치에는 사설 IP 주소만 할당되므로 인터넷에서 액세스할 수 없습니다.

## <a name="azure-virtual-networks"></a>Azure 가상 네트워크

AKS에서는 다음 두 가지 네트워크 모델 중 하나를 사용하는 클러스터를 배포할 수 있습니다.

- *Kubenet* 네트워킹 - 네트워크 리소스는 일반적으로 AKS 클러스터가 배포될 때 만들어지고 구성됩니다.
- *Azure CNI(컨테이너 네트워킹 인터페이스)* 네트워킹 - AKS 클러스터가 기존 가상 네트워크 리소스 및 구성에 연결됩니다.

### <a name="kubenet-basic-networking"></a>Kubenet(기본) 네트워킹

*kubenet* 네트워킹 옵션은 AKS 클러스터 만들기에 대한 기본 구성입니다. *kubenet*을 사용하면 노드는 Azure Virtual Network 서브넷의 IP 주소를 얻습니다. Pod는 논리적으로 다른 주소 공간에서 Azure Virtual Network 노드 서브넷에 대한 IP 주소를 받습니다. 그런 후에 NAT(Network Address Translation)는 Pod가 Azure Virtual Network의 리소스에 연결할 수 있도록 구성됩니다. 트래픽의 원본 IP 주소는 노드의 기본 IP 주소로 NAT됩니다.

노드를 사용 합니다 [kubenet][kubenet] Kubernetes 플러그 인입니다. Azure 플랫폼에서 가상 네트워크를 만들고 구성하거나 기존 가상 네트워크 서브넷에 AKS 클러스터를 배포하도록 선택할 수 있습니다. 마찬가지로 노드만 라우팅할 수 있는 IP 주소를 받고 pod NAT를 사용 하 여 AKS 클러스터 외부 다른 리소스와 통신할 수 있습니다. 이 방법을 사용하면 네트워크 공간에서 Pod가 사용하도록 예약해야 하는 IP 주소의 수가 크게 줄어듭니다.

자세한 내용은 [kubenet AKS 클러스터에 대 한 네트워킹 구성][aks-configure-kubenet-networking]합니다.

### <a name="azure-cni-advanced-networking"></a>Azure CNI(고급) 네트워킹

Azure CNI를 사용하면 모든 Pod가 서브넷에서 IP 주소를 가져오고 직접 액세스가 가능합니다. 이러한 IP 주소는 네트워크 공간에서 고유해야 하며 미리 계획되어야 합니다. 각 노드에는 지원하는 최대 Pod 수에 대한 구성 매개 변수가 있습니다. 그러면 노드당 동일한 IP 주소 수가 해당 노드에 대해 미리 예약됩니다. 이 접근 방식에서는 계획 수립에 응용 프로그램 수요가 증가 함에 따라 더 큰 서브넷에서 클러스터를 다시 빌드해야 또는 IP 주소 소모 그렇지 않은 경우 발생할 수 있습니다.

노드를 사용 합니다 [Azure 네트워킹 인터페이스 CNI (컨테이너)][cni-networking] Kubernetes 플러그 인입니다.

![각 노드를 단일 Azure VNet에 연결하는 브리지가 있는 두 노드를 보여주는 다이어그램][advanced-networking-diagram]

자세한 내용은 [AKS 클러스터에 대 한 Azure CNI 구성][aks-configure-advanced-networking]합니다.

### <a name="compare-network-models"></a>네트워크 모델 비교

Kubenet와 Azure CNI AKS 클러스터에 대 한 네트워크 연결을 제공합니다. 그러나 각 장단점 됩니다. 높은 수준에서는 다음 사항을 고려해 야 합니다.

* **kubenet**
    * IP 주소 공간을 절약합니다.
    * 클러스터 외부에서 pod에 연결할 Kubernetes 내부 또는 외부 부하 분산 장치를 사용 합니다.
    * 수동으로 관리 하 고 사용자 정의 경로 (Udr)를 유지 관리 해야 합니다.
    * 클러스터당 400 노드의 최대 수입니다.
* **Azure CNI**
    * Pod 전체 가상 네트워크 연결 및 클러스터 외부에서 직접 연결할 수 있습니다.
    * IP 주소 공간이 필요합니다.

다음과 같은 동작 차이가 kubenet 및 Azure CNI 간에 존재 합니다.

| 기능                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| 기존 또는 새 가상 네트워크에서 클러스터 배포                                            | 지원-Udr 수동으로 적용 합니다. | 지원됨 |
| Pod pod 연결                                                                         | 지원됨 | 지원됨 |
| Pod VM 연결; 동일한 가상 네트워크의 VM                                          | Pod에서 시작 하는 경우 작동 | 두 가지 방식으로 작동합니다. |
| Pod VM 연결; 피어 링 된 가상 네트워크의 VM                                            | Pod에서 시작 하는 경우 작동 | 두 가지 방식으로 작동합니다. |
| VPN 또는 Expressroute를 사용 하 여 온-프레미스 액세스                                                | Pod에서 시작 하는 경우 작동 | 두 가지 방식으로 작동합니다. |
| 서비스 끝점에서 보호 된 리소스에 대 한 액세스                                             | 지원됨 | 지원됨 |
| 부하 분산 장치 서비스, 응용 프로그램 게이트웨이 또는 수신 컨트롤러를 사용 하 여 Kubernetes 서비스를 노출 합니다. | 지원됨 | 지원됨 |
| 기본 Azure DNS 및 Private Zones                                                          | 지원됨 | 지원됨 |

### <a name="support-scope-between-network-models"></a>네트워크 모델 사이의 범위를 지원 합니다.

사용할 네트워크 모델에 관계 없이 kubenet와 Azure CNI 다음 방법 중 하나에 배포할 수 있습니다.

* 자동으로 Azure 플랫폼 만들고 AKS 클러스터를 만들 때 가상 네트워크 리소스를 구성할 수 있습니다.
* 수동으로 만들고 및 가상 네트워크 리소스를 구성 하 AKS 클러스터를 만들 때 해당 리소스에 연결 합니다.

Kubenet와 Azure CNI 사용 하 여 서비스 끝점 또는 Udr과 같은 기능을 지원 하지만 합니다 [AKS에 대 한 지원 정책][support-policies] 가능 내용을 정의 합니다. 예를 들어:

* AKS 클러스터에 대 한 가상 네트워크 리소스를 수동으로 만들어야 하는 경우 사용자 고유의 Udr 또는 서비스 끝점을 구성 하는 경우를 지원 됩니다.
* Azure 플랫폼에서 자동으로 AKS 클러스터에 대 한 가상 네트워크 리소스를 만들 경우 사용자 고유의 Udr 또는 서비스 끝점을 구성 하려면 이러한 AKS-관리 되는 리소스를 수동으로 변경 하려면 지원 되지 않습니다.

## <a name="ingress-controllers"></a>인그레스(Ingress) 컨트롤러

LoadBalancer 유형 Service를 만들면 기본 Azure 부하 분산 장치 리소스가 만들어집니다. 부하 분산 장치는 지정된 포트의 Service에 있는 Pod에 트래픽을 분산하도록 구성됩니다. LoadBalancer는 4번째 계층에서만 작동합니다. 즉 Service에서 실제 애플리케이션을 인식하지 못하고, 라우팅 고려 사항을 추가로 만들 수 없습니다.

*인그레스(Ingress) 컨트롤러*는 7계층에서 작동하며, 더 지능적인 규칙을 사용하여 애플리케이션 트래픽을 분산시킬 수 있습니다. 인그레스 컨트롤러의 일반적인 용도는 인바운드 URL에 따라 HTTP 트래픽을 다른 애플리케이션으로 라우팅하는 것입니다.

![AKS 클러스터의 수신 트래픽 흐름을 보여 주는 다이어그램][aks-ingress]

AKS에서는 NGINX와 같은 도구를 사용하여 인그레스 리소스를 만들거나 AKS HTTP 애플리케이션 라우팅 기능을 사용할 수 있습니다. AKS 클러스터에 대한 HTTP 애플리케이션 라우팅을 사용하도록 설정하면 Azure 플랫폼에서 인그레스 컨트롤러와 *External-DNS* 컨트롤러를 만듭니다. Kubernetes에서 새 인그레스 리소스가 만들어지면 필요한 DNS A 레코드가 클러스터별 DNS 영역에 만들어집니다. 자세한 내용은 [HTTP 응용 프로그램 라우팅 배포][aks-http-routing]합니다.

수신의 또 다른 일반적인 기능은 SSL/TLS 종료입니다. HTTPS를 통해 액세스되는 대규모 웹 애플리케이션에서 TLS 종료는 애플리케이션 자체 내에서가 아니라 수신 리소스에서 처리할 수 있습니다. 자동 TLS 인증 생성 및 구성을 제공하기 위해 Let's Encrypt와 같은 공급자를 사용하도록 수신 리소스를 구성할 수 있습니다. Let 's Encrypt 사용 하 여 NGINX 수신 컨트롤러를 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [수신 및 TLS][aks-ingress-tls]합니다.

AKS 클러스터에서 컨테이너에 대한 요청에 클라이언트 원본 IP를 유지하기 위해 인그레스 컨트롤러를 구성할 수도 있습니다. 클라이언트의 요청을 인그레스 컨트롤러를 통해 AKS 클러스터의 컨테이너에 라우팅되면 해당 요청의 원본 IP는 대상 컨테이너에 사용할 수 없습니다. *클라이언트 원본 IP 유지*를 사용하도록 설정한 경우, 클라이언트에 대한 원본 IP는 *X-Forwarded-For* 아래에 있는 요청 헤더에서 사용할 수 있습니다. 인그레스 컨트롤러에서 클라이언트 원본 IP 유지를 사용하고 있는 경우, SSL pass-through 옵션을 사용할 수 없습니다. 클라이언트 원본 IP 유지 및 SSL pass-through는 *LoadBalancer* 유형과 같은 다른 서비스들과 함께 사용할 수 있습니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹

네트워크 보안 그룹은 AKS 노드와 같은 VM의 트래픽을 필터링합니다. LoadBalancer와 같은 Service를 만들 때 Azure 플랫폼은 필요한 모든 네트워크 보안 그룹 규칙을 자동으로 구성합니다. AKS 클러스터의 pod에 대한 트래픽을 필터링하는 네트워크 보안 그룹 규칙은 수동으로 구성하지 않습니다. Kubernetes Service 매니페스트의 일부로 필요한 포트 및 전달을 정의하고, Azure 플랫폼에서 적절한 규칙을 만들거나 업데이트하도록 합니다. 다음 섹션에 설명된 대로 pod에 트래픽 필터 규칙을 자동으로 적용하는 네트워크 정책을 사용할 수도 있습니다.

## <a name="network-policies"></a>네트워크 정책

기본적으로 AKS 클러스터의 모든 pod는 트래픽을 제한 없이 송수신할 수 있습니다. 보안 향상을 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수도 있습니다. 보통 백 엔드 애플리케이션은 필요한 프런트 엔드 서비스에만 제공되고, 데이터베이스 구성 요소는 연결된 애플리케이션 계층에서만 액세스할 수 있습니다.

네트워크 정책 기능은 Kubernetes pod 간의 트래픽 흐름을 제어할 수 있도록 AKS에서 사용할 수 있습니다. 할당된 레이블, 네임스페이스 또는 트래픽 포트와 같은 설정에 따라 트래픽을 허용하거나 거부하도록 선택할 수 있습니다. 네트워크 보안 그룹은 Pod가 아닌 AKS 노드에 더 적합합니다. 네트워크 정책을 사용하는 것은 트래픽 흐름을 제어하는 데 있어 더 적합한 클라우드 네이티브 방식입니다. Pod는 AKS 클러스터에서 동적으로 생성되므로 필요한 네트워크 정책을 자동으로 적용할 수 있습니다.

자세한 내용은 [Azure Kubernetes Service (AKS)에서 네트워크 정책을 사용 하 여 pod 간에 트래픽을 보호][use-network-policies]합니다.

## <a name="next-steps"></a>다음 단계

AKS 네트워킹을 사용 하 여 시작, 만들기 및 사용 하 여 사용자 고유의 IP 주소 범위를 사용 하 여 AKS 클러스터를 구성 하려면 [kubenet][aks-configure-kubenet-networking] or [Azure CNI][aks-configure-advanced-networking]합니다.

관련된 모범 사례를 참조 하세요 [네트워크 연결 및 AKS에서 보안에 대 한 유용한][operator-best-practices-network]합니다.

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes AKS 클러스터 / 및 워크 로드][aks-concepts-clusters-workloads]
- [Kubernetes AKS 액세스 및 id][aks-concepts-identity]
- [Kubernetes / AKS 보안][aks-concepts-security]
- [Kubernetes / AKS 저장소][aks-concepts-storage]
- [Kubernetes AKS 소수][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ingress.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
