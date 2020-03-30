---
title: Azure 교차 네트워크 연결
description: 이 페이지에서는 Azure 네트워킹 기능을 기반으로 하는 교차 네트워크 연결 및 솔루션에 대한 응용 프로그램 시나리오에 대해 설명합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 48ec26cc98310dfeb61aa17018c940b431cfbcee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644259"
---
# <a name="cross-network-connectivity"></a>네트워크 간 연결

Fabrikam Inc.는 미국 동부에 있는 대규모의 실제 Azure 배포 기업입니다. Fabrikam은 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다. 마찬가지로 Contoso Ltd.는 미국 서부에 현재 상태 및 Azure 배포를 보유하고 있습니다. Contoso는 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다.  

파브리캄, 콘토소 주식회사 인수 합병 후 Fabrikam은 네트워크를 상호 연결하고자 합니다. 다음 그림은 이 시나리오를 보여 줍니다.

 [![1]][1개]

위의 그림 중간에 있는 파선 화살표는 원하는 네트워크 상호 연결을 나타냅니다. 특히, 원하는 세 가지 유형의 교차 연결이 있습니다: 1) 파브리캄과 Contoso VNets 교차 연결, 2) 크로스 리전 온-프레미스 및 VNets 교차 연결 (즉, Contoso VNet에 Fabrikam 온-프레미스 네트워크를 연결 하 고 Contoso 연결 파브리캄 VNet에 온-프레미스 네트워크), 3) 파브리캄과 콘토소 온-프레미스 네트워크 크로스 연결. 

다음 표는 합병 전에 Contoso Ltd.의 ExpressRoute 의 개인 피어링의 경로 테이블을 보여 주며, 이 표는 Contoso Ltd.의 익스프레스루트(ExpressRoute)의 경로 테이블을 보여 주며, 합병 전에 보여 주어도 됩니다.

[![2]][2]

다음 표에서는 합병 전에 Contoso 구독에서 VM의 유효 경로를 보여 주며, 이 에 대한 것입니다. 테이블에 따라 VNet의 VM은 기본 주소 와 는 별도로 VNet 주소 공간과 Contoso 온-프레미스 네트워크를 인식합니다. 

[![4]][4]

다음 표는 합병 전에 Fabrikam Inc.의 ExpressRoute의 개인 피어링의 경로 테이블을 보여 주며,

[![3]][3]

다음 표에서는 합병 전에 Fabrikam 구독에서 VM의 유효 경로를 보여 주며, 이 에 대한 것입니다. 테이블에 따라 VNet의 VM은 기본 주소 와 는 별도로 VNet 주소 공간과 Fabrikam 온-프레미스 네트워크를 인식합니다.

[![5]][5]

이 문서에서는 단계별로 살펴보고 다음 Azure 네트워크 기능을 사용하여 원하는 교차 연결을 달성하는 방법에 대해 설명하겠습니다.

* [가상 네트워크 피어링][Virtual network peering] 
* [가상 네트워크 익스프레스루트 연결][connection]
* [글로벌 도달 범위][Global Reach] 

## <a name="cross-connecting-vnets"></a>VNet 교차 연결

가상 네트워크 피어링(VNet 피어링)은 두 개의 가상 네트워크를 연결할 때 가장 최적화되고 최상의 네트워크 성능을 제공합니다. VNet 피어링은 동일한 Azure 지역(일반적으로 VNet 피어링이라고 함)과 두 개의 서로 다른 Azure 지역(일반적으로 글로벌 VNet 피어링이라고 함)에서 두 개의 VNet을 피어링하는 것을 지원합니다. 

Contoso의 VNet과 Fabrikam Azure 구독 간에 글로벌 VNet 피어링을 구성해 보겠습니다. 두 가상 네트워크 간에 피어링을 만드는 방법은 [가상 네트워크 피어링][Configure VNet peering] 문서 만들기를 참조하십시오.

다음 그림은 전역 VNet 피어링을 구성한 후 네트워크 아키텍처를 보여 주며,

[![6]][6]

다음 표에서는 Contoso 구독 VM에 알려진 경로를 보여 주며, 이 루트는 다음과 같은 것입니다. 테이블의 마지막 항목에 주의하십시오. 이 항목은 가상 네트워크를 상호 연결한 결과입니다.

[![7]][7]

다음 표는 Fabrikam 구독 VM에 알려진 경로를 보여 주며, 이 표는 Fabrikam 구독 VM에 알려진 경로입니다. 테이블의 마지막 항목에 주의하십시오. 이 항목은 가상 네트워크를 상호 연결한 결과입니다.

[![8]][8]

VNet 피어링은 두 개의 가상 네트워크를 직접 연결합니다(위의 두 표에 *VNetGlobalPeering* 항목에 대한 다음 홉이 없음 참조).

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>온-프레미스 네트워크에 VNet 교차 연결

우리는 여러 가상 네트워크에 ExpressRoute 회로를 연결할 수 있습니다. ExpressRoute 회로에 연결할 수 있는 최대 가상 네트워크 수에 대한 [구독 및 서비스 제한을][Subscription limits] 참조하십시오. 

파브리캄 익스프레스루트 회로를 Contoso 구독 VNet에 연결하고 Contoso ExpressRoute 회로를 Fabrikam 구독 VNet에 연결하여 가상 네트워크와 온-프레미스 네트워크 간의 교차 연결을 활성화해 보겠습니다. 가상 네트워크를 다른 구독의 ExpressRoute 회로에 연결하려면 권한 부여를 만들고 사용해야 합니다.  이 문서 보기: [가상 네트워크를 ExpressRoute 회로에 연결합니다.][Connect-ER-VNet]

