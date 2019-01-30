---
title: 개념 - AKS(Azure Kubernetes Service)의 네트워킹
description: 기본 및 고급 네트워킹, 수신 컨트롤러, 부하 분산 장치 및 고정 IP 주소를 포함하여 AKS(Azure Kubernetes Service)의 네트워킹에 대해 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 62ba98f221041d5bbf9bb095a02d052218eb0fd0
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49380888"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>애플리케이션에 대한 AKS(Azure Kubernetes Service)의 네트워크 개념

애플리케이션 개발에 대한 컨테이너 기반 마이크로 서비스 접근 방식에서 애플리케이션 구성 요소는 함께 작동하여 해당 작업을 처리해야 합니다. Kubernetes는 이 애플리케이션 통신을 사용할 수 있게 하는 다양한 리소스를 제공합니다. 애플리케이션을 내부 또는 외부에 연결하고 공개할 수 있습니다. 고가용성 애플리케이션을 빌드하기 위해 애플리케이션의 부하를 분산시킬 수 있습니다. 더 복잡한 애플리케이션에는 SSL/TLS 종료 또는 여러 구성 요소의 라우팅에 대한 수신 트래픽 구성이 필요할 수 있습니다. 보안상의 이유로 Pod, 노드 또는 서로 간의 네트워크 트래픽 흐름을 제한해야 할 수도 있습니다.

이 문서에서는 AKS에서 네트워킹을 애플리케이션에 제공하는 핵심 개념을 소개합니다.

