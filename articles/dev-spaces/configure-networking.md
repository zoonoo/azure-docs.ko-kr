---
title: 여러 네트워크 토폴로지에 Azure Dev Spaces에 대한 네트워킹 구성
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Azure Kubernetes Services에서 Azure Dev Spaces를 실행하기 위한 네트워킹 요구 사항을 설명합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, CNI, kubenet, SDN, 네트워크
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91970341"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>여러 네트워크 토폴로지에 Azure Dev Spaces에 대한 네트워킹 구성

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces는 기본 네트워킹 구성을 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 실행됩니다. 클러스터를 방화벽 뒤에 배치하거나, 네트워크 보안 그룹을 사용하거나, 네트워크 정책을 사용하는 등 AKS 클러스터의 네트워킹 구성을 변경하려면 Azure Dev Spaces를 실행하기 위한 추가 고려 사항을 통합해야 합니다.

![가상 네트워크 구성](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>가상 네트워크 또는 서브넷 구성

AKS 클러스터에는 AKS 클러스터의 수신 또는 송신 트래픽을 제한하는 다른 가상 네트워크 또는 서브넷 구성이 있을 수 있습니다. 예를 들어 Azure Firewall과 같은 방화벽 뒤에 클러스터를 배치하거나 네트워크 보안 그룹 또는 사용자 지정 역할을 사용하여 네트워크 트래픽을 제한할 수 있습니다. [GitHub의 Azure Dev Spaces 샘플 리포지토리][sample-repo]에서 네트워크 구성에 대한 예제를 찾을 수 있습니다.

Azure Dev Spaces는 *수신 및 송신* 네트워크 트래픽과 *수신 전용* 트래픽에 대한 특정 요구 사항이 있습니다. AKS 클러스터에 대한 트래픽을 제한하는 가상 네트워크 또는 서브넷 구성이 설정된 AKS 클러스터에서 Azure Dev Spaces를 사용하는 경우, Azure Dev Spaces가 제대로 작동하려면 다음 수신 전용 및 송수신 트래픽 요구 사항을 따라야 합니다.

### <a name="ingress-and-egress-network-traffic-requirements"></a>수신 및 송신 네트워크 트래픽 요구 사항

Azure Dev Spaces에는 다음 FQDN에 대한 수신 및 송신 트래픽이 필요합니다.

| FQDN                       | 포트       | 사용      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Azure Dev Spaces에 대한 docker 이미지 끌어오기 |
| gcr.io                     | HTTPS: 443 | Azure Dev Spaces에 대한 helm 이미지 끌어오기 |
| storage.googleapis.com     | HTTPS: 443 | Azure Dev Spaces에 대한 helm 이미지 끌어오기 |

위의 모든 FQDN 및 [Azure Dev Spaces 인프라 서비스 ][service-tags] 간의 네트워크 트래픽을 허용하도록 방화벽 또는 보안 구성을 업데이트합니다. 예를 들어 방화벽을 사용하여 네트워크를 보호하는 경우, 위의 FQDN을 방화벽의 애플리케이션 규칙에 추가하고 Azure Dev Spaces 서비스 태그도 [방화벽에 추가][firewall-service-tags]해야 합니다. 이러한 도메인에서 들어오고 나가는 트래픽을 허용하려면 방화벽에 대한 이러한 업데이트가 모두 필요합니다.

### <a name="ingress-only-network-traffic-requirements"></a>수신 전용 네트워크 트래픽 요구 사항

Azure Dev Spaces는 Kubernetes 네임스페이스 수준의 라우팅뿐만 아니라 자체 FQDN을 사용하여 서비스에 대한 공용 액세스를 제공합니다. 이러한 두 기능이 모두 작동하려면 클러스터에서 Azure Dev Spaces 수신 컨트롤러의 외부 IP 주소에 대한 공개 수신을 허용하도록 방화벽 또는 네트워크 구성을 업데이트합니다. 또는 [내부 부하 분산 장치][aks-internal-lb]를 만들고 방화벽의 공용 IP를 내부 부하 분산 장치의 IP로 변환하는 NAT 규칙을 방화벽에 추가할 수 있습니다. 또한 [traefik][traefik-ingress] 또는 [NGINX][nginx-ingress]를 사용하여 사용자 지정 수신 컨트롤러를 만들 수도 있습니다.

## <a name="aks-cluster-network-requirements"></a>AKS 클러스터 네트워크 요구 사항

AKS를 사용하면 [네트워크 정책][aks-network-policies]을 사용하여 클러스터에서 Pod 간의 수신 및 송신 트래픽 뿐만 아니라 Pod의 송신 트래픽도 제어할 수 있습니다. Azure Dev Spaces는 *수신 및 송신* 네트워크 트래픽과 *수신 전용* 트래픽에 대한 특정 요구 사항이 있습니다. AKS 네트워크 정책이 설정된 AKS 클러스터에서 Azure Dev Spaces를 사용하는 경우 Azure Dev Spaces가 제대로 작동하려면 다음 수신 전용 및 송수신 트래픽 요구 사항을 따라야 합니다.

### <a name="ingress-and-egress-network-traffic-requirements"></a>수신 및 송신 네트워크 트래픽 요구 사항

Azure Dev Spaces를 사용하면 클러스터의 개발 공간에 있는 Pod와 디버깅을 위해 직접 통신할 수 있습니다. 이 기능이 작동하도록 하려면 [지역에 따라 달라지는][service-tags] Azure Dev Spaces 인프라의 IP 주소에 대한 수신 및 송신 통신을 허용하는 네트워크 정책을 추가합니다.

### <a name="ingress-only-network-traffic-requirements"></a>수신 전용 네트워크 트래픽 요구 사항

Azure Dev Spaces는 네임스페이스의 Pod 간에 라우팅을 제공합니다. 예를 들어 Azure Dev Spaces가 설정된 네임스페이스는 부모/자식 관계를 가질 수 있습니다. 그러면 부모 및 자식 네임스페이스를 통해 Pod 간에 네트워크 트래픽을 라우팅할 수 있습니다. 또한 Azure Dev Spaces는 자체 FQDN을 사용하여 서비스 엔드포인트를 노출합니다. 서비스를 노출하는 다양한 방법과 네임스페이스 수준 라우팅에 영향을 주는 방법을 구성하려면 [여러 엔드포인트 옵션 사용][endpoint-options]을 참조하세요.

## <a name="using-azure-cni"></a>Azure CNI 사용

기본적으로 AKS 클러스터는 Azure Dev Spaces와 함께 작동하는 네트워킹에 [kubenet][aks-kubenet]을 사용하도록 구성됩니다. [Azure CNI(Container Networking Interface)][aks-cni]를 사용하도록 AKS 클러스터를 구성할 수도 있습니다. AKS 클러스터에서 Azure CNI와 함께 Azure Dev Spaces를 사용하려면 Azure Dev Spaces에서 배포한 Pod에 사용할 최대 10개의 개인 IP 주소를 가상 네트워크 및 서브넷 주소 공간에 허용합니다. 개인 IP 주소를 허용하는 방법에 대한 자세한 내용은 [AKS Azure CNI 설명서][aks-cni-ip-planning]에서 확인할 수 있습니다.

## <a name="using-api-server-authorized-ip-ranges"></a>API 서버 권한이 부여된 IP 범위 사용

AKS 클러스터를 사용하면 사용자 지정 가상 네트워크를 사용하거나 [권한 있는 IP 범위를 사용하여 API 서버에 대한 액세스를 보호][aks-ip-auth-ranges]하는 등 클러스터와 상호 작용할 수 있는 IP 주소를 제한하는 추가 보안을 구성할 수 있습니다. 이 추가 보안을 사용할 때 Azure Dev Spaces를 사용하려면 클러스터를 [생성][aks-ip-auth-range-create]하는 동안 [해당 지역에 따라 추가 범위를 허용][service-tags]해야 합니다. 또한 기존 클러스터를 [업데이트][aks-ip-auth-range-update]하여 이러한 추가 범위를 허용할 수 있습니다. 또한 디버깅을 위해 AKS 클러스터에 연결하는 모든 개발 머신의 IP 주소가 API 서버에 연결하도록 허용해야 합니다.

## <a name="using-aks-private-clusters"></a>AKS 프라이빗 클러스터 사용

현재 [AKS 프라이빗 클러스터][aks-private-clusters]에는 Azure Dev Spaces가 지원되지 않습니다.

## <a name="using-different-endpoint-options"></a>여러 엔드포인트 옵션 사용

Azure Dev Spaces에는 AKS에서 실행되는 서비스의 엔드포인트를 노출하는 옵션이 있습니다. 클러스터에서 Azure Dev Spaces를 사용하도록 설정하는 경우, 클러스터의 엔드포인트 유형을 구성할 때 다음과 같은 옵션을 사용할 수 있습니다.

* 기본값인 *퍼블릭* 엔드포인트는 공용 IP 주소를 사용하여 수신 컨트롤러를 배포합니다. 공용 IP 주소는 클러스터의 DNS에 등록되므로 URL을 사용하여 서비스에 대한 공용 액세스를 허용합니다. `azds list-uris`를 사용하여 이 URL을 볼 수 있습니다.
* *프라이빗* 엔드포인트는 개인 IP 주소를 사용하여 수신 컨트롤러를 배포합니다. 개인 IP 주소를 사용하는 경우 클러스터의 가상 네트워크 내에서만 클러스터의 부하 분산 장치에 액세스할 수 있습니다. 부하 분산 장치의 개인 IP 주소는 클러스터의 DNS에 등록되므로 URL을 사용하여 클러스터의 가상 네트워크 내 서비스에 액세스할 수 있습니다. `azds list-uris`를 사용하여 이 URL을 볼 수 있습니다.
* 엔드포인트 옵션에 *없음* 을 설정하면 수신 컨트롤러를 배포할 수 없습니다. 수신 컨트롤러가 배포되지 않으면 [Azure Dev Spaces 라우팅 기능][dev-spaces-routing]이 작동하지 않습니다. 필요에 따라 [traefik][traefik-ingress] 또는 [NGINX][nginx-ingress]를 사용하여 사용자 고유의 수신 컨트롤러 솔루션을 구현할 수 있습니다. 그러면 라우팅 기능이 다시 작동할 수 있습니다.

엔드포인트 옵션을 구성하려면 클러스터에서 Azure Dev Spaces를 사용하도록 설정할 때 *-e* 또는 *--endpoint* 를 사용합니다. 예:

> [!NOTE]
> 이 엔드포인트 옵션을 사용하려면 Azure CLI 버전 2.2.0 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>클라이언트 요구 사항

Azure Dev Spaces는 Azure Dev Spaces CLI 확장, Visual Studio Code 확장 및 Visual Studio 확장과 같은 클라이언트 쪽 도구를 사용하여 디버깅을 위해 AKS 클러스터와 통신합니다. Azure Dev Spaces 클라이언트 쪽 도구를 사용하려면 개발 머신에서 [Azure Dev Spaces 인프라][dev-spaces-allow-infrastructure]로 들어오는 트래픽을 허용합니다. [API 서버 권한이 부여된 IP 범위][auth-range-section]를 사용하는 경우, 디버깅을 위해 AKS 클러스터에 연결하는 모든 개발 머신의 IP 주소가 API 서버에 연결하도록 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방식에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags