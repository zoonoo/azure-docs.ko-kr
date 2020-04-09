---
title: Azure 네트워킹 서비스를 사용하여 원격으로 작업
description: 이 페이지에서는 원격으로 작업할 수 있도록 사용할 수 있는 Azure 네트워킹 서비스를 사용하는 방법과 원격으로 작업하는 사용자 수증가로 인한 트래픽 문제를 완화하는 방법에 대해 설명합니다.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982857"
---
# <a name="working-remotely-using-azure-networking-services"></a>Azure 네트워킹 서비스를 사용하여 원격으로 작업

>[!NOTE]
> 이 문서에서는 Azure 네트워킹 서비스, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 직면할 수 있는 네트워크 문제를 완화하는 방법에 대해 설명합니다.

이 문서에서는 조직에서 사용자에 대한 원격 액세스를 설정하거나 사용량이 가장 많은 기간 동안 추가 용량으로 기존 솔루션을 보완하는 데 사용할 수 있는 옵션에 대해 설명합니다. 네트워크 아키텍트에는 다음과 같은 과제가 있습니다.

- 네트워크 사용률 의 증가를 해결합니다.
- 회사와 고객의 더 많은 직원에게 신뢰할 수 있는 안전한 연결을 제공합니다.
- 전 세계 원격 위치에 대한 연결을 제공합니다.

모든 네트워크(예: 개인 WAN 및 회사 코어 네트워크)가 최대 원격 작업자 부하로 인한 혼잡을 경험하는 것은 아닙니다. 병목 현상은 일반적으로 기업의 온-프레미스 네트워크의 가정용 광대역 네트워크 및 VPN 게이트웨이에서만 보고됩니다.

네트워크 플래너는 서로 다른 트래픽 유형에 서로 다른 네트워크 처리 우선 순위와 일부 스마트 부하 리디렉션/배포가 필요하다는 점을 염두에 두어 병목 현상을 완화하고 네트워크 혼잡을 완화할 수 있습니다. 예를 들어, 의사-환자 상호 작용의 실시간 텔레메데신 트래픽은 매우 중요하고 지연/지터에 민감하다. 반면 저장소 간에 동일한 트래픽을 복제하는 것은 민감한 지연이 아닙니다. 이전 트래픽은 더 높은 서비스 품질의 최적의 네트워크 경로를 통해 라우팅되어야 합니다. 이후 트래픽을 최적이 아닌 경로를 통해 라우팅하는 것이 허용되는 반면.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>모범 사례 공유 - Azure 네트워크는 탄력성 및 고가용성을 위해 설계되었습니다.

Azure는 리소스 사용률의 급격한 변화를 견딜 수 있도록 설계되었으며 사용량이 가장 많은 기간 동안 크게 도움이 될 수 있습니다. 또한 Microsoft는 세계에서 가장 큰 네트워크 중 하나를 유지 관리하고 운영합니다. Microsoft의 네트워크는 단일 네트워크 요소 오류에서 전체 리전 실패에 이르기까지 다양한 유형의 장애를 견딜 수 있는 고가용성을 위해 설계되었습니다.

Microsoft 네트워크는 요구 사항을 충족하고 Skype 및 Teams, CDN, 실시간 빅 데이터 분석, Azure 저장소, Bing 및 Xbox에 대한 지연에 민감한 멀티미디어 트래픽을 비롯한 다양한 유형의 네트워크 트래픽에 대해 최적의 성능을 제공하도록 설계되었습니다. 다양한 유형의 트래픽에 대해 최적의 성능을 제공하기 위해 Microsoft 네트워크는 트래픽의 원본에 최대한 가깝게 리소스를 통해 전송하려는 모든 트래픽을 끌어들입니다.

>[!NOTE] 
>아래에 설명된 Azure 네트워킹 기능을 사용하면 Microsoft 글로벌 네트워크의 트래픽 유치 동작을 활용하여 더 나은 고객 네트워킹 환경을 제공합니다. Microsoft 네트워크의 트래픽 유치 동작은 사용량이 가장 많은 기간 동안 혼잡이 발생할 수 있는 첫 번째/마지막 마일 네트워크에서 가능한 한 빨리 트래픽을 로드하는 데 도움이 됩니다.
>

## <a name="enable-employees-to-work-remotely"></a>직원들이 원격으로 작업할 수 있도록 지원

Azure VPN 게이트웨이는 P2S(사이트 간) 및 S2S(사이트 간) VPN 연결을 모두 지원합니다. Azure VPN 게이트웨이를 사용하면 직원의 연결을 확장하여 Azure 배포 된 리소스와 온-프레미스 리소스 모두에 안전하게 액세스할 수 있습니다. 자세한 내용은 [사용자가 원격으로 작업할 수 있도록 하는 방법을 참조하세요.](../vpn-gateway/work-remotely-support.md) 

