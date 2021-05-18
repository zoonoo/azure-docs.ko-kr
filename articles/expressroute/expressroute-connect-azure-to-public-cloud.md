---
title: 퍼블릭 클라우드에 Azure 연결 | Microsoft Docs
description: 다른 퍼블릭 클라우드에 Azure를 연결하는 다양한 방법을 설명합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: fb4c34345ab902e243f22eae06fd5ea174fd2992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202194"
---
# <a name="connecting-azure-with-public-clouds"></a>퍼블릭 클라우드와 Azure 연결

많은 기업은 비즈니스 및 기술 목표 때문에 다중 클라우드 전략을 추구하고 있습니다. 여기에는 비용, 유연성, 기능 가용성, 중복성, 데이터 주권 등이 포함됩니다.이 전략은 두 클라우드를 최대한 활용하는 데 도움이 됩니다. 

이 방법을 사용할 경우 기업에서 네트워크 및 애플리케이션 아키텍처 과 관련된 문제도 발생합니다. 해당 문제로 대기 시간, 데이터 처리량 등이 포함됩니다. 문제를 해결하기 위해 고객은 여러 클라우드에 직접 연결하려고 합니다. 일부 서비스 공급자는 여러 클라우드 공급자를 연결하는 솔루션을 고객에게 제공합니다. 다른 경우에는 고객이 고유 라우터를 배포하여 여러 퍼블릭 클라우드를 연결할 수 있습니다.
## <a name="connectivity-via-expressroute"></a>ExpressRoute를 통한 연결
ExpressRoute를 사용하면 연결 공급자가 지원하는 프라이빗 연결을 통해 고객이 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 통해 고객은 Microsoft 클라우드 서비스에 연결할 수 있습니다.

ExpressRoute를 통해 연결하는 방법에는 세 가지가 있습니다.

1. 계층3 공급자
2. 계층2 공급자
3. 직접 연결

### <a name="layer3-provider"></a>계층3 공급자

계층3 공급자는 일반적으로 IP VPN 또는 MPLS VPN 공급자라고 합니다. 고객은 데이터 센터, 분기, 클라우드 간 다중 지점 연결을 위해 해당 공급자를 활용합니다. 고객은 BGP 또는 정적 기본 경로를 통해 L3 공급자에 연결합니다. 서비스 공급자는 고객 사이트, 데이터 센터, 퍼블릭 클라우드 간 경로를 보급합니다. 
 
계층3 공급자를 통해 연결하는 경우 Microsoft는 BGP를 통해 서비스 공급자에 연결하는 고객 VNET 경로를 보급합니다. 공급자는 두 가지 구현을 사용할 수 있습니다.

![계층3 공급자가 표시된 다이어그램](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

모든 클라우드 공급자의 트래픽이 고객 라우터에 도착하는 경우 공급자는 각 클라우드 공급자를 개별 VRF에 배치할 수 있습니다. 고객이 서비스 공급자를 사용하여 BGP를 실행하는 경우 해당 경로가 기본적으로 다른 클라우드 공급자에게 다시 보급됩니다. 

서비스 공급자가 모든 클라우드 공급자를 동일한 VRF에 배치하는 경우 경로가 서비스 공급자에서 다른 클라우드 공급자로 직접 보급됩니다. 이 경우 eBGP 경로가 기본적으로 다른 eBGP 인접 항목에 보급되는 표준 BGP 작업을 가정합니다.

퍼블릭 클라우드마다 접두사 한도가 다르기 때문에 경로를 배포하는 동안 서비스 공급자는 신중하게 경로를 배포해야 합니다.

### <a name="layer2-provider-and-direct-connection"></a>계층2 공급자 및 직접 연결

두 모델의 물리적 연결은 서로 다르지만 계층3에서 MSEE와 고객 라우터 간에 직접 BGP가 설정됩니다. ExpressRoute Direct의 경우 고객이 MSEE에 직접 연결합니다. 계층2의 경우 서비스 공급자가 고객 프레미스에서 클라우드로 VLAN을 확장합니다. 고객은 계층2 네트워크를 기반으로 해서 BGP를 실행하여 해당 DC를 클라우드에 연결합니다.
![계층2 공급자 및 직접 연결이 표시된 다이어그램](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
두 경우 모두, 고객은 각 퍼블릭 클라우드에 대한 지점 간 연결을 갖습니다. 고객은 각 퍼블릭 클라우드에 대해 개별 BGP 연결을 설정합니다. 하나의 클라우드 공급자가 수신한 경로는 기본적으로 다른 클라우드 공급자에게 보급됩니다. 클라우드 공급자마다 접두사 한도가 서로 다르기 때문에 경로를 보급하는 동안 고객은 해당 한도에 주의해야 합니다. 고객은 다른 퍼블릭 클라우드의 경로를 보급하는 동안 Microsoft에서 일반적인 BGP 노브를 사용할 수 있습니다.

## <a name="direct-connection-with-expressroute"></a>ExpressRoute를 사용한 직접 연결

고객은 클라우드 공급자의 직접 연결 제안에 ExpressRoute를 직접 연결할 수 있습니다. 두 클라우드 공급자가 직접 연결되고, 라우터 간에 직접 BGP가 설정됩니다. 이 유형의 연결은 현재 Oracle에서 사용할 수 있습니다.

## <a name="site-to-site-vpn"></a>사이트 간 VPN

고객은 인터넷을 활용하여 다른 퍼블릭 클라우드와 Azure의 인스턴스를 연결할 수 있습니다. 거의 모든 클라우드 공급자는 사이트 간 VPN 기능을 제공합니다. 그러나 특정 변형이 없어서 비호환성이 발생할 수 있습니다. 예를 들어 일부 클라우드 공급자는 IKEv1만 지원하므로 해당 클라우드에 VPN 종료 엔드포인트가 있어야 합니다. IKEv2를 지원하는 클라우드 공급자의 경우 두 클라우드 공급자의 VPN 게이트웨이 간에 직접 터널을 설정할 수 있습니다.

사이트 간 VPN은 높은 처리량 및 낮은 대기 시간 솔루션으로 간주되지 않습니다. 그러나 물리적 연결에 대한 백업으로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
ExpressRoute 및 가상 네트워크 연결에 대한 추가 질문이 있으면 [ExpressRoute FAQ][ER-FAQ]를 참조하세요.

Azure와 Oracle 간 연결에 대한 자세한 내용은 [Azure와 Oracle Cloud 간 직접 연결 설정][ER-OCI]을 참조하세요.

<!--Link References-->
[ER-FAQ]: ./expressroute-faqs.md
[ER-OCI]: ../virtual-machines/workloads/oracle/configure-azure-oci-networking.md