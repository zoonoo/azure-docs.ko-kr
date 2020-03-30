---
title: Azure를 퍼블릭 클라우드에 연결 | 마이크로 소프트 문서
description: Azure를 다른 퍼블릭 클라우드에 연결하는 다양한 방법 설명
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: osamaz
ms.openlocfilehash: b8a454c2a104dfe8545cf734bf0b020b8f749bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889638"
---
# <a name="connecting-azure-with-public-clouds"></a>Azure와 퍼블릭 클라우드 연결

많은 기업들이 비즈니스 및 기술 적 목표 때문에 멀티 클라우드 전략을 추구하고 있습니다. 여기에는 비용, 유연성, 기능 가용성, 중복성, 데이터 주권 등이 포함됩니다. 이 전략은 두 클라우드의 장점을 최대한 활용하는 데 도움이 됩니다. 

또한 이러한 접근 방식은 네트워크 및 응용 프로그램 아키텍처 측면에서 기업에 문제를 제기합니다. 이러한 문제 중 일부는 대기 시간 및 데이터 처리량입니다. 이러한 문제를 해결하기 위해 고객은 여러 클라우드에 직접 연결하려고 합니다. 일부 서비스 공급자는 고객을 위해 여러 클라우드 공급자를 연결하는 솔루션을 제공합니다. 다른 경우에는 고객이 자체 라우터를 배포하여 여러 공용 클라우드를 연결할 수 있습니다.
## <a name="connectivity-via-expressroute"></a>익스프레스루트를 통한 연결
ExpressRoute를 사용하면 고객이 연결 공급자가 용이하게 하는 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 통해 고객은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.

익스프레스루트를 통해 연결하는 방법에는 세 가지가 있습니다.

1. 계층3 공급자
2. 계층2 공급자
3. 직접 연결

### <a name="layer3-provider"></a>계층3 공급자

Layer3 공급자는 일반적으로 IP VPN 또는 MPLS VPN 공급자로 알려져 있습니다. 고객은 이러한 공급자를 활용하여 데이터 센터, 지점 및 클라우드 간의 멀티포인트 연결을 지원합니다. 고객은 BGP 또는 정적 기본 경로를 통해 L3 공급자에 연결합니다. 서비스 공급자는 고객 사이트, 데이터 센터 및 퍼블릭 클라우드 간의 경로를 보급합니다. 
 
Layer3 공급자를 통해 연결할 때 Microsoft는 BGP를 통해 서비스 공급자에게 고객 VNET 경로를 보급합니다. 공급자는 두 가지 다른 구현을 가질 수 있습니다.

![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

모든 클라우드 공급자의 트래픽이 고객 라우터에 도달하는 경우 공급자는 각 클라우드 공급자를 별도의 VRF에 착륙시킬 수 있습니다. 고객이 서비스 공급자와 함께 BGP를 실행하는 경우 이러한 경로는 기본적으로 다른 클라우드 공급자에게 다시 보급됩니다. 

서비스 공급자가 동일한 VRF에 모든 클라우드 공급자를 방문하면 경로가 서비스 공급자의 다른 클라우드 공급자에게 직접 보급됩니다. 이는 eBGP 경로가 기본적으로 다른 eBGP 이웃에 보급되는 표준 BGP 작업을 가정합니다.

각 퍼블릭 클라우드에는 접두사 제한이 다르므로 경로 서비스 공급자를 배포하는 동안 경로를 배포할 때주의해야 합니다.

### <a name="layer2-provider-and-direct-connection"></a>계층2 공급자 및 직접 연결

두 모델의 물리적 연결은 다르지만 layer3 BGP는 MSEE와 고객 라우터 간에 직접 설정됩니다. 익스프레스루트 다이렉트 고객은 MSEE에 직접 연결합니다. Layer2의 경우 서비스 공급자는 VLAN을 고객 구내에서 클라우드로 확장합니다. 고객은 계층2 네트워크 위에 BGP를 실행하여 DC를 클라우드에 연결합니다.
![](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
두 경우 모두 고객은 각 퍼블릭 클라우드에 대한 지점 간 연결을 갖게 됩니다. 고객은 각 퍼블릭 클라우드에 대해 별도의 BGP 연결을 설정합니다. 한 클라우드 공급자가 수신한 경로는 기본적으로 다른 클라우드 공급자에게 보급됩니다. 각 클라우드 공급자는 서로 다른 접두사 제한을 가지므로 경로를 광고하는 동안 고객은 이러한 제한을 처리해야 합니다. 고객은 다른 퍼블릭 클라우드의 경로를 광고하는 동안 Microsoft와 함께 일반적인 BGP 노브를 사용할 수 있습니다.

## <a name="direct-connection-with-expressroute"></a>익스프레스루트와 직접 연결

고객은 ExpressRoute를 클라우드 공급자의 직접 연결 제품에 직접 연결할 수 있습니다. 두 개의 클라우드 공급자가 다시 연결되고 BGP가 라우터 간에 직접 설정됩니다. 이러한 유형의 연결은 현재 오라클에서 사용할 수 있습니다.

## <a name="site-to-site-vpn"></a>사이트 간 VPN

고객은 인터넷을 활용하여 Azure의 인스턴스를 다른 퍼블릭 클라우드와 연결할 수 있습니다. 거의 모든 클라우드 공급자는 사이트 간 VPN 기능을 제공합니다. 그러나 특정 변종의 부족으로 인해 비호환성이 있을 수 있습니다. 예를 들어 일부 클라우드 공급자는 IKEv1만 지원하므로 해당 클라우드에 필요한 VPN 종료 끝점이 있습니다. IKEv2를 지원하는 클라우드 공급자의 경우 두 클라우드 공급자의 VPN 게이트웨이 간에 직접 터널을 설치할 수 있습니다.

사이트 간 VPN은 처리량이 높고 대기 시간이 짧은 솔루션으로 간주되지 않습니다. 그러나 물리적 연결에 대한 백업으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
익스프레스루트 및 가상 네트워크 연결에 대한 자세한 내용은 [ExpressRoute FAQ를][ER-FAQ] 참조하십시오.

Azure와 Oracle 간의 연결을 위해 [Azure와 Oracle 클라우드 간의 직접 연결 설정][ER-OCI] 을 참조하십시오.

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



