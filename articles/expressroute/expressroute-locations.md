---
title: "연결 공급자 및 위치: Azure ExpressRoute | Microsoft Docs"
description: "이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 연결 공급자에 따라 정렬됩니다."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 70955113bbd2e008be855e591d04651d09438e81
ms.lasthandoff: 03/24/2017


---
# <a name="expressroute-partners-and-peering-locations"></a>Express 경로 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서의 표에서는 Express 경로 연결 공급자, Express 경로 지역 범위, Express 경로를 통해 지원되는 Microsoft 클라우드 서비스 및 Express 경로 SI(시스템 통합업체)에 대한 정보를 제공합니다.

## <a name="partners"></a>Express 경로 연결 공급자
Express 경로는 모든 Azure 지역 및 위치에서 지원됩니다. 다음 맵에 Azure 지역 및 Express 경로 위치 목록을 제공합니다. Express 경로 위치는 Microsoft가 여러 서비스 공급자와 피어링하는 위치를 말합니다.

![위치 맵][0]

지역 내 하나 이상의 Express 경로 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>지역 내 ExpressRoute 위치에 대한 Azure 지역 
다음 표에서 지역 내 Express 경로 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **Express 경로 위치** |
| --- | --- | --- |
| **북아메리카** |미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |애틀랜타, 시카고, 댈러스, 라스베이거스, 로스앤젤레스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC, 몬트리올, 퀘벡 시티, 토론토 |
| **남미** |브라질 남부 |상파울루 |
| **유럽** |북유럽, 서유럽, 영국 서부, 영국 남부 |암스테르담, 더블린, 런던, 뉴포트(웨일스), 파리 |
| **아시아** |동아시아, 동남 아시아 |홍콩 특별행정구, 싱가포르 |
| **일본** |일본 서부, 일본 동부 |오사카, 도쿄 |
| **오스트레일리아** |오스트레일리아 남동부, 오스트레일리아 동부 |멜버른, 시드니 |
| **인도** |인도 서부, 인도 중부, 인도 남부 |첸나이, 뭄바이 |
| **대한민국** |한국 중부, 한국 남부 |부산, 서울 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>국내 클라우드에 대한 지역 및 지리적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **Express 경로 위치** |
| --- | --- | --- | --- |
| **미국 정부 클라우드** |미국 아이오와 주 정부, 미국 버지니아 주 정부, US DoD 중부, US DoD 동부  |시카고, 댈러스, 뉴욕, 실리콘밸리, 워싱턴 DC |
| **중국** |중국 북부, 중국 동부 |베이징, 상하이 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 Express 경로 SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 Express 경로 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="locations"></a>연결 공급자 위치

