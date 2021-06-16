---
title: 'Azure ExpressRoute: Global Reach를 사용하여 Microsoft Cloud에 연결'
description: 온-프레미스 네트워크 간 프라이빗 네트워크를 설정하기 위해 Azure ExpressRoute Global Reach와 ExpressRoute 회로를 함께 연결하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 512f2529e5426e97187f5cb2840dcab27a32c1d6
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111541602"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute는 온-프레미스 네트워크를 Microsoft Cloud에 연결하기 위한 프라이빗하고 복원력 있는 방법입니다. 프라이빗 데이터 센터 또는 회사 네트워크에서 Azure, Microsoft 365와 같은 다양한 Microsoft 클라우드 서비스에 액세스할 수 있습니다. 예를 들어, 실리콘밸리의 ExpressRoute 회로를 사용하는 샌프란시스코 지사와, 런던의 ExpressRoute 회로를 사용하는 런던 지사가 있을 수 있습니다. 두 지점 모두 미국 서부 및 영국 남부의 Azure 리소스에 대한 고속 연결이 있습니다. 그러나 지점은 서로 직접 연결하여 데이터를 보낼 수는 없습니다. 즉, 10.0.1.0/24는 10.0.3.0/24 및 10.0.4.0/24 네트워크에는 데이터를 보낼 수 있지만 10.0.2.0/24 네트워크에는 보낼 수 없습니다.

![Express Route Global Reach와 함께 연결되지 않은 회로를 보여주는 다이어그램입니다.][1]

**ExpressRoute Global Reach** 를 사용하면 온-프레미스 네트워크 간의 프라이빗 네트워크를 설정하기 위해 ExpressRoute 회로를 함께 연결할 수 있습니다. 위의 예제에서 ExpressRoute Global Reach가 추가됨에 따라 샌프란시스코 사무실(10.0.1.0/24)은 기존 ExpressRoute 회로 및 Microsoft의 글로벌 네트워크를 통해 런던 사무실(10.0.2.0/24)과 데이터를 교환할 수 있습니다. 

![Express Route Global Reach와 함께 연결된 회로를 보여주는 다이어그램입니다.][2]

## <a name="use-case"></a>사용 사례
ExpressRoute Global Reach는 서비스 공급자의 WAN 구현을 보완하고 전 세계에 있는 지점을 연결하도록 설계되었습니다. 예를 들어, 서비스 공급자가 미국에서 주로 운영되고 미국 내 모든 지점을 연결했지만, 서비스 공급자가 일본과 홍콩에서는 운영되지 않는 경우, ExpressRoute Global Reach를 사용하여 로컬 서비스 공급자와 작업할 수 있고 Microsoft는 ExpressRoute와 글로벌 네트워크를 사용하여 해당 지역의 지점과 미국 내 지점을 연결합니다.

![Express Route Global Reach의 사용 사례를 보여주는 다이어그램입니다.][3]

## <a name="availability"></a>가용성 
ExpressRoute Global Reach는 다음 국가에서 지원됩니다. 

> [!NOTE] 
> [서로 다른 지리적 지역](expressroute-locations-providers.md#locations) 간에 ExpressRoute Global Reach를 사용하도록 설정하려면 회로가 **프리미엄 SKU** 여야 합니다.

* 오스트레일리아
* Canada
* 덴마크
* 프랑스
* 독일
* 홍콩 특별행정구
* 인도
* 아일랜드
* 일본
* 한국
* 네덜란드
* 뉴질랜드
* 노르웨이
* 싱가포르
* 남아프리카 공화국(요하네스버그만 해당)
* 스웨덴
* 스위스
* 영국
* 미국

## <a name="next-steps"></a>다음 단계
- [Global Reach FAQ](expressroute-faqs.md#globalreach)를 봅니다.
- [Global Reach를 사용하도록 설정](expressroute-howto-set-global-reach.md)하는 방법을 알아봅니다.
- [ExpressRoute 회로를 가상 네트워크에 연결](expressroute-howto-linkvnet-arm.md)하는 방법을 알아봅니다.

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "글로벌 환경을 사용하지 않는 다이어그램"
[2]: ./media/expressroute-global-reach/2.png "글로벌 환경을 사용하는 다이어그램"
[3]: ./media/expressroute-global-reach/3.png "글로벌 환경의 사용 사례"
