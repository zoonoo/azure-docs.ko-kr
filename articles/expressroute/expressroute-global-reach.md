---
title: 'Azure Express 경로: Global Reach를 사용 하 여 Microsoft 클라우드에 연결'
description: Azure Express 경로 Global Reach에서 Express 경로 회로를 함께 연결 하 여 온-프레미스 네트워크 간에 개인 네트워크를 만드는 방법에 대해 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: duau
ms.openlocfilehash: 8c6ed19a585bc1ebae65045cd1cc4c442f113597
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651486"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
Express 경로는 온-프레미스 네트워크를 Microsoft 클라우드에 연결 하는 개인적이 고 복원 력 있는 방법입니다. Azure와 같은 여러 Microsoft 클라우드 서비스에 액세스 하 고 개인 데이터 센터나 회사 네트워크에서 Microsoft 365 수 있습니다. 예를 들어, 실리콘밸리의 ExpressRoute 회로를 사용하는 샌프란시스코 지사와, 런던의 ExpressRoute 회로를 사용하는 런던 지사가 있을 수 있습니다. 두 지점에는 미국 서 부 및 영국 남부의 Azure 리소스에 대 한 고속 연결이 있습니다. 그러나 지점은 서로 직접 연결 하 여 데이터를 보낼 수 없습니다. 즉, 10.0.1.0/24는 10.0.3.0/24 및 10.0.4.0/24 네트워크에 데이터를 보낼 수 있지만 10.0.2.0/24 네트워크에는 보낼 수 없습니다.

![Express Route Global Reach와 함께 연결 되지 않은 회로를 보여 주는 다이어그램입니다.][1]

**ExpressRoute Global Reach**를 사용하면 온-프레미스 네트워크 간의 프라이빗 네트워크를 설정하기 위해 ExpressRoute 회로를 함께 연결할 수 있습니다. 위의 예제에서 ExpressRoute Global Reach가 추가됨에 따라 샌프란시스코 사무실(10.0.1.0/24)은 기존 ExpressRoute 회로 및 Microsoft의 글로벌 네트워크를 통해 런던 사무실(10.0.2.0/24)과 데이터를 교환할 수 있습니다. 

![Express Route Global Reach와 함께 연결 된 회로를 표시 하는 다이어그램입니다.][2]

## <a name="use-case"></a>사용 사례
ExpressRoute Global Reach는 서비스 공급자의 WAN 구현을 보완하고 전 세계에 있는 지점을 연결하도록 설계되었습니다. 예를 들어, 서비스 공급자가 미국에서 주로 운영되고 미국 내 모든 지점을 연결했지만, 서비스 공급자가 일본과 홍콩에서는 운영되지 않는 경우, ExpressRoute Global Reach를 사용하여 로컬 서비스 공급자와 작업할 수 있고 Microsoft는 ExpressRoute와 글로벌 네트워크를 사용하여 해당 지역의 지점과 미국 내 지점을 연결합니다.

![Express Route Global Reach의 사용 사례를 보여 주는 다이어그램입니다.][3]

## <a name="availability"></a>가용성 
Express 경로 Global Reach는 현재 Express 경로를 지 원하는 대부분의 지역에서 지원 됩니다. 현재 지원 되는 지역에 대 한 [express 경로 연결 공급자](expressroute-locations-providers.md#partners) 를 참조할 수 있습니다. 

> [!NOTE] 
> [서로 다른 지정 학적 지역](expressroute-locations-providers.md#locations)간에 express 경로 Global Reach를 사용 하도록 설정 하려면 회로가 **프리미엄 SKU**여야 합니다.

## <a name="next-steps"></a>다음 단계
- [GLOBAL REACH FAQ](expressroute-faqs.md#globalreach)를 확인 합니다.
- [Global Reach를 사용 하도록 설정](expressroute-howto-set-global-reach.md)하는 방법을 알아봅니다.
- [Express 경로 회로를 가상 네트워크에 연결](expressroute-howto-linkvnet-arm.md)하는 방법을 알아봅니다.

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "글로벌 환경을 사용하지 않는 다이어그램"
[2]: ./media/expressroute-global-reach/2.png "글로벌 환경을 사용하는 다이어그램"
[3]: ./media/expressroute-global-reach/3.png "글로벌 환경의 사용 사례"
