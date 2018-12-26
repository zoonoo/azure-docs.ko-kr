---
title: Azure Virtual WAN 개요 | Microsoft Docs
description: Virtual WAN 자동화된 확장성 있는 분기 간 연결에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/25/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: c2edb821eb8bd9a5da7a6cce81269e7d3f611722
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869894"
---
# <a name="what-is-azure-virtual-wan"></a>Azure Virtual WAN이란?

Azure Virtual WAN은 Azure를 통해 최적화 및 자동화된 분기 간 연결을 제공하는 네트워킹 서비스입니다. Virtual WAN을 사용하여 Azure와 통신하도록 분기 디바이스를 연결 및 구성할 수 있습니다. 수동으로 또는 Virtual WAN 파트너를 통해 선호 파트너 디바이스를 사용하여 이 작업을 수행할 수 있습니다. 자세한 내용은 [선호 파트너](https://go.microsoft.com/fwlink/p/?linkid=2019615) 문서를 참조하세요. 선호 파트너 디바이스는 쉽게 사용할 수 있고, 간편하게 연결할 수 있으며, 구성을 관리할 수 있습니다. Azure WAN 기본 제공 대시보드는 시간을 절약하는 데 도움이 되는 문제 해결 정보를 즉시 제공하며, 대규모 연결을 확인하는 간편한 방법도 제공합니다.

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwan.png)

이 문서에서는 Azure 및 비 Azure 워크로드의 네트워크 연결에 대한 빠른 보기를 제공합니다. Virtual WAN은 다음과 같은 이점을 제공합니다.

* **허브 스포크에서 통합된 연결 솔루션:** 온-프레미스 사이트와 Azure Hub 간의 사이트 간 구성과 연결을 자동화합니다.
* **자동화된 스포크 설치 및 구성:** Azure 허브로 가상 네트워크 및 워크로드를 원활하게 연결합니다.
* **직관적인 문제 해결:** Azure 내에서 종단 간 흐름을 보고 이 정보를 사용하여 필요한 작업을 수행할 수 있습니다.

## <a name="s2s"></a>사이트 간 연결

Virtual WAN을 사용하여 사이트 간 연결을 만들려면 [Virtual WAN 파트너](virtual-wan-locations-partners.md)를 통해 이동하거나 수동으로 연결을 만들 수 있습니다.

### <a name="s2spartner"></a>Virtual WAN 파트너와 작업

Virtual WAN 파트너 사용 시의 프로세스는 다음과 같습니다.

1. 분기 디바이스(VPN/SDWAN) 컨트롤러가 [Azure 서비스 주체](../active-directory/develop/howto-create-service-principal-portal.md)를 사용하여 Azure에 사이트 중심 정보를 내보내도록 인증됩니다.
2. 분기 디바이스(VPN/SDWAN) 컨트롤러는 Azure 연결 구성을 가져오고 로컬 디바이스를 업데이트합니다. 구성 다운로드, 온-프레미스 VPN 디바이스의 편집 및 업데이트를 자동화합니다.
3. 디바이스에 올바른 Azure 구성이 적용되면 Azure WAN에 대한 사이트 간 연결(활성 터널 2개)이 설정됩니다. Azure는 IKEv1 및 IKEv2를 둘 다 지원합니다. BGP는 선택 사항입니다.


선호 파트너를 사용하지 않으려는 경우 연결을 수동으로 구성할 수 있습니다. [Virtual WAN을 사용하여 사이트 간 연결 만들기](virtual-wan-site-to-site-portal.md)를 참조하세요.

## <a name="p2s"></a>지점 및 사이트 간 연결(미리 보기)

P2S(지점 및 사이트 간) 연결을 사용하면 개별 클라이언트 컴퓨터에서 가상 허브에 대한 안전한 연결을 만들 수 있습니다. P2S 연결은 클라이언트 컴퓨터에서 시작하여 설정됩니다. 이 솔루션은 집 또는 회의실과 같은 원격 위치에서 연결하려는 재택 근무자에게 유용합니다. 또한 P2S VPN은 연결해야 하는 클라이언트가 몇 개만 있는 경우 S2S VPN 대신 사용할 수 있는 유용한 솔루션입니다.

연결을 수동으로 만들려면 [Virtual WAN을 사용하여 지점 및 사이트 간 연결 만들기](virtual-wan-point-to-site-portal.md)를 참조하세요.

## <a name="er"></a>ExpressRoute 연결(미리 보기)

연결을 수동으로 만들려면 [Virtual WAN을 사용하여 ExpressRoute 연결 만들기](virtual-wan-expressroute-portal.md)를 참조하세요.


## <a name="resources"></a>Virtual WAN 리소스

종단 간 가상 WAN을 구성하려면 다음 리소스를 만듭니다.

* **virtualWAN:** virtualWAN 리소스는 Azure 네트워크의 가상 오버레이를 나타내며 여러 리소스의 컬렉션입니다. 가상 WAN 내에서 가지려는 모든 가상 허브에 대한 링크를 포함합니다. Virtual WAN 리소스는 서로 격리되며 공통의 허브를 포함할 수 없습니다. Virtual WAN에서 가상 허브는 서로 통신하지 않습니다. ‘분기 간 트래픽 허용’ 속성을 사용하면 VPN 사이트 간에, 그리고 VPN 사이트와 ExpressRoute 사용 사이트 간에 트래픽을 전송할 수 있습니다. Azure Virtual WAN의 ExpressRoute는 현재 미리 보기로 제공됩니다.

* **사이트:** vpnsite로 알려진 사이트 리소스는 온-프레미스 VPN 장치 및 해당 설정을 나타냅니다. Virtual WAN 파트너와 작업하여 이 정보를 Azure로 자동으로 내보내는 기본 제공 솔루션을 갖습니다.

* **허브:** 가상 허브는 Microsoft에서 관리하는 가상 네트워크입니다. 허브는 온-프레미스 네트워크(vpnsite)에서 연결을 활성화하는 다양한 서비스 엔드포인트를 포함합니다. 허브는 지역에서 네트워크의 핵심입니다. Azure 지역당 하나의 허브만 있을 수 있습니다. Azure Portal을 사용하여 허브를 만들면 가상 허브 VNet 및 가상 허브 vpngateway가 생성됩니다.

  허브 게이트웨이는 ExpressRoute 및 VPN Gateway에 사용하는 가상 네트워크 게이트웨이와 동일하지 않습니다. 예를 들어 Virtual WAN을 사용할 때 온-프레미스 사이트에서 VNet으로 직접 사이트 간 연결을 만들지 않습니다. 대신 허브로 사이트 간 연결을 만듭니다. 트래픽은 항상 허브 게이트웨이를 통해 이동합니다. VNet에 자체 가상 네트워크 게이트웨이가 필요하지 않다는 것을 의미합니다. Virtual WAN을 사용하면 VNet은 가상 허브와 가상 허브 게이트웨이를 통해 쉽게 크기 조정을 활용할 수 있습니다. 

* **허브 가상 네트워크 연결:** 허브 가상 네트워크 연결 리소스는 가상 네트워크에 허브를 원활하게 연결하는 데 사용됩니다. 이 때 동일한 허브 지역 내에 있는 가상 네트워크에 연결할 수 있습니다.

* **허브 경로 테이블:** 가상 허브 경로를 만들어 가상 허브 경로 테이블에 적용할 수 있습니다. 가상 허브 경로 테이블에 여러 경로를 적용할 수 있습니다.

## <a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]


## <a name="next-steps"></a>다음 단계

[Virtual WAN 파트너 및 위치](virtual-wan-locations-partners.md) 페이지를 확인합니다.