보안 소켓 터널링 프로토콜(SSTP)을 사용하는 경우 동시 연결 수는 128개로 제한됩니다. 더 많은 연결을 얻으려면 OpenVPN 또는 IKEv2로 전환하는 것이 좋습니다. 자세한 내용은 [SSTP에서 OpenVPN 프로토콜 또는 IKEv2로의 전환을](../vpn-gateway/ikev2-openvpn-from-sstp.md
)참조하십시오.

Azure에 배포된 리소스에 액세스하려면 원격 개발자는 VPN 연결 대신 Azure Bastion 솔루션을 사용하여 VM에 대한 공용 IP를 사용하지 않고도 안전한 쉘 액세스(RDP 또는 SSH)를 얻을 수 있습니다. 자세한 내용은 [Azure 요새를 사용하여 원격으로 작업을](../bastion/work-remotely-support.md)참조하세요.

대규모 VPN 연결을 집계하고, 서로 다른 온-프레미스 글로벌 위치, 다른 지역 허브 및 스포크 가상 네트워크의 리소스 간 연결을 지원하고, 여러 홈 광대역 네트워크의 활용도를 최적화하기 위해 Azure Virtual WAN을 사용할 수 있습니다. 자세한 내용은 [가정의 필요 사항에서 일하기 위해 고군분투하는 것을 참조하십시오. Azure 가상 WAN이 도움이 될 수 있는 곳은 다음과 같습니다.](../virtual-wan/work-remotely-support.md)

원격 인력을 지원하는 또 다른 방법은 Azure 방화벽으로 보호되는 Azure 가상 네트워크에서 호스팅되는 VDI(가상 데스크톱 인프라)를 배포하는 것입니다. 예를 들어 WVD(Windows 가상 데스크톱)는 Azure에서 실행되는 데스크톱 및 앱 가상화 서비스입니다. Windows Virtual Desktop을 사용하면 추가 게이트웨이 서버를 실행할 필요 없이 Azure 구독에서 확장 가능하고 유연한 환경을 설정할 수 있습니다. 가상 네트워크의 WVD 가상 시스템에 대해서만 책임이 있습니다. 자세한 내용은 [Azure 방화벽 원격 작업 지원을](../firewall/remote-work-support.md)참조하십시오. 

Azure에는 풍부한 에코 시스템 파트너 집합도 있습니다. Azure의 파트너 네트워크 가상 어플라이언스도 VPN 연결을 확장하는 데 도움을 줄 수 있습니다. 자세한 내용은 [원격 작업에 대한 NVA(네트워크 가상 어플라이언스) 고려 사항을](../vpn-gateway/nva-work-remotely-support.md)참조하십시오.

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>전 세계에 분산된 리소스에 액세스하기 위해 직원의 연결 확장

다음 Azure 서비스는 직원이 전역적으로 분산된 리소스에 액세스할 수 있도록 하는 데 도움이 될 수 있습니다. 리소스는 Azure 지역, 온-프레미스 네트워크 또는 다른 퍼블릭 또는 프라이빗 클라우드에 있을 수 있습니다. 

- **Azure 가상 네트워크 피어링:** 리소스를 두 개 이상의 Azure 지역에 배포하거나 여러 가상 네트워크를 사용하여 원격으로 작업하는 직원의 연결을 집계하는 경우 가상 네트워크 피어링을 사용하여 여러 Azure 가상 네트워크 간의 연결을 설정할 수 있습니다. 자세한 내용은 [가상 네트워크 피어링][VNet-peer]을 참조하세요.

- **Azure VPN 기반 솔루션**: P2S 또는 S2S VPN을 통해 Azure에 연결된 원격 직원의 경우 온-프레미스 네트워크와 Azure VPN 게이트웨이 간에 S2S VPN을 구성하여 온-프레미스 네트워크에 대한 액세스를 활성화할 수 있습니다. 자세한 내용은 [사이트 간 연결 만들기를][S2S]참조하십시오.

- **ExpressRoute**: ExpressRoute 개인 피어링을 사용하면 공동 위치 시설에서 Azure 배포와 온-프레미스 인프라 또는 인프라 간에 개인 연결을 활성화할 수 있습니다. ExpressRoute는 Microsoft 피어링을 통해 온-프레미스 네트워크에서 Microsoft의 공용 끝점에 액세스할 수도 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반적인 연결보다 낮고 일관된 대기 시간을 통해 안전한 연결성, 안정성, 높은 처리량을 제공합니다. 자세한 내용은 [ExpressRoute 개요][ExR]를 참조하세요. [이미 ExpressRoute 파트너 에코시스템의][ExR-eco] 일부인 기존 네트워크 공급자를 활용하면 Microsoft에 대한 대규모 대역폭 연결을 얻는 시간을 줄일 수 있습니다.  [ExpressRoute Direct를][ExR-D] 사용하면 온-프레미스 네트워크를 Microsoft 백본에 직접 연결할 수 있습니다. ExpressRoute Direct는 듀얼 10Gbps 또는 100Gbps의 두 가지 라인 요금 옵션을 제공합니다. 

