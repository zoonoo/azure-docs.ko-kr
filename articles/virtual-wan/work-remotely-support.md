---
title: Azure 가상 WAN 및 원격으로 작업
description: 이 페이지에서는 COVID-19 전염병으로 인해 Azure Virtual WAN을 활용하여 원격으로 작업을 활성화하는 방법에 대해 설명합니다.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337138"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure 가상 WAN 및 원격 작업 지원

>[!NOTE]
>이 문서에서는 Azure Virtual WAN, Azure, Microsoft 네트워크 및 Azure 파트너 에코시스템을 활용하여 원격으로 작업하고 COVID-19 위기로 인해 직면하고 있는 네트워크 문제를 완화하는 방법에 대해 설명합니다.
>

원격 사용자에게 연결을 제공하기 위해 분주히 움직이고 있습니까?
갑자기 계획했던 것 이상으로 급증하는 사용자를 지원해야 할 필요가 있습니까?
사용자가 집에서 연결하고 클라우드에 액세스할 뿐만 아니라 온-프레미스에 연결할 수 있어야 합니까?
원격 사용자가 개인 WAN 뒤에 있는 리소스에 연결할 수 있어야 합니까?
사용자가 리전 간 연결을 설정할 필요 없이 클라우드 내 리소스에 액세스할 필요가 있습니까?
이러한 글로벌 전염병으로 인해 우리 주변에 전례 없는 변화가 일어나고 있으므로 Azure Virtual WAN 팀은 연결 요구 사항을 충족할 수 있도록 여기에 있습니다.

Azure Virtual WAN은 단일 운영 인터페이스를 제공하기 위해 많은 네트워킹, 보안 및 라우팅 기능을 함께 제공하는 네트워킹 서비스입니다. 이러한 기능에는 지점 연결(SD-WAN 또는 VPN CPE와 같은 가상 WAN 파트너 장치의 연결 자동화), 사이트 간 VPN 연결, 원격 사용자 VPN(Point-to-Site) 연결, 개인(ExpressRoute) 연결, 인트라 클라우드 연결(가상 네트워크의 전이 연결), VPN 익스프레스라우팅 상호 연결, 라우팅, Azure 방화벽, 개인 연결용 암호화 등 가상 WAN을 사용하기 위해 이러한 사용 사례를 모두 사용할 필요는 없습니다. 하나의 사용 사례로 시작하고 발전함에 따라 네트워크를 조정할 수 있습니다.

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwan1.png)

이제 원격 사용자에 대해 이야기하면 네트워크를 시작하고 실행하는 데 필요한 사항을 살펴 볼 수 있습니다.

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>원격 사용자 연결 설정

IPsec/IKE(IKEv2) 또는 OpenVPN 연결을 통해 Azure의 리소스에 연결할 수 있습니다. 이러한 유형의 연결에는 원격 사용자에 대해 VPN 클라이언트를 구성해야 합니다. 이 클라이언트는 [Azure VPN 클라이언트](https://go.microsoft.com/fwlink/?linkid=2117554) 또는 OpenVPN 클라이언트 또는 IKEv2를 지원하는 모든 클라이언트일 수 있습니다. 자세한 내용은 [지점 및 사이트 간 연결 만들기](virtual-wan-point-to-site-portal.md)를 참조하세요.

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>원격 사용자에서 온-프레미스로의 연결

여기에는 두 가지 옵션이 있습니다.

* 기존 VPN 장치로 사이트 간 연결을 설정합니다. IPsec VPN 장치를 Azure Virtual WAN 허브에 연결하면 지점 간 사용자 VPN(원격 사용자)과 사이트 간 VPN 간의 상호 연결이 자동으로 수행됩니다. 온프레미스 VPN 장치에서 Azure Virtual WAN으로 사이트 간 VPN을 설정하는 방법에 대한 자세한 내용은 [가상 WAN을 사용하여 사이트 간 연결 만들기를](virtual-wan-site-to-site-portal.md)참조하십시오.

* 익스프레스루트 회로를 가상 WAN 허브에 연결합니다. ExpressRoute 회로를 연결하려면 가상 WAN에 ExpressRoute 게이트웨이를 배포해야 합니다. 사이트를 배포하는 즉시 지점 간 사용자 VPN과 ExpressRoute 사용자 간의 상호 연결이 자동으로 수행됩니다. 익스프레스Route 연결을 만들려면 [가상 WAN을 사용하여 익스프레스라우팅 연결 만들기를](virtual-wan-expressroute-portal.md)참조하십시오. 기존 ExpressRoute 회로를 사용하여 Azure 가상 WAN에 연결할 수 있습니다.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>기존 기본 가상 WAN 고객

기본 가상 WAN은 사이트 간 VPN만 제공합니다. 원격 사용자가 연결하려면 가상 WAN을 표준 가상 WAN으로 업그레이드해야 합니다. 가상 WAN을 업그레이드하는 단계는 [기본에서 표준으로 가상 WAN 업그레이드를 참조하세요.](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>추가 정보

가상 WAN은 지역/위치당 하나의 허브를 지원합니다. 위치 정보는 [Virtual WAN 파트너 및 위치](virtual-wan-locations-partners.md) 문서를 참조하세요. 각 허브는 최대 10,000개의 원격 사용자 연결, 1,000개의 분기 연결, 4개의 ExpressRoute 회로 및 최대 500개의 가상 네트워크 연결을 지원합니다. 원격 사용자를 확장할 때 질문이 있는 경우 azurevirtualwan@microsoft.com에 이메일을 보내 도움을 요청하는 것을 주저하지 마십시오. 기술 지원이 필요한 경우 Azure 포털에서 지원 티켓을 열고 도움을 받을 수 있습니다.

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>다음 단계

가상 WAN에 대한 자세한 내용은 [가상 WAN 개요를](virtual-wan-about.md) 참조하십시오.