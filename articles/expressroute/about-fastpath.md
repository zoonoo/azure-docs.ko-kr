---
title: Azure 익스프레스루트 패스트패스 소개
description: 게이트웨이를 우회하여 네트워크 트래픽을 전송하는 Azure ExpressRoute FastPath에 대해 알아보기
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282854"
---
# <a name="about-expressroute-fastpath"></a>ExpressRoute FastPath 정보

ExpressRoute 가상 네트워크 게이트웨이는 네트워크 경로를 교환하고 네트워크 트래픽을 라우팅하도록 설계되었습니다. FastPath는 온-프레미스 네트워크와 가상 네트워크 간의 데이터 경로 성능을 향상시내도록 설계되었습니다. 이 옵션을 사용하면 FastPath는 게이트웨이를 우회하여 가상 네트워크의 가상 컴퓨터로 네트워크 트래픽을 직접 보냅니다.

## <a name="requirements"></a>요구 사항

### <a name="circuits"></a>회로

패스트 패스는 모든 익스프레스 루트 회로에서 사용할 수 있습니다.

### <a name="gateways"></a>게이트웨이

FastPath는 여전히 가상 네트워크와 온-프레미스 네트워크 간의 경로를 교환하기 위해 가상 네트워크 게이트웨이를 만들어야 합니다. 성능 정보 및 게이트웨이 SCO를 포함한 가상 네트워크 게이트웨이 및 ExpressRoute에 대한 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이를](expressroute-about-virtual-network-gateways.md)참조하십시오.

FastPath를 구성하려면 가상 네트워크 게이트웨이가 다음 중 하나여야 합니다.

* 울트라 퍼포먼스
* ErGw3AZ

## <a name="supported-features"></a>지원되는 기능

FastPath는 대부분의 구성을 지원하지만 다음 기능을 지원하지는 않습니다.

* 게이트웨이 서브넷의 UDR: 가상 네트워크의 게이트웨이 서브넷에 UDR을 적용하면 온-프레미스 네트워크의 네트워크 트래픽이 가상 네트워크 게이트웨이로 계속 전송됩니다.

* VNet 피어링: ExpressRoute에 연결된 가상 네트워크로 피어링된 다른 가상 네트워크가 있는 경우 온-프레미스 네트워크에서 다른 가상 네트워크(예: 소위 "Spoke" VNet)로의 네트워크 트래픽이 가상 네트워크로 계속 전송됩니다. 게이트웨이. 해결 방법은 모든 가상 네트워크를 ExpressRoute 회로에 직접 연결하는 것입니다.

* 기본 로드 밸러서: 가상 네트워크에 기본 내부 로드 밸러서를 배포하거나 가상 네트워크에 배포하는 Azure PaaS 서비스를 사용하는 경우 기본 내부 로드 밸러서를 사용하는 경우 온-프레미스 네트워크에서 호스팅되는 가상 IP로의 네트워크 트래픽이 기본 로드 밸러서가 가상 네트워크 게이트웨이로 전송됩니다. 해결책은 기본 로드 밸러블러를 [표준 로드 밸레인저로](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)업그레이드하는 것입니다.

* 개인 링크: 온-프레미스 네트워크에서 가상 네트워크의 [개인 끝점에](../private-link/private-link-overview.md) 연결하면 연결은 가상 네트워크 게이트웨이를 통해 전달됩니다.
 
## <a name="next-steps"></a>다음 단계

FastPath를 사용하려면 [가상 네트워크와 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)연결을 참조하십시오.
