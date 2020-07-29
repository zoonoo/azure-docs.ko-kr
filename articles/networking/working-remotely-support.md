---
title: Azure 네트워킹 서비스를 사용 하 여 원격으로 작업
description: 이 페이지에서는 원격 작업을 가능 하 게 하는 데 사용할 수 있는 Azure 네트워킹 서비스를 사용 하는 방법 및 원격으로 작업 하는 사용자 수가 증가 하 여 트래픽 문제를 완화 하는 방법을 설명 합니다.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982857"
---
# <a name="working-remotely-using-azure-networking-services"></a>Azure 네트워킹 서비스를 사용 하 여 원격으로 작업

>[!NOTE]
> 이 문서에서는 Azure 네트워킹 서비스, Microsoft 네트워크 및 Azure 파트너 에코 시스템을 활용 하 여 원격으로 작업 하 고 COVID-19 위기 때문에 발생할 수 있는 네트워크 문제를 완화 하는 방법을 설명 합니다.

이 문서에서는 조직에서 사용자에 대 한 원격 액세스를 설정 하거나 사용량이 많은 기간 동안 추가 용량으로 기존 솔루션을 보완 하는 데 사용할 수 있는 옵션을 설명 합니다. 네트워크 설계자는 다음과 같은 문제에 직면 하 고 있습니다.

- 네트워크 사용률 증가를 해결 합니다.
- 회사 및 고객의 더 많은 직원에 게 안정적인 보안 연결을 제공 합니다.
- 전 세계 원격 위치에 대 한 연결을 제공 합니다.

모든 네트워크 (예: 사설 WAN 및 회사 코어 네트워크)가 최고 원격 작업자 로드에서 정체를 경험 하는 것은 아닙니다. 병목 상태는 일반적으로 회사의 온-프레미스 네트워크에 대 한 VPN 게이트웨이 및 홈 광대역 네트워크 에서만 보고 됩니다.

네트워크 계획자를 통해 병목 현상을 줄이고 네트워크 정체를 완화 하는 데 도움이 될 수 있습니다. 다른 트래픽 유형에는 서로 다른 네트워크 처리 우선 순위와 일부 스마트 로드 리디렉션/배포를 고려해 야 합니다. 예를 들어, 의사 환자 상호 작용의 실시간 tele-transmission는 높은 중요도와 지연/지터를 구분 합니다. 반면, 저장소 간의 동일한 트래픽 복제는 지연 시간이 중요 하지 않습니다. 이전 트래픽은 서비스 품질이 높은 최적의 네트워크 경로를 통해 라우팅해야 합니다. 반면에 최적 경로를 통해 나중에 트래픽을 라우팅할 수 있습니다.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>모범 사례 공유-Azure 네트워크는 탄력성 및 고가용성을 위해 설계 되었습니다.

Azure는 리소스 사용률의 급격 한 변화를 견딜 수 있도록 설계 되었으며, 피크 사용 기간 중에 매우 유용 합니다. 또한 Microsoft는 세계 최대의 네트워크 중 하나를 유지 관리 하 고 운영 합니다. Microsoft 네트워크는 단일 네트워크 요소 오류에서 전체 지역 장애에 이르기까지 다양 한 유형의 실패를 견딜 수 있는 고가용성을 위해 설계 되었습니다.

Microsoft 네트워크는 요구 사항을 충족 하 고 Skype 및 팀, CDN, 실시간 빅 데이터 분석, Azure storage, Bing 및 Xbox에 대 한 중요 한 멀티미디어 트래픽을 포함 하 여 다양 한 유형의 네트워크 트래픽에 대 한 최적의 성능을 제공 하도록 설계 되었습니다. 서로 다른 트래픽 유형에 대 한 최적의 성능을 제공 하기 위해 Microsoft 네트워크는 트래픽 원본에 최대한 가깝게 리소스를 통해 전송 하려는 모든 트래픽을 attracts 합니다.

>[!NOTE] 
>아래에서 설명 하는 Azure 네트워킹 기능을 사용 하면 Microsoft 글로벌 네트워크의 트래픽 인력 동작을 활용 하 여 더 나은 고객 네트워킹 환경을 제공할 수 있습니다. Microsoft 네트워크의 트래픽 인력 동작은 피크 사용 기간 동안 정체를 겪을 수 있는 첫 번째/마지막 마일 네트워크에서 가능한 한 빨리 트래픽 로드를 방지 하는 데 도움이 됩니다.
>

## <a name="enable-employees-to-work-remotely"></a>직원이 원격으로 작업할 수 있도록 설정

Azure VPN gateway는 지점 및 사이트 간 (P2S) 및 사이트 간 (S2S) VPN 연결을 모두 지원 합니다. Azure VPN gateway를 사용 하 여 직원의 연결을 확장 하 여 Azure 배포 리소스와 온-프레미스 리소스에 안전 하 게 액세스할 수 있습니다. 자세한 내용은 [사용자가 원격으로 작업할 수 있도록 하는 방법](../vpn-gateway/work-remotely-support.md)을 참조 하세요. 

SSTP (Secure Sockets Tunneling Protocol)를 사용 하는 경우 동시 연결 수는 128 개로 제한 됩니다. 더 많은 수의 연결을 얻으려면 OpenVPN 또는 IKEv2로 전환 하는 것이 좋습니다. 자세한 내용은 [OpenVPN 프로토콜 또는 SSTP에서 IKEv2로 전환](../vpn-gateway/ikev2-openvpn-from-sstp.md
)을 참조 하세요.

Azure에 배포 된 리소스에 액세스 하기 위해 원격 개발자는 액세스할 Vm에 공용 Ip를 요구 하지 않고 VPN 연결 대신 Azure 방호 솔루션을 사용 하 여 RDP 또는 SSH (secure shell access)를 가져올 수 있습니다. 자세한 내용은 [Azure 방호를 사용 하 여 원격으로 작업](../bastion/work-remotely-support.md)을 참조 하세요.

대규모 VPN 연결을 집계 하는 경우 서로 다른 온-프레미스 글로벌 위치의 리소스 간 모든 연결을 지원 하 고, 서로 다른 지역 허브 및 스포크 가상 네트워크에서, 여러 홈 광대역 네트워크의 사용률을 최적화 하기 위해 Azure 가상 WAN을 사용할 수 있습니다. 자세한 내용은 [가정에서 작업을 수행 하는 데 필요한 작업을 참조 하세요. Azure 가상 WAN이 도움이 될 수 있는 위치는 다음과](../virtual-wan/work-remotely-support.md)같습니다.

원격 직원을 지 원하는 또 다른 방법은 azure 방화벽을 사용 하 여 보호 되는 Azure virtual network에 호스트 되는 VDI (가상 데스크톱 인프라)를 배포 하는 것입니다. 예를 들어 WVD (Windows 가상 데스크톱)는 Azure에서 실행 되는 데스크톱 및 앱 가상화 서비스입니다. Windows 가상 데스크톱을 사용 하면 추가 게이트웨이 서버를 실행할 필요 없이 Azure 구독에 확장 가능 하 고 유연한 환경을 설정할 수 있습니다. 가상 네트워크의 WVD 가상 컴퓨터에 대해서만 책임을 집니다. 자세한 내용은 [Azure 방화벽 원격 작업 지원](../firewall/remote-work-support.md)을 참조 하세요. 

Azure에는 다양 한 에코 시스템 파트너 집합도 있습니다. Azure의 파트너 네트워크 가상 어플라이언스를 통해 VPN 연결을 확장할 수도 있습니다. 자세한 내용은 [원격 작업에 대 한 NVA (네트워크 가상 어플라이언스) 고려 사항](../vpn-gateway/nva-work-remotely-support.md)을 참조 하세요.

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>직원의 연결을 확장 하 여 전역으로 분산 되는 리소스에 액세스

다음 Azure 서비스를 통해 직원이 전 세계적으로 배포 된 리소스에 액세스할 수 있습니다. 리소스는 Azure 지역, 온-프레미스 네트워크 또는 다른 공용 또는 사설 클라우드에서 있을 수 있습니다. 

- **Azure 가상 네트워크 피어 링**: 둘 이상의 azure 지역에 리소스를 배포 하거나 여러 가상 네트워크를 사용 하 여 원격으로 작업 하는 직원의 연결을 집계 하는 경우 가상 네트워크 피어 링을 사용 하 여 여러 azure 가상 네트워크 간에 연결을 설정할 수 있습니다. 자세한 내용은 [가상 네트워크 피어링][VNet-peer]을 참조하세요.

- **AZURE vpn 기반 솔루션**: P2S 또는 S2S vpn을 통해 azure에 연결 된 원격 직원의 경우 온-프레미스 네트워크와 Azure VPN GATEWAY 간에 S2S vpn을 구성 하 여 온-프레미스 네트워크에 대 한 액세스를 사용 하도록 설정할 수 있습니다. 자세한 내용은 [사이트 간 연결 만들기][S2S]를 참조 하세요.

- **Express**경로: express 경로 개인 피어 링을 사용 하 여 Azure 배포와 온-프레미스 인프라 또는 공동 배치 기능에서 인프라 간에 개인 연결을 사용 하도록 설정할 수 있습니다. Microsoft 피어 링을 통해 Express 경로를 통해 온-프레미스 네트워크에서 Microsoft의 공용 끝점에 액세스할 수도 있습니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다. 인터넷을 통한 일반 연결 보다 낮은 대기 시간과 일관성 있는 보안 연결, 안정성 및 높은 처리량을 제공 합니다. 자세한 내용은 [ExpressRoute 개요][ExR]를 참조하세요. 이미 [express 경로 파트너 에코 시스템][ExR-eco] 의 일부인 기존 네트워크 공급자를 활용 하면 Microsoft에 대 한 높은 대역폭 연결을 가져오는 시간을 줄일 수 있습니다.  [Express 경로 직접][ExR-D] 를 사용 하 여 온-프레미스 네트워크를 Microsoft 백본에 직접 연결할 수 있습니다. Express 경로 다이렉트는 이중 10gbps 또는 100 Gbps의 두 가지 다른 선 율 옵션을 제공 합니다. 