- **Azure 가상 WAN**: Azure Virtual WAN을 사용하면 VPN 연결과 ExpressRoute 회로 간에 원활한 상호 운용성이 가능합니다. 앞에서 설명한 것처럼 Azure Virtual WAN은 다른 지역 허브 및 스포크 가상 네트워크의 다른 온프레미 글로벌 위치에 있는 리소스 간의 모든 연결을 지원합니다.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>프런트 엔드 리소스에 대한 고객 연결 확장

더 많은 사람들이 온라인에 접속하는 동안 많은 기업 웹 사이트가 고객 트래픽을 증가시켰습니다. Azure 응용 프로그램 게이트웨이는 증가된 프런트 엔드 워크로드를 관리하는 데 도움이 될 수 있습니다. 자세한 내용은 [응용 프로그램 게이트웨이 트래픽 이량 지원](../application-gateway/high-traffic-support.md)을 참조하십시오.

## <a name="microsoft-support-for-multi-cloud-traffic"></a>멀티 클라우드 트래픽에 대한 Microsoft 지원

다른 공용 클라우드에서 배포하는 경우 Microsoft는 글로벌 연결을 제공할 수 있습니다. Azure 가상 WAN, VPN 또는 익스프레스루트는 이와 관련하여 도움이 될 수 있습니다. Azure에서 다른 클라우드로 연결을 확장하려면 두 클라우드 간에 S2S VPN을 구성할 수 있습니다. ExpressRoute를 사용하여 Azure에서 다른 공용 클라우드에 대한 연결을 설정할 수도 있습니다. 오라클 클라우드는 ExpressRoute 파트너 에코시스템의 일부입니다. [Azure와 Oracle 클라우드 인프라 간에 직접 상호 연결을 설정할][Az-OCI]수 있습니다. ExpressRoute 파트너 에코시스템의 일부인 대부분의 서비스 제공업체는 다른 퍼블릭 클라우드에 대한 개인 연결을 제공합니다. 이러한 서비스 공급자를 활용하면 ExpressRoute를 통해 Azure및 기타 클라우드에서의 배포 간에 개인 연결을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 사용자가 원격으로 작업하도록 확장하는 데 다양한 Azure 네트워킹 기능을 사용할 수 있는 방법에 대해 설명합니다.

| **기술** | **설명** |
| --- | --- |
| [사용자가 원격으로 작업할 수 있도록 하는 방법](../vpn-gateway/work-remotely-support.md) | 사용 가능한 옵션을 검토하여 사용자에 대한 원격 액세스를 설정하거나 기존 솔루션을 조직에 대한 추가 용량으로 보완합니다.|
| [가정의 필요에서 일하기 위해 고군분투? Azure 가상 WAN이 도움을 줄 수 있는 곳은 다음과 같습니다.](../virtual-wan/work-remotely-support.md) | Azure Virtual WAN을 사용하여 조직의 원격 연결 요구 사항을 해결합니다.|
| [Application Gateway 높은 트래픽 지원](../application-gateway/high-traffic-support.md) | 웹 응용 프로그램에 대한 트래픽을 관리할 수 있는 확장 가능하고 안전한 방법을 위해 WAF(웹 응용 프로그램 방화벽)가 있는 응용 프로그램 게이트웨이를 사용합니다. |
| [원격 작업에 대한 네트워크 가상 어플라이언스(NVA) 고려 사항](../vpn-gateway/nva-work-remotely-support.md)|Azure에서 NVA를 활용하여 원격 액세스 솔루션을 제공하는 방법에 대한 지침을 검토합니다. |
| [SSTP에서 OpenVPN 프로토콜 또는 IKEv2로 전환](https://go.microsoft.com/fwlink/?linkid=2124112) | OpenVPN 프로토콜 또는 IKEv2로 전환하여 SSTP의 128개의 동시 연결 제한을 극복합니다.|
| [Azure 요새를 사용하여 원격으로 작업](../bastion/work-remotely-support.md) | 공용 IP 주소를 사용하지 않고 Azure 포털에서 직접 Azure 가상 네트워크 내의 가상 시스템에 안전하고 원활한 RDP/SSH 연결을 제공합니다. |
| [Azure ExpressRoute를 사용하여 원격 사용자를 지원하는 하이브리드 연결을 만듭니다.](../expressroute/work-remotely-support.md) | 하이브리드 연결에 ExpressRoute를 사용하여 조직의 사용자가 원격으로 작업할 수 있도록 합니다.|
| [Azure 방화벽 원격 작업 지원](../firewall/remote-work-support.md)|Azure 방화벽을 사용하여 Azure 가상 네트워크 리소스를 보호합니다. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