- [서비스](#services)
- [Azure 가상 네트워크](#azure-virtual-networks)
- [수신 컨트롤러](#ingress-controllers)
- [네트워크 정책](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes 기본 사항

애플리케이션에 액세스하거나 애플리케이션 구성 요소가 서로 통신할 수 있도록 Kubernetes에서 추상화 계층을 가상 네트워킹에 제공합니다. Kubernetes 노드는 가상 네트워크에 연결되고, Pod에 대한 인바운드 및 아웃바운드 연결을 제공할 수 있습니다. *kube-proxy* 구성 요소가 각 노드에서 실행되어 이러한 네트워크 기능을 제공합니다.

Kubernetes에서 *Services*는 IP 주소 또는 DNS 이름을 통해 특정 포트에서 직접 액세스할 수 있도록 Pod를 논리적으로 그룹화합니다. 트래픽은 *부하 분산 장치*를 사용하여 분산시킬 수도 있습니다. 더 복잡한 애플리케이션 트래픽 라우팅은 *수신 컨트롤러*를 사용하여 수행할 수도 있습니다. Pod에 대한 네트워크 트래픽의 보안 및 필터링은 Kubernetes *네트워크 정책*을 사용하여 수행할 수 있습니다.

또한 Azure 플랫폼은 AKS 클러스터에 대한 가상 네트워킹을 간소화하는 데에도 도움이 됩니다. Kubernetes 부하 분산 장치를 만들면 기본 Azure 부하 분산 장치 리소스가 만들어지고 구성됩니다. Pod에서 네트워크 포트를 열면 해당 Azure 네트워크 보안 그룹 규칙이 구성됩니다. HTTP 애플리케이션 라우팅의 경우 새 수신 경로가 구성될 때 Azure에서 *외부 DNS*를 구성할 수도 있습니다.

## <a name="services"></a>Services

애플리케이션 워크로드에 대한 네트워크 구성을 간소화하기 위해 Kubernetes는 *Service*를 사용하여 일단의 Pod를 논리적으로 그룹화하고 네트워크 연결을 제공합니다. 사용할 수 있는 Service 유형은 다음과 같습니다.

- **클러스터 IP** - AKS 클러스터 내에서 사용할 내부 IP 주소를 만듭니다. 클러스터 내의 다른 워크로드를 지원하는 내부 전용 애플리케이션에 적합합니다.

    ![AKS 클러스터의 클러스터 IP 트래픽 흐름을 보여 주는 다이어그램][aks-clusterip]

- **NodePort** - 노드 IP 주소와 포트를 사용하여 응용 프로그램에 직접 액세스할 수 있도록 포트 매핑을 기본 노드에 만듭니다.

    ![AKS 클러스터의 NodePort 트래픽 흐름을 보여 주는 다이어그램][aks-nodeport]

- **LoadBalancer** - Azure 부하 분산 장치 리소스를 만들고, 외부 IP 주소를 구성하고, 요청된 Pod를 부하 분산 장치 백 엔드 풀에 연결합니다. 고객 트래픽이 애플리케이션에 도달할 수 있도록 하기 위해 부하 분산 규칙이 원하는 포트에 만들어집니다. 

    ![AKS 클러스터의 Load Balancer 트래픽 흐름을 보여 주는 다이어그램][aks-loadbalancer]

    인바운드 트래픽을 추가로 제어하고 라우팅하려면 [수신 컨트롤러](#ingress-controllers)를 대신 사용할 수 있습니다.

- **ExternalName** - 응용 프로그램에 쉽게 액세스하기 위한 특정 DNS 항목을 만듭니다.

부하 분산 장치 및 서비스에 대한 IP 주소는 동적으로 할당하거나 사용할 기존 고정 IP 주소를 지정할 수 있습니다. 내부 및 외부 고정 IP 주소를 모두 할당할 수 있습니다. 이 기존 고정 IP 주소는 종종 DNS 항목에 연결됩니다.

*내부* 및 *외부* 부하 분산 장치를 모두 만들 수 있습니다. 내부 부하 분산 장치에는 사설 IP 주소만 할당되므로 인터넷에서 액세스할 수 없습니다.

## <a name="azure-virtual-networks"></a>Azure 가상 네트워크

AKS에서는 다음 두 가지 네트워크 모델 중 하나를 사용하는 클러스터를 배포할 수 있습니다.

- *기본* 네트워킹 - AKS 클러스터가 배포될 때 네트워크 리소스가 만들어지고 구성됩니다.
- *고급* 네트워킹 - AKS 클러스터가 기존 가상 네트워크 리소스 및 구성에 연결됩니다.

### <a name="basic-networking"></a>기본 네트워킹

*기본* 네트워킹 옵션은 AKS 클러스터 만들기에 대한 기본 구성입니다. Azure 플랫폼은 클러스터와 Pod의 네트워크 구성을 관리합니다. 기본 네트워킹은 사용자 지정 가상 네트워크 구성이 필요하지 않은 배포에 적합합니다. 기본 네트워킹에서는 AKS 클러스터에 할당된 서브넷 이름 또는 IP 주소 범위와 같은 네트워크 구성을 정의할 수 없습니다.

기본 네트워킹에 대해 구성된 AKS 클러스터의 노드에는 [kubenet][kubenet] Kubernetes 플러그 인이 사용됩니다.

기본 네트워킹에서 제공하는 기능은 다음과 같습니다.

- Azure Load Balancer를 통해 외부 또는 내부로 Kubernetes 서비스를 노출합니다.
- Pod는 공용 인터넷의 리소스에 액세스할 수 있습니다.

### <a name="advanced-networking"></a>고급 네트워킹

*고급* 네트워킹은 구성한 Azure 가상 네트워크에 Pod를 배치합니다. 이 가상 네트워크는 다른 Azure 리소스에 대한 자동 연결 및 다양한 기능 집합과의 통합을 제공합니다. 고급 네트워킹은 기존 서브넷 및 연결 사용과 같은 특정 가상 네트워크 구성이 필요한 배포에 적합합니다. 고급 네트워킹을 사용하면 이러한 서브넷 이름과 IP 주소 범위를 정의할 수 있습니다.

고급 네트워킹에 대해 구성된 AKS 클러스터의 노드에는 [Azure CNI(컨테이너 네트워킹 인터페이스)][cni-networking] Kubernetes 플러그 인이 사용됩니다.

![각 노드를 단일 Azure VNet에 연결하는 브리지가 있는 두 노드를 보여주는 다이어그램][advanced-networking-diagram]

고급 네트워킹에서 기본 네트워킹을 통해 제공하는 기능은 다음과 같습니다.

- AKS 클러스터를 기존 Azure 가상 네트워크에 배포하거나 클러스터에 대한 새 가상 네트워크와 서브넷을 만듭니다.
- 클러스터의 모든 Pod에 가상 네트워크의 IP 주소가 할당됩니다. Pod에서 클러스터의 다른 Pod 및 가상 네트워크의 다른 노드와 직접 통신할 수 있습니다.
- Pod는 ExpressRoute와 S2S(사이트 간) VPN 연결을 통한 온-프레미스 네트워크를 포함하여 피어링된 가상 네트워크의 다른 서비스에 연결할 수 있습니다. 또한 온-프레미스에서 Pod에 연결할 수도 있습니다.
- 서비스 엔드포인트를 사용하도록 설정된 서브넷의 Pod는 Azure 서비스(예: Azure Storage 및 SQL DB)에 안전하게 연결될 수 있습니다.
- UDR(사용자 정의 경로)을 만들어 트래픽을 Pod에서 네트워크 가상 어플라이언스로 라우팅할 수 있습니다.

자세한 내용은 [AKS의 고급 네트워크 구성][aks-configure-advanced-networking]을 참조하세요.

## <a name="ingress-controllers"></a>수신 컨트롤러

LoadBalancer 유형 Service를 만들면 기본 Azure 부하 분산 장치 리소스가 만들어집니다. 부하 분산 장치는 지정된 포트의 Service에 있는 Pod에 트래픽을 분산하도록 구성됩니다. LoadBalancer는 계층 4에서만 작동합니다. 즉 Service에서 실제 애플리케이션을 인식하지 못하고, 라우팅 고려 사항을 추가로 만들 수 없습니다.

*수신 컨트롤러*는 계층 7에서 작동하며, 더 지능적인 규칙을 사용하여 응용 프로그램 트래픽을 분산시킬 수 있습니다. 수신 컨트롤러의 일반적인 용도는 인바운드 URL에 따라 HTTP 트래픽을 다른 애플리케이션으로 라우팅하는 것입니다.

![AKS 클러스터의 수신 트래픽 흐름을 보여 주는 다이어그램][aks-ingress]

AKS에서는 NGINX와 같은 도구를 사용하여 수신 리소스를 만들거나 AKS HTTP 애플리케이션 라우팅 기능을 사용할 수 있습니다. AKS 클러스터에 대한 HTTP 애플리케이션 라우팅을 사용하도록 설정하면 Azure 플랫폼에서 수신 컨트롤러와 *External-DNS* 컨트롤러를 만듭니다. Kubernetes에서 새 수신 리소스가 만들어지면 필요한 DNS A 레코드가 클러스터별 DNS 영역에 만들어집니다. 자세한 내용은 [HTTP 애플리케이션 라우팅 배포][aks-http-routing]를 참조하세요.

수신의 또 다른 일반적인 기능은 SSL/TLS 종료입니다. HTTPS를 통해 액세스되는 대규모 웹 애플리케이션에서 TLS 종료는 애플리케이션 자체 내에서가 아니라 수신 리소스에서 처리할 수 있습니다. 자동 TLS 인증 생성 및 구성을 제공하기 위해 Let's Encrypt와 같은 공급자를 사용하도록 수신 리소스를 구성할 수 있습니다. Let 's Encrypt를 사용하여 NGINX 수신 컨트롤러를 구성하는 방법에 대한 자세한 내용은 [수신 및 TLS][aks-ingress-tls]를 참조하세요.

## <a name="network-security-groups"></a>네트워크 보안 그룹

네트워크 보안 그룹은 AKS 노드와 같은 VM의 트래픽을 필터링합니다. LoadBalancer와 같은 Service를 만들 때 Azure 플랫폼은 필요한 모든 네트워크 보안 그룹 규칙을 자동으로 구성합니다. AKS 클러스터의 Pod에 대한 트래픽을 필터링하는 네트워크 보안 그룹 규칙은 수동으로 구성하지 않습니다. Kubernetes Service 매니페스트의 일부로 필요한 포트 및 전달을 정의하고, Azure 플랫폼에서 적절한 규칙을 만들거나 업데이트하도록 합니다.

SSH와 같은 트래픽에 대한 기본 네트워크 보안 그룹 규칙이 있습니다. 이러한 기본 규칙은 클러스터를 관리하고 액세스 문제를 해결하기 위한 것입니다. 이러한 기본 규칙이 삭제되면 AKS 관리와 관련된 문제가 발생할 수 있으며 SLO(서비스 수준 목표)를 위반할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AKS 네트워킹을 시작하려면 [AKS 클러스터에 대한 고급 네트워킹 만들기 및 구성][aks-configure-advanced-networking]을 참조하세요.

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 액세스 및 ID][aks-concepts-identity]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 저장소][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

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
[aks-configure-advanced-networking]: configure-advanced-networking.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md