다음 그림은 가상 네트워크에 대한 ExpressRoute 교차 연결을 구성한 후 네트워크 아키텍처를 보여 주며 있습니다.

[![9]][9]

다음 표는 ExpressRoute를 통해 가상 네트워크를 온-프레미스 네트워크에 교차 연결한 후 Contoso Ltd.의 ExpressRoute의 개인 피어링의 경로 테이블을 보여 주었습니다. 경로 테이블에 두 가상 네트워크에 속하는 경로가 있는지 확인합니다.

[![10]][10]

다음 표는 ExpressRoute를 통해 가상 네트워크를 온-프레미스 네트워크에 교차 연결한 후 Fabrikam Inc.의 ExpressRoute의 개인 피어링의 경로 테이블을 보여 주었습니다. 경로 테이블에 두 가상 네트워크에 속하는 경로가 있는지 확인합니다.

[![11]][11]

다음 표에서는 Contoso 구독 VM에 알려진 경로를 보여 주며, 이 루트는 다음과 같은 것입니다. 테이블의 *가상 네트워크 게이트웨이* 항목에 주의하십시오. VM은 온-프레미스 네트워크 모두에 대한 경로를 봅니다.

[![12]][12]

다음 표는 Fabrikam 구독 VM에 알려진 경로를 보여 주며, 이 표는 Fabrikam 구독 VM에 알려진 경로입니다. 테이블의 *가상 네트워크 게이트웨이* 항목에 주의하십시오. VM은 온-프레미스 네트워크 모두에 대한 경로를 봅니다.

[![13]][13]

>[!NOTE]
>Fabrikam 및/또는 Contoso 구독에서는 각 허브 VNet에 VNet을 스포크할 수도 있습니다(허브 및 스포크 디자인은 이 문서의 아키텍처 다이어그램에 설명되어 있지 않음). 허브 VNet 게이트웨이와 ExpressRoute 간의 교차 연결을 통해 동부 및 서부 허브와 스포크 간의 통신도 가능합니다.
>

## <a name="cross-connecting-on-premises-networks"></a>온-프레미스 네트워크 교차 연결

ExpressRoute 글로벌 리치는 서로 다른 ExpressRoute 회로에 연결된 온-프레미스 네트워크 간의 연결을 제공합니다. 콘토소와 파브리캄 익스프레스루트 회로 사이의 글로벌 리치를 구성해 보겠습니다. ExpressRoute 회로는 서로 다른 구독에 있으므로 권한 부여를 만들고 사용해야 합니다. 단계별 지침은 [ExpressRoute 글로벌 리치 구성][Configure Global Reach] 문서를 참조하십시오.

다음 그림은 전역 도달 을 구성 한 후 네트워크 아키텍처를 보여줍니다.

[![14]][14]

다음 표는 전역 도달을 구성한 Contoso Ltd.의 ExpressRoute 의 개인 피어링의 경로 테이블을 보여 주며, 이 표는 라우트 테이블에 온-프레미스 네트워크에 모두 속하는 경로가 있는지 확인합니다. 

[![15]][15]

다음 표는 글로벌 도달을 구성한 후 Fabrikam Inc.의 ExpressRoute의 개인 피어링의 경로 테이블을 보여 주며, 라우트 테이블에 온-프레미스 네트워크에 모두 속하는 경로가 있는지 확인합니다.

[![16]][16]

## <a name="next-steps"></a>다음 단계

VNet 및 VNet 피어링에 대한 자세한 내용은 [가상 네트워크 FAQ를][VNet-FAQ]참조하십시오. 익스프레스루트 및 가상 네트워크 연결에 대한 자세한 내용은 [ExpressRoute FAQ를][ER-FAQ] 참조하십시오.

글로벌 도달 범위는 국가/지역별로 국가/지역으로 출시됩니다. 원하는 국가/지역에서 글로벌 도달을 사용할 수 있는지 확인하려면 [ExpressRoute 글로벌 도달][Global Reach]을 참조하세요.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "적용 시나리오"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "합병 전 Contoso ExpressRoute 경로 표"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "합병 전 파브리캄 익스프레스루트 루트 테이블"
합병 전 [4개의]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "Contoso VM 노선"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "합병 전 파브리캄 VM 노선"
[6]: ./media/cross-network-connectivity/vnet-peering.png "VNet 피어링 후의 아키텍처"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "VNet 피어링 후 7개의 Contoso VM 경로"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VNet 피어링 후 파브리캄 VM 경로"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "익스프레스루트 교차 연결 후 아키텍처"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "ExR 및 VNet을 교차 연결한 후 Contoso ExpressRoute 경로 표"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "ExR 및 VNet을 교차 연결한 후 파브리캄 익스프레스루트 루트 테이블"
 "ExR 및 VNet을 교차 연결한 후 12개의 Contoso VM 경로" [12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "ExR 및 VNet을 교차 연결한 후 Fabrikam VM 경로"
[14]: ./media/cross-network-connectivity/globalreach.png "글로벌 리치 를 구성한 후의 아키텍처"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "글로벌 도달 후 콘토소 익스프레스 루트 경로 테이블"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "글로벌 도달 후 파브리캄 익스프레스루트 루트 테이블"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq