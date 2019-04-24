---
title: Azure 간 네트워크 연결 | Microsoft Docs
description: 이 페이지 간 네트워크 연결 및 Azure 네트워킹 기능을 기반으로 하는 솔루션에 대 한 응용 프로그램 시나리오를 설명 합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368879"
---
# <a name="cross-network-connectivity"></a>네트워크 간 연결

Fabrikam Inc.는 미국 동부에 있는 대규모의 실제 Azure 배포 기업입니다. Fabrikam은 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다. 마찬가지로, Contoso Ltd. 미국 서부의 Azure 배포를 현재 상태에 있습니다. Contoso는 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다.  

Fabrikam Inc.는 Contoso Ltd.를 합병했습니다. 합병한 후에 Fabrikam은 네트워크 상호 연결하려고 합니다. 다음 그림은 이 시나리오를 보여 줍니다.

 [![1]][1]

위의 그림 중간 점선으로 된 화살표는 원하는 네트워크 상호 연결을 나타냅니다. 특히, 세 가지 유형이 있습니다 교차 연결 필요한: 1) Fabrikam과 Contoso Vnet 간 연결, 2) 간 연결 (즉, Contoso VNet에 Fabrikam 온-프레미스 네트워크를 연결 및 Fabrikam VNet에 연결 하는 Contoso 온-프레미스 네트워크), 지역 온-프레미스 및 Vnet 간 3) Fabrikam 및 Contoso 온-프레미스 네트워크 간 연결 합니다. 

다음 표에서 ExpressRoute의 Contoso Ltd.를 병합 하기 전에 개인 피어 링 경로 테이블을 보여 줍니다.

[![2]][2]

다음 표에서 Contoso 구독을 병합 하기 전에 VM의 유효 경로 보여 줍니다. 테이블 당 VNet에서 VM은 VNet 주소 공간 및 Contoso 온-프레미스 네트워크에 기본 구성 외에도 인식 합니다. 

[![4]][4]

다음 표에서 ExpressRoute의 Fabrikam Inc., 병합 하기 전에 개인 피어 링 경로 테이블을 보여 줍니다.

[![3]][3]

다음 표에서 Fabrikam 구독의 병합 하기 전에 VM의 유효 경로 보여 줍니다. 테이블 당 VNet에서 VM은 VNet 주소 공간 및 함으로써 기본 외에도 Fabrikam 온-프레미스 네트워크를 인식 합니다.

[![5]][5]

이 문서에서는 과정을 단계별로 살펴보겠습니다 및 다음 Azure 네트워크 기능을 사용 하 여 원하는 교차 연결을 수행 하는 방법을 설명 합니다.

* [가상 네트워크 피어링][Virtual network peering] 
* [가상 네트워크 ExpressRoute 연결][connection]
* [글로벌 환경][Global Reach] 

## <a name="cross-connecting-vnets"></a>간 Vnet 연결

두 가상 네트워크에 연결 하는 경우 가장 적합 한 최고의 네트워크 성능을 제공 가상 네트워크 (VNet 피어 링) 피어 링 합니다. VNet 피어 링은 두 개의 Vnet 피어 링 (일반적으로 라고 VNet 피어 링) 동일한 Azure 지역 내와 서로 다른 두 Azure 지역 (일반적으로 라고 글로벌 VNet 피어 링)를 지원 합니다. 

Contoso 및 Fabrikam Azure 구독에 Vnet 간에 피어 링 하는 글로벌 VNet을 구성 하겠습니다. 가상 네트워크 간 피어 링 virtual network 만들기를 참조 하는 방법에 대 한 [가상 네트워크 피어 링을 만듭니다] [ Configure VNet peering] 문서.

다음 그림은 글로벌 VNet 피어 링을 구성한 후 네트워크 아키텍처를 보여줍니다.

[![6]][6]

다음 표에서 Contoso 구독 VM에 알려진 경로 보여 줍니다. 테이블의 마지막 항목에 주의 합니다. 이 항목의 결과인 간 가상 네트워크를 연결 합니다.

[![7]][7]

다음 표에서 Fabrikam 구독 VM에 알려진 경로 보여 줍니다. 테이블의 마지막 항목에 주의 합니다. 이 항목의 결과인 간 가상 네트워크를 연결 합니다.

[![8]][8]

두 가상 네트워크에 직접 연결 VNet 피어 링 (없습니다 다음 홉에 대 한 가지 참조 *VNetGlobalPeering* 위의 두 테이블에 있는 항목)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>온-프레미스 네트워크에 Vnet 연결 간

ExpressRoute 회로 여러 가상 네트워크에 연결할 수 있습니다. 참조 [구독 및 서비스 제한] [ Subscription limits] ExpressRoute 회로에 연결할 수 있는 가상 네트워크의 최대 수에 합니다. 

Fabrikam 구독 가상 네트워크와 온-프레미스 네트워크 간에 교차 연결을 사용 하도록 VNet에 VNet Contoso 구독에 Fabrikam ExpressRoute 회로 마찬가지로 Contoso ExpressRoute 회로 연결 하겠습니다. 다른 구독에서 ExpressRoute 회로에 가상 네트워크에 연결을 만들고 권한 부여를 사용 해야 합니다.  문서를 참조 하세요. [가상 네트워크를 ExpressRoute 회로에 연결][Connect-ER-VNet]합니다.