다음 테이블에서는 서비스 공급자별 위치를 보여 줍니다. 위치별 사용 가능한 공급자를 보려는 경우 [위치별 서비스 공급자](expressroute-locations-providers.md#locations)를 참조하세요.


### <a name="production-azure"></a>프로덕션 Azure
| **서비스 공급자** | **Microsoft Azure** | **Office 365 및 CRM Online** | **위치** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |지원됨 |지원됨 |멜버른, 시드니 |
| **Airtel** | 서비스 예정 | 서비스 예정 | 첸나이, 뭄바이 |
| **[Aryaka Networks](http://www.aryaka.com/)** |지원됨 |지원됨 |암스테르담, 댈러스, 실리콘밸리, 싱가포르, 도쿄, 워싱턴 DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 런던, 실리콘밸리, 싱가포르, 시드니, 워싱턴 DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |지원됨 |지원됨 |몬트리올, 토론토 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |지원됨 |지원됨 |암스테르담, 홍콩 특별행정구, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |곧 출시됩니다 |서비스 예정 |실리콘밸리 |
| **China Telecom Global** |지원됨 |지원되지 않음 |홍콩 |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |지원됨 |지원됨 |댈러스, 몬트리올, 토론토 |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |지원됨 |지원됨 |암스테르담, 더블린, 런던, 도쿄 |
| **Comcast** |지원됨 |지원됨 |시카고, 실리콘밸리, 워싱턴 DC |
| **Console**| 지원됨 | 지원됨 |실리콘밸리, 토론토 |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |지원됨 |지원됨 |로스앤젤레스, 뉴욕 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원됨 |암스테르담, 애틀랜타, 시카고, 댈러스, 홍콩, 런던, 로스앤젤레스, 멜버른, 뉴욕, 오사카, 파리+, 상 파울로, 시애틀, 실리콘밸리, 싱가포르, 시드니, 도쿄, 토론토, 워싱턴 DC |
| **euNetworks** |지원됨 |지원됨 |암스테르담 |
| **[GCX(Global Cloud Xchange)](http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | 지원됨| 지원됨 | 첸나이 |
| **GÉANT** |지원됨 |지원됨 |암스테르담 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[InterCloud](https://www.intercloud.com/)** |지원됨 |지원됨 |암스테르담, 런던, 싱가포르, 워싱턴 DC |
| **Internet Solutions - 클라우드 연결** |지원됨 |지원됨 |암스테르담, 런던 |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |지원됨 |지원됨 |암스테르담, 런던, 파리 |
| **Jisc** |지원됨 |지원됨 |런던 |
| **KINX** |지원됨 |지원됨 |서울 |
| **[KPN](http://www.kpn.com/cloudconnect)** | 지원됨 | 지원됨 | 암스테르담 | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |암스테르담, 시카고, 댈러스, 라스베이거스+, 런던, 시애틀, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **LG CNS** |서비스 예정 |서비스 예정 |부산+ |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 |지원됨 |댈러스, 홍콩 특별행정구, 라스베이거스, 로스앤젤레스, 멜버른, 뉴욕, 퀘벡 시티, 시애틀, 싱가포르, 시드니, 토론토, 워싱턴 DC |
| **MTN** |지원됨 |지원됨 |런던 |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |지원됨 |지원됨 |뉴포트(웨일스) |
| **NEXTDC** |지원됨 |지원됨 |멜버른, 시드니 |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |지원됨 |지원됨 |런던, 로스앤젤레스, 오사카, 싱가포르, 도쿄, 워싱턴 DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |지원됨 |지원됨 |암스테르담, 홍콩, 런던, 실리콘밸리, 싱가포르, 시드니, 워싱턴 DC |
| **PCCW Global Limited** |지원됨 |지원됨 |홍콩 |
| **Sejong Telecom** |지원됨 |지원됨 |서울 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |지원됨 |지원됨 |첸나이 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |지원됨 |지원됨 |싱가포르 |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |지원됨 |지원됨 |오사카, 도쿄 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |지원됨 |지원됨 |암스테르담, 첸나이, 홍콩 특별행정구, 런던, 뭄바이, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |지원됨 |지원됨 |암스테르담, 더블린, 런던 |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |지원됨 |지원됨 |암스테르담+, 상파울루 |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |지원됨 |지원됨 |런던 |
| **Telenor** |지원됨 |지원됨 |암스테르담, 런던 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |지원됨 |지원됨 |멜버른, 시드니 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |지원됨 |지원됨 |암스테르담, 시카고, 달라스, 홍콩 특별행정구, 런던, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |지원됨 |지원되지 않음 |런던 |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |지원됨 |지원됨 |시카고, 댈러스+, 런던+, 로스앤젤레스, 뉴욕, 실리콘밸리, 토론토, 워싱턴 DC |

 **+** 는 서비스 예정을 나타냄

### <a name="national-cloud-environment"></a>국가별 클라우드 환경

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |지원됨 |지원됨 |시카고, 워싱턴 DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 시애틀+, 실리콘밸리, 워싱턴 DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |지원됨 |지원됨 |시카고, 뉴욕+, 워싱턴 DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨 | 지원됨 | 댈러스 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |지원됨 |지원됨 |시카고, 댈러스, 뉴욕, 워싱턴 DC |

### <a name="china"></a>중국
| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **China Telecom** |지원됨 |지원되지 않음 |베이징, 상하이 |

자세한 내용은 [중국의 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)를 참조하세요.

### <a name="germany"></a>독일
| **서비스 공급자** | **Microsoft Azure** | **Office 365** | **위치** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |지원됨 |지원되지 않음 |베를린+, 프랑크푸르트 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |지원됨 |지원되지 않음 |베를린 |
| **Interxion** |지원됨 |지원되지 않음 |프랑크푸르트 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |지원됨  | 지원되지 않음 | 베를린 |

## <a name="c1partners"></a>나열되지 않은 서비스 공급자를 통한 연결
연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix 클라우드 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 Express 경로 회로를 정렬합니다.
  * [Express 경로 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

| **연결 공급자** | **Exchange** | **위치** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |싱가포르 |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | 토론토, 몬트리올 |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |도쿄 |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |시애틀 |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | 런던 |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | 몬트리올, 토론토 |
| **[C3ntro](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | 댈러스 |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 댈러스 |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | 싱가포르 |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 암스테르담 |
| **[지수 E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 런던 |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 암스테르담 |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | 런던, 슬라우 |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |뉴욕, 워싱턴 DC |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 시드니 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 워싱턴 D.C. |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion 런던 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | 암스테르담, 프랑크푸르트 |  
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | 런던
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | 댈러스, 로스앤젤레스 |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 프랑크푸르트 |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |시카고, 실리콘밸리, 워싱턴 DC |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | 마드리드 |


## <a name="expressroute-system-integrators"></a>Express 경로 시스템 통합업체
사용자 요구에 맞도록 개인 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 Express 경로에 등록할 수 있습니다.

| **시스템 통합 업체** | **대륙** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | 유럽 |
| **[Avanade Inc.](http://www.avanade.com/)** | 아시아, 유럽, 북아메리카, 남미 |
| **Bright Skies GmbH** | 유럽
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | 유럽 |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | 북아메리카 |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | 오스트레일리아 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 유럽(독일) |
| **[Nelite](http://nelite.com/)** | 유럽 |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | 아시아 |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | 유럽 |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 북아메리카 |
| **[Project Leadership](http://www.projectleadership.net/azure)** | 북아메리카 |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | 유럽 |


## <a name="next-steps"></a>다음 단계
* Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.
* 모든 필수 조건이 충족되었는지 확인합니다. [Express 경로 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"