- **Azure 가상 wan**: AZURE 가상 WAN은 VPN 연결과 express 경로 회로 간의 원활한 상호 운용성을 허용 합니다. 앞서 언급 했 듯이, Azure 가상 WAN은 서로 다른 지역 허브 및 스포크 가상 네트워크에서 다른 온-프레미스 전역 위치의 리소스 간 임의 연결도 지원 합니다.

## <a name="scale-customer-connectivity-to-frontend-resources"></a>프런트 엔드 리소스에 대 한 고객 연결 크기 조정

많은 사람들이 온라인으로 전환 하는 경우 많은 회사 웹 사이트에서 고객의 트래픽이 증가 합니다. Azure 애플리케이션 게이트웨이는 증가 된 프런트 엔드 워크 로드를 관리 하는 데 도움이 됩니다. 자세한 내용은 [Application Gateway 높은 트래픽 지원](../application-gateway/high-traffic-support.md)을 참조 하세요.

## <a name="microsoft-support-for-multi-cloud-traffic"></a>다중 클라우드 트래픽에 대 한 Microsoft 지원

다른 공용 클라우드에서 배포 하는 경우 Microsoft는 글로벌 연결을 제공할 수 있습니다. Azure 가상 WAN, VPN 또는 Express 경로는 이러한 측면에서 도움이 될 수 있습니다. Azure에서 다른 클라우드로의 연결을 확장 하려면 두 클라우드 간에 S2S VPN을 구성할 수 있습니다. Express 경로를 사용 하 여 Azure에서 다른 공용 클라우드로의 연결을 설정할 수도 있습니다. Oracle 클라우드는 Express 경로 파트너 에코 시스템의 일부입니다. [Azure와 Oracle 클라우드 인프라 간의 직접 상호 관계를 설정할][Az-OCI]수 있습니다. Express 경로 파트너 에코 시스템의 일부인 대부분의 서비스 공급자는 다른 공용 클라우드에 대 한 개인 연결도 제공 합니다. 이러한 서비스 공급자를 활용 하 여 Azure의 배포와 Express 경로를 통해 다른 클라우드의 개인 연결을 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서는 다양 한 Azure 네트워킹 기능을 사용 하 여 사용자가 원격으로 작업할 수 있는 크기를 조정 하는 방법을 설명 합니다.

| **자료** | **설명** |
| --- | --- |
| [사용자가 원격으로 작업할 수 있도록 하는 방법](../vpn-gateway/work-remotely-support.md) | 사용자에 대 한 원격 액세스를 설정 하거나 조직에 대 한 추가 용량으로 기존 솔루션을 보완 하는 데 사용할 수 있는 옵션을 검토 합니다.|
| [집에서 요구 하는 작업을 위해 노력 하 고 있나요? Azure 가상 WAN이 지원할 수 있는 위치는 다음과 같습니다.](../virtual-wan/work-remotely-support.md) | Azure 가상 WAN을 사용 하 여 조직의 원격 연결 요구를 해결 합니다.|
| [Application Gateway 높은 트래픽 지원](../application-gateway/high-traffic-support.md) | 웹 응용 프로그램에 대 한 트래픽을 관리 하는 확장 가능 하 고 안전한 방법으로 WAF (웹 응용 프로그램 방화벽)와 함께 Application Gateway를 사용 합니다. |
| [원격 작업에 대 한 NVA (네트워크 가상 어플라이언스) 고려 사항](../vpn-gateway/nva-work-remotely-support.md)|Azure의 Nva 활용에 대 한 지침을 검토 하 여 원격 액세스 솔루션을 제공 합니다. |
| [SSTP에서 OpenVPN 프로토콜 또는 IKEv2로 전환](https://go.microsoft.com/fwlink/?linkid=2124112) | OpenVPN 프로토콜 또는 IKEv2로 전환 하 여 SSTP의 128 동시 연결 제한을 극복 합니다.|
| [Azure 방호를 사용 하 여 원격으로 작업](../bastion/work-remotely-support.md) | 공용 IP 주소를 사용 하지 않고 Azure Portal 직접 Azure virtual network 내에서 가상 머신에 대 한 안전 하 고 원활한 RDP/SSH 연결을 제공 합니다. |
| [Azure Express 경로를 사용 하 여 원격 사용자를 지 원하는 하이브리드 연결 만들기](../expressroute/work-remotely-support.md) | 하이브리드 연결을 위한 Express 경로를 사용 하 여 조직의 사용자가 원격으로 작업할 수 있도록 합니다.|
| [Azure 방화벽 원격 작업 지원](../firewall/remote-work-support.md)|Azure 방화벽을 사용 하 여 Azure 가상 네트워크 리소스를 보호 합니다. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
