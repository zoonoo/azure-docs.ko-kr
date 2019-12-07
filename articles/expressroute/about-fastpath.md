---
title: Azure Express 경로 정보 정보
description: 게이트웨이를 우회 하 여 네트워크 트래픽을 전송 하는 Azure Express 경로 FastPath에 대해 알아봅니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: cherylmc
ms.openlocfilehash: 6ff1dac312bcb4df1e1afc9679df09fc8a2b28ff
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897365"
---
# <a name="about-expressroute-fastpath"></a>Express 경로 정보

Express 경로 가상 네트워크 게이트웨이는 네트워크 경로를 교환 하 고 네트워크 트래픽을 라우팅하는 데 적합 합니다. FastPath는 온-프레미스 네트워크와 가상 네트워크 간의 데이터 경로 성능을 향상 시 키도 록 설계 되었습니다. 사용 하도록 설정 하면 FastPath는 게이트웨이를 우회 하 여 가상 네트워크의 가상 머신으로 네트워크 트래픽을 직접 보냅니다.

## <a name="requirements"></a>요구 사항

### <a name="circuits"></a>배선

FastPath는 모든 Express 경로 회로에서 사용할 수 있습니다.

### <a name="gateways"></a>게이트웨이

FastPath는 가상 네트워크와 온-프레미스 네트워크 간에 경로를 교환 하기 위해 가상 네트워크 게이트웨이를 만들어야 합니다. Virtual network 게이트웨이 및 Express 경로에 대 한 자세한 내용은 [express 경로 가상 네트워크 게이트웨이](expressroute-about-virtual-network-gateways.md)를 참조 하세요.

가상 네트워크 게이트웨이는 다음 중 하나 여야 합니다.

* Ultra Performance
* ErGw3AZ

## <a name="supported-features"></a>지원되는 기능

FastPath는 대부분의 구성을 지원 하지만 다음과 같은 기능은 지원 하지 않습니다.

* 게이트웨이 서브넷의 UDR: 가상 네트워크의 게이트웨이 서브넷에 UDR을 적용 하면 온-프레미스 네트워크의 네트워크 트래픽이 가상 네트워크 게이트웨이로 계속 전송 됩니다.

* VNet 피어 링: 피어 링에 연결 된 다른 가상 네트워크가 있는 경우 온-프레미스 네트워크에서 다른 가상 네트워크 (즉, "스포크" Vnet)로의 네트워크 트래픽은 계속 가상 네트워크로 전송 됩니다. 관문. 해결 방법은 모든 가상 네트워크를 Express 경로 회로에 직접 연결 하는 것입니다.

* 기본 Load Balancer: 가상 네트워크에 기본 내부 부하 분산 장치를 배포 하거나 가상 네트워크에 배포 하는 Azure PaaS 서비스에서 기본 내부 부하 분산 장치를 사용 하는 경우 온-프레미스 네트워크에서 호스트 되는 가상 Ip에 대 한 네트워크 트래픽이 기본 부하 분산 장치는 가상 네트워크 게이트웨이로 전송 됩니다. 이 솔루션은 기본 부하 분산 장치를 [표준 부하 분산](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)장치로 업그레이드 하는 것입니다.

* 개인 링크: 온-프레미스 네트워크에서 가상 네트워크의 [개인 끝점](../private-link/private-link-overview.md) 에 연결 하는 경우 연결은 가상 네트워크 게이트웨이를 통해 이동 합니다.
 
## <a name="next-steps"></a>다음 단계

FastPath를 사용 하도록 설정 하려면 [가상 네트워크를 Express 경로에 연결](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)을 참조 하세요.
