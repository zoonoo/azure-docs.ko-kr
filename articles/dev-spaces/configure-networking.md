---
title: 다른 네트워크 토폴로지에서 Azure 개발자 공간에 대한 네트워킹 구성
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Azure Kubernetes 서비스에서 Azure 개발자 공간 실행을 위한 네트워킹 요구 사항을 설명합니다.
keywords: Azure 개발자 공간, 개발자 공간, 도커, 쿠버넷, Azure, AKS, Azure Kubernetes 서비스, 컨테이너, CNI, 쿠베넷, SDN, 네트워크
ms.openlocfilehash: 3e344576caf276ae7cb5fe00395c84810a4e7d32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262046"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>다른 네트워크 토폴로지에서 Azure 개발자 공간에 대한 네트워킹 구성

Azure 개발자 공간은 기본 네트워킹 구성이 있는 AKS(Azure Kubernetes Service) 클러스터에서 실행됩니다. 방화벽 뒤에 클러스터를 배치하거나, 네트워크 보안 그룹을 사용하거나, 네트워크 정책을 사용하는 등 AKS 클러스터의 네트워킹 구성을 변경하려면 Azure 개발자 공간 실행에 대한 추가 고려 사항을 통합해야 합니다.

![가상 네트워크 구성](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>가상 네트워크 또는 서브넷 구성

AKS 클러스터에는 다른 가상 네트워크 또는 서브넷 구성이 있어 AKS 클러스터의 트래픽이 침투하거나 송신될 수 있습니다. 예를 들어 클러스터가 Azure 방화벽과 같은 방화벽 뒤에 있거나 네트워크 트래픽을 제한하기 위해 네트워크 보안 그룹 또는 사용자 지정 역할을 사용할 수 있습니다. [GitHub의 Azure 개발자 공간 샘플 리포지토리에서][sample-repo]예제 네트워크 구성을 찾을 수 있습니다.

Azure 개발자 공간에는 *인그레스 및 송신* 네트워크 트래픽과 *인그레스 트래픽에 대한* 특정 요구 사항이 있습니다. AKS 클러스터의 트래픽을 제한하는 가상 네트워크 또는 서브넷 구성이 있는 AKS 클러스터에서 Azure 개발자 공간을 사용하는 경우 Azure 개발자 공간이 제대로 작동하려면 다음 의 사용 만 을 따르고 트래픽 요구 사항을 입력하고 송신해야 합니다.

### <a name="ingress-and-egress-network-traffic-requirements"></a>네트워크 트래픽 요구 사항 의 송신 및 송신

Azure 개발자 공간에는 다음과 같은 FQDN에 대한 트래픽이 들어와 트래픽이 필요합니다.

| FQDN                       | 포트       | 사용      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Azure 개발자 공간에 대한 도커 이미지를 가져오려면 |
| gcr.io                     | HTTPS: 443 | Azure 개발자 공간에 대한 투구 이미지를 가져오려면 |
| storage.googleapis.com     | HTTPS: 443 | Azure 개발자 공간에 대한 투구 이미지를 가져오려면 |
| azds-*.azds.io             | HTTPS: 443 | Azure 개발자 공간 컨트롤러에 대 한 Azure 개발자 공간 백 엔드 서비스와 통신 합니다. 정확한 FQDN은 *데이터플레인Fqdn에서* 찾을 수 있습니다.`USERPROFILE\.azds\settings.json` |

방화벽 또는 보안 구성을 업데이트하여 위의 모든 FQDN과 네트워크 트래픽이 연결되고 허용되도록 합니다. 예를 들어 방화벽을 사용하여 네트워크를 보호하는 경우 위의 FQD를 방화벽의 응용 프로그램 규칙에 추가하여 이러한 도메인을 트래픽으로 연결및 허용할 수 있습니다.

### <a name="ingress-only-network-traffic-requirements"></a>네트워크 트래픽 요구 사항만 인서

Azure 개발자 공간은 Kubernetes 네임스페이스 수준 라우팅뿐만 아니라 자체 FQDN을 사용하여 서비스에 대한 공용 액세스를 제공합니다. 이러한 두 기능이 모두 작동하려면 방화벽 또는 네트워크 구성을 업데이트하여 클러스터의 Azure Dev Spaces 의 외부 IP 주소에 대한 공용 침입을 허용합니다. 또는 [내부 로드 밸런서를][aks-internal-lb] 만들고 방화벽에 NAT 규칙을 추가하여 방화벽의 공용 IP를 내부 로드 밸런서의 IP로 변환할 수 있습니다. [또한 traefik][traefik-ingress] 또는 [NGINX를][nginx-ingress] 사용하여 사용자 지정 받는 컨트롤러를 만들 수도 있습니다.

## <a name="aks-cluster-network-requirements"></a>AKS 클러스터 네트워크 요구 사항

AKS를 사용하면 [네트워크 정책을][aks-network-policies] 사용하여 클러스터의 포드 간 트래픽을 제어하고 송신할 수 있을 뿐만 아니라 포드에서 트래픽을 송신할 수 있습니다. Azure 개발자 공간에는 *인그레스 및 송신* 네트워크 트래픽과 *인그레스 트래픽에 대한* 특정 요구 사항이 있습니다. AKS 네트워크 정책이 있는 AKS 클러스터에서 Azure 개발자 공간을 사용하는 경우 Azure 개발자 공간이 제대로 작동하려면 다음 사용 만 하고 트래픽 요구 사항을 발생시키고 송신해야 합니다.

### <a name="ingress-and-egress-network-traffic-requirements"></a>네트워크 트래픽 요구 사항 의 송신 및 송신

Azure 개발자 공간을 사용하면 디버깅을 위해 클러스터의 개발 공간에서 포드와 직접 통신할 수 있습니다. 이 기능이 작동하려면 [지역에 따라 달라지는][dev-spaces-ip-auth-range-regions]Azure Dev Spaces 인프라의 IP 주소에 송신 및 송신 통신을 허용하는 네트워크 정책을 추가합니다.

### <a name="ingress-only-network-traffic-requirements"></a>네트워크 트래픽 요구 사항만 인서

Azure 개발자 공간은 네임스페이스 에서 포드 간에 라우팅을 제공합니다. 예를 들어 Azure 개발자 공간이 활성화된 네임스페이스에는 부모/자식 관계가 있을 수 있으며, 이를 통해 네트워크 트래픽을 상위 및 하위 네임스페이스의 포드 간에 라우팅할 수 있습니다. 또한 Azure 개발자 공간은 자체 FQDN을 사용하여 서비스 끝점을 노출합니다. 서비스를 노출하는 다양한 방법과 서비스를 노출하는 방법을 구성하려면 다른 [끝점 옵션 사용을][endpoint-options]참조하십시오.

## <a name="using-azure-cni"></a>Azure CNI 사용

기본적으로 AKS 클러스터는 Azure 개발자 공간에서 작동하는 네트워킹에 [쿠베넷을][aks-kubenet] 사용하도록 구성됩니다. [CNI(Azure 컨테이너 네트워킹 인터페이스)를][aks-cni]사용하도록 AKS 클러스터를 구성할 수도 있습니다. AKS 클러스터에서 Azure CNI와 함께 Azure 개발자 공간을 사용하려면 Azure 개발자 공간에서 배포한 포드에 대해 최대 10개의 개인 IP 주소에 대해 가상 네트워크 및 서브넷 주소 공간을 허용합니다. 개인 IP 주소 허용에 대한 자세한 내용은 [AKS Azure CNI 설명서에서][aks-cni-ip-planning]확인할 수 있습니다.

## <a name="using-api-server-authorized-ip-ranges"></a>API 서버 인증 IP 범위 사용

AKS 클러스터를 사용하면 사용자 지정 가상 네트워크를 사용하거나 권한 있는 IP 범위를 사용하여 [API 서버에 대한 액세스를 보호하는][aks-ip-auth-ranges]등 클러스터와 상호 작용할 수 있는 IP 주소를 제한하는 추가 보안을 구성할 수 있습니다. 클러스터를 [만드는][aks-ip-auth-range-create] 동안 이 추가 보안을 사용할 때 Azure 개발자 공간을 사용하려면 [지역에 따라 추가 범위를 허용해야][dev-spaces-ip-auth-range-regions]합니다. 기존 클러스터를 [업데이트하여][aks-ip-auth-range-update] 이러한 추가 범위를 허용할 수도 있습니다. 또한 API 서버에 연결하기 위해 디버깅을 위해 AKS 클러스터에 연결하는 모든 개발 컴퓨터의 IP 주소를 허용해야 합니다.

## <a name="using-aks-private-clusters"></a>AKS 개인 클러스터 사용

현재 Azure 개발자 공간은 [AKS 개인 클러스터에서][aks-private-clusters]지원되지 않습니다.

## <a name="using-different-endpoint-options"></a>다양한 엔드포인트 옵션 사용

Azure 개발자 공간에는 AKS에서 실행되는 서비스에 대한 끝점을 노출하는 옵션이 있습니다. 클러스터에서 Azure 개발자 공간을 사용하도록 설정하면 클러스터의 끝점 유형을 구성하기 위한 다음 옵션이 있습니다.

* 기본값인 *공용* 끝점은 공용 IP 주소가 있는 인드레스 컨트롤러를 배포합니다. 공용 IP 주소는 클러스터의 DNS에 등록되어 URL을 사용하여 서비스에 대한 공용 액세스를 허용합니다. 을 사용하여 `azds list-uris`이 URL을 볼 수 있습니다.
* *개인* 끝점은 개인 IP 주소가 있는 인그레스 컨트롤러를 배포합니다. 개인 IP 주소를 사용하면 클러스터의 로드 밸런서에 클러스터의 가상 네트워크 내에서만 액세스할 수 있습니다. 로드 밸런서의 개인 IP 주소는 클러스터의 DNS에 등록되어 URL을 사용하여 클러스터의 가상 네트워크 내의 서비스에 액세스할 수 있습니다. 을 사용하여 `azds list-uris`이 URL을 볼 수 있습니다.
* 끝점 옵션에 대해 *없음을* 설정하면 받는 컨트롤러가 배포되지 않습니다. 인트리스 컨트롤러가 배포되지 않은 경우 [Azure 개발자 공간 라우팅 기능이][dev-spaces-routing] 작동하지 않습니다. 선택적으로, 당신은 라우팅 기능이 다시 작동 할 수 있도록 [traefik][traefik-ingress] 또는 [NGINX를][nginx-ingress]사용하여 자신의 inress 컨트롤러 솔루션을 구현 할 수 있습니다.

엔드포인트 옵션을 구성하려면 클러스터에서 Azure 개발자 공간을 사용하도록 설정하면 *-e* 또는 *--endpoint를* 사용합니다. 다음은 그 예입니다.

> [!NOTE]
> 끝점 옵션을 사용하려면 Azure CLI 버전 2.2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>클라이언트 요구 사항

Azure 개발자 공간은 Azure 개발자 공간 CLI 확장, Visual Studio 코드 확장 및 Visual Studio 확장과 같은 클라이언트 측 도구를 사용하여 AKS 클러스터와 통신하여 디버깅합니다. Azure 개발자 공간 클라이언트 쪽 도구를 사용하려면 개발 컴퓨터에서 *azds-azds.io\** 도메인으로 의 트래픽을 허용합니다. 정확한 `USERPROFILE\.azds\settings.json` FQDN은 *데이터플레인Fqdn을* 참조하십시오. API [서버 권한 있는 IP 범위를][auth-range-section]사용하는 경우 API 서버에 연결하기 위해 디버깅을 위해 AKS 클러스터에 연결하는 모든 개발 컴퓨터의 IP 주소도 허용해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-quickstart]

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
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[team-quickstart]: quickstart-team-development.md