다음 그림은 ExpressRoute를 구성한 후 네트워크 아키텍처를 보여 줍니다. 가상 네트워크에 연결을 교차 합니다.

[![9]][9]

다음 표에서 개인 피어 링 ExpressRoute의 Contoso Ltd.의 후 간 ExpressRoute 통해 온-프레미스 네트워크에 가상 네트워크 연결의 경로 테이블을 보여 줍니다. 경로 테이블에는 두 가상 네트워크에 속하는 경로 참조 하세요.

[![10]][10]

다음 표에서 개인 피어 링 ExpressRoute의 Fabrikam Inc.의 후 간 ExpressRoute 통해 온-프레미스 네트워크에 가상 네트워크 연결의 경로 테이블을 보여 줍니다. 경로 테이블에는 두 가상 네트워크에 속하는 경로 참조 하세요.

[![11]][11]

다음 표에서 Contoso 구독 VM에 알려진 경로 보여 줍니다. 주의 *가상 네트워크 게이트웨이* 테이블의 항목입니다. VM 두 온-프레미스 네트워크에 대 한 경로 볼 수 있습니다.

[![12]][12]

다음 표에서 Fabrikam 구독 VM에 알려진 경로 보여 줍니다. 주의 *가상 네트워크 게이트웨이* 테이블의 항목입니다. VM 두 온-프레미스 네트워크에 대 한 경로 볼 수 있습니다.

[![13]][13]

>[!NOTE]
>Fabrikam 및/또는 Contoso 구독 할 수도 있습니다 스포크 VNet (허브 및 스포크 디자인은이 문서의 아키텍처 다이어그램에서 설명 하지 않습니다) 해당 허브에 Vnet입니다. ExpressRoute 허브 VNet 게이트웨이 간에 교차 연결 동부 및 서 부 허브 및 스포크 간의 통신을 허용도 됩니다.
>

## <a name="cross-connecting-on-premises-networks"></a>온-프레미스 네트워크 연결 간

ExpressRoute 글로벌 환경 서로 다른 ExpressRoute 회로에 연결 된 온-프레미스 네트워크 간의 연결을 제공 합니다. Contoso 및 Fabrikam ExpressRoute 회로 간의 글로벌 환경를 구성 하겠습니다. ExpressRoute 회로 다른 구독에 있으므로를 만들고 권한 부여를 사용 해야 합니다. 참조 [ExpressRoute 글로벌 환경 구성] [ Configure Global Reach] 단계별 지침에 대 한 문서.

다음 그림은 글로벌 환경 구성 후 네트워크 아키텍처를 보여줍니다.

[![14]][14]

다음 표에서 개인 피어 링 ExpressRoute의 Contoso Ltd.의 글로벌 환경 구성 후의 경로 테이블을 보여 줍니다. 경로 테이블에는 두 온-프레미스 네트워크에 속하는 경로 참조 하세요. 

[![15]][15]

다음 표에서 개인 피어 링 ExpressRoute의 Fabrikam Inc.의 글로벌 환경 구성 후의 경로 테이블을 보여 줍니다. 경로 테이블에는 두 온-프레미스 네트워크에 속하는 경로 참조 하세요.

[![16]][16]

## <a name="next-steps"></a>다음 단계

참조 [가상 네트워크 FAQ][VNet-FAQ]VNet 및 VNet 피어 링 추가 질문에 대 한 합니다. 참조 [ExpressRoute FAQ] [ ER-FAQ] 추가 질문이 ExpressRoute에 가상 네트워크 연결에 대 한 합니다.

Global Reach는 국가별로 롤아웃됩니다. Global Reach를 원하는 국가에서 사용할 수 있는지 확인하려면 [ExpressRoute Global Reach][Global Reach]를 참조하세요.

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "적용 시나리오"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "병합 하기 전에 Contoso ExpressRoute 경로 테이블"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "병합 하기 전에 Fabrikam ExpressRoute 경로 테이블"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "병합 하기 전에 Contoso VM 경로"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "Fabrikam VM 병합 하기 전에 경로"
[6]: ./media/cross-network-connectivity/vnet-peering.png "후 VNet 피어 링 아키텍처"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "Contoso VM 후 VNet 피어 링 경로"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "Fabrikam VM 후 VNet 피어 링 경로"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "Expressroute 교차 연결 후의 아키텍처"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "연결 ExR 및 Vnet 간 후 Contoso ExpressRoute 경로 테이블"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "연결 ExR 및 Vnet 간 후 Fabrikam ExpressRoute 경로 테이블"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "후 Contoso VM 경로 교차 연결 ExR 및 Vnet"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "후 Fabrikam VM 경로 교차 연결 ExR 및 Vnet"
[14]: ./media/cross-network-connectivity/globalreach.png "글로벌 환경 구성 후의 아키텍처"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "글로벌 환경 후 Contoso ExpressRoute 경로 테이블"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "글로벌 환경 후 Fabrikam ExpressRoute 경로 테이블"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq