---
title: 'Helyek és kapcsolatszolgáltatók: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Hely alapján rendezve.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: cherylmc
ms.openlocfilehash: 71a0d18669ea2c5fc9e0548e195c3df0145f6cb0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967799"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


이 문서의 표에서는 Express 경로 지리적 범위 및 위치, Express 경로 연결 공급자 및 SIs (Express 경로 시스템 통합 업체)에 대 한 정보를 제공 합니다.

> [!Note]
> Azure 지역 및 Express 경로 위치는 서로 다른 두 가지 개념 이며, Azure 하이브리드 네트워킹 연결을 탐색 하는 데 두 가지 간의 차이점을 이해 하는 것이 중요 합니다. 
>
>

## <a name="azure-regions"></a>Azure-régiók
Azure 지역은 Azure 계산, 네트워킹 및 저장소 리소스가 있는 글로벌 데이터 센터입니다. Azure 리소스를 만들 때 고객은 리소스 위치를 선택 해야 합니다. 리소스 위치는 리소스를 만들 Azure 데이터 센터 (또는 가용성 영역)를 결정 합니다.

## <a name="expressroute-locations"></a>ExpressRoute-helyek
Express 경로 위치 (예를 들어 피어 링 위치 또는 위치 면)는 MSEE (Microsoft Enterprise edge) 장치가 있는 공동 배치 기능입니다. Express 경로 위치는 Microsoft 네트워크에 대 한 진입점으로, 전 세계적으로 분산 되어 있어 전 세계의 Microsoft 네트워크에 연결할 수 있는 기회를 고객에 게 제공 합니다. 이러한 위치는 Express 경로 파트너와 Express 경로 다이렉트 고객이 Microsoft 네트워크에 대 한 교차 연결을 발급 하는 위치입니다. 일반적으로 Express 경로 위치는 Azure 지역과 일치 하지 않아도 됩니다. 예를 들어, 고객은 *시애틀* 피어 링 위치에 *미국 동부*의 리소스 위치로 express 경로 회로를 만들 수 있습니다.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz. 

## <a name="locations"></a>지정 학적 지역 내에서 Express 경로 위치로 Azure 지역
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormánya** | Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Közép-Franciaország, Dél-Franciaország, Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati régiója, az Egyesült Királyság déli régiója |암스테르담, Amsterdam2, 코펜하겐, 더블린, 프랑크푸르트, Geneva, 런던, London2, 마르세유에, Milan, 뮌헨, Newport (Wales), Oslo, 파리, Stavanger, 스톡홀름, 취리히, 뮌헨 |
| **Észak-Amerika** | USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |애틀랜타, 시카고, 달라스, 덴버, Las Vegas, 로스앤젤레스, 마이애미, 뉴욕, San Antonio, 시애틀, 실리콘 계곡, 실리콘 Valley2, 워싱턴 DC, 워싱턴 DC2, 몬트리올, 퀘벡 City, |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia |홍콩 특별 행정구, 자카르타, 콸라룸푸르, 싱가포르, 싱가포르 2, 타이베이 |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **오세아니아** | Délkelet-Ausztrália, Kelet-Ausztrália |오클랜드, 멜버른, 퍼스, 시드니, Sydney2 | 
| **Dél-Korea** | Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **UAE** | 아랍에미리트 중부, 아랍에미리트 북부 | Dubai, Dubai2 |
| **Dél-Afrika** | 남아프리카 공화국 서 부, 남아프리카 공화국 북부 |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>국가별 클라우드의 Azure 지역 및 지정 학적 경계
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |상하이, Shanghai2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |베이징, Beijing2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázatban a kapcsolati helyek és az egyes helyekhez tartozó szolgáltatók láthatók. A szolgáltatók és a szolgáltatási helyek megtekintéséhez lásd: [Helyek szolgáltatók szerint](expressroute-locations.md).

* **로컬 Azure 지역은** 각 피어 링 위치에서 [express](expressroute-faqs.md) 경로를 사용 하 여 액세스할 수 있습니다. **n/a** 는 해당 피어 링 위치에서 Express 경로 로컬을 사용할 수 없음을 나타냅니다.

* **영역은** [가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 나타냅니다.


### <a name="global-commercial-azure"></a>글로벌 상용 Azure
| **Hely** | **주소** | **영역별로** | **로컬 Azure 지역** | **ER Direct** | **Szolgáltatók** |
| --- | --- | --- | --- | --- | --- |
| **Amszterdam** | [서-ix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Nyugat-Európa | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, Colt, 의사 Inix, euNetworks, GÉANT, InterCloud, Interx이온, KPN, IX Reach, 수준 3 통신, Megaport, NTT 통신, 주황색, Tata 통신 Zayo |
| **Amszterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Nyugat-Európa | 10G, 100G | CenturyLink Cloud Connect, Colt, UNCIX, euNetworks, Interxion, 주황, Vodafone |
| **Atlanta** | [서-ix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | – | – | Equinix, Megaport |
| **오클랜드** | [Vocus Group NZ 올버니](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | – | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Dél-Korea | – | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália középső régiója | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Ausztrália 2. középső régiója| 10G, 100G | CDC |
| **Fokváros** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Dél-Afrika nyugati régiója | 10G | Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Csennai** | Tata Communications | 2 | Dél-India | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Csennai2** | Airtel | 2 | Dél-India | – | Airtel |
| **Chicago** | [서-ix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA északi középső régiója | 10G, 100G | Aryaka Networks, & T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite,, PacketFabric, PCCW Global 제한, 스 프린트, Telia 캐리어, Verizon, Zayo |
| **코펜하겐** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | – | 10G | Interxion |
| **Dallas** | [서-ix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | – | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA nyugati középső régiója | – | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Egyesült Arab Emírségek északi régiója | – | Etisalat 아랍에미리트 |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Egyesült Arab Emírségek északi régiója | – | du datamena, Megaport, Orixcom |
| **Dublin** | [서-ix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Észak-Európa | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Frankfurt** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Középnyugat-Németország | 10G, 100G | Colt, DE-DE x, GEANT, Interxion, 주황색, Telia 캐리어 |
| **Geneva** | [서-ix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Nyugat-Svájc | 10G, 100G | |
| **Hong Kong SAR** | [서-ix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Kelet-Ázsia | – | Aryaka Networks, 영국령 텔레콤, CenturyLink 클라우드 연결, 최고 통신, 중국 통신 글로벌, 고 대이, Megaport, NTT 통신, 주황색, PCCW Global 제한, Tata 통신, Telia 캐리어, Verizon |
| **지역** | 서 용 kom 인도네시아 | 4 | – | 10G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Dél-Afrika északi régiója | – | 영국령 텔레콤, 인터넷 솔루션-클라우드 연결, 액체 텔레콤, 주황, Teraco |
| **Kuala Lumpur** | [시간 dotCom Menara 목표](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | – | – | TIME dotCom |
| **Las Vegas** | [LV 전환](https://www.switch.com/las-vegas) | 1 | – | – | CenturyLink Cloud Connect, Megaport |
| **London** | [서-ix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Egyesült Királyság déli régiója | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions – Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse – KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse 북부 2](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | Egyesült Királyság déli régiója | 10G, 100G | Colt, IX Reach, Megaport, Telehouse |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | – | – | CoreSite, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Dél-Franciaország | – | 비 CIX, GEANT, Interxion, 재규어 네트워크 |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Délkelet-Ausztrália | 10G, 100G | AARNet, Devoli,, Megaport, NEXTDC, Optus, Telstra Corporation, TPG 텔레콤 |
| **Miami** | [서-ix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | – | 10G | C3ntro, Megaport, Neutrona Networks |
| **Milan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | – | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | – | – | 종 캐나다, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Nyugat-India | – | GCX (Global Gcx), Jio, Sify, Tata 통신, Verizon |
| **Mumbai2** | Airtel | 2 | Nyugat-India | – | Airtel, Sify, Vodafone Idea |
| **뮌헨** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | – | 10G, 100G | |
| **New York** | [서-ix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | – | – | CenturyLink Cloud Connect, Colt, Coresite, 서 면 Inix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Wales)** | [Következő generációs adatok](https://www.nextgenerationdata.co.uk) | 1 | Egyesült Királyság nyugati régiója | – | 영국령 텔레콤, Colt, 수준 3 통신, 차세대 데이터 |
| **Oszaka** | [서-ix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Nyugat-Japán | – | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norvégia keleti régiója | 10G, 100G | Telenor |
| **Párizs** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Közép-Franciaország | – | CenturyLink Cloud Connect, Colt,와 서는 Inix, Intercloud, Interx이온, 주황색, Telia 캐리어, Zayo |
| **퍼스** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | – | 10G | Megaport, NextDC |
| **Quebec város** | [유리한](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kelet-Kanada | – | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA déli középső régiója | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [서-ix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Dél-Brazília | – | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [서-ix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA 2. nyugati régiója | 10G, 100G | Aryaka Networks, Megaport, Telus, Zayo |
| **Szöul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Korea középső régiója | 10G, 100G | KINX, KT, LG CN, Sejong 텔레콤 |
| **Szilícium-völgy** | [서-ix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA nyugati régiója | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, CenturyLink Cloud Connect, Colt, Comcast, Coresite, 의사 Inix, InterCloud, Internet2, IX Reach, 패킷, PacketFabric, 수준 3 통신, Megaport, 주황, 스 프린트, Tata 통신, Telia 캐리어, Verizon, Zayo |
| **실리콘 Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA nyugati régiója | 10G, 100G | Colt, Coresite | 
| **Szingapúr** | [서-ix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Délkelet-Ázsia | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [전역 스위치 타이 Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Délkelet-Ázsia | 10G, 100G | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [녹색 산지 DC1](https://greenmountain.no/dc1-stavanger/) | 1 | – | 10G, 100G | |
| **스톡홀름** | [서-ix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | – | 10G | 서-ix, Telia 캐리어 |
| **Sydney** | [서-ix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Ausztrália keleti régiója | 10G, 100G | AARNet, & T NetBond, 영국령 텔레콤, Devoli, Kordia, Megaport, NEXTDC, NTT Communications, Optus, 주황, Spark NZ, Telstra Corporation, TPG 텔레콤, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Ausztrália keleti régiója | 10G, 100G | NextDC |
| **타이베이** | 최고 통신 | 2 | – | 10G | 최고 통신, FarEasTone |
| **Tokió** | [서-ix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Kelet-Japán | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, CenturyLink Cloud Connect, Colt, Equinix, 인터넷 이니셔티브 일본 Inc.-IIJ, Megaport, NTT 통신, NTT 동부, 주황, 소프트 뱅크, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Közép-Kanada | 10G, 100G | & T NetBond, 종 캐나다, CenturyLink Cloud Connect, Cologix, Megaport, Telus, Verizon, Zayo |
| **Washington, D.C.** | [서-ix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | Aryaka Networks, & T NetBond, 영국령 텔레콤, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Cologix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, 주황, PacketFabric, SES, 스 프린트 및 Tata 통신, Telia 캐리어, Verizon, Zayo |
| **워싱턴 DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Viasat, Zayo | 
| **취리히** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | – | – | Intercloud, Interx이온, Megaport, Swisscom |

 **+** = hamarosan elérhető

### <a name="national-cloud-environments"></a>Országos felhőkörnyezetek

Azure 국가별 클라우드는 서로 격리 되며 전역 주석 erical Azure에서 격리 됩니다. 한 Azure 클라우드의 Express 경로는 다른 azure 지역에 연결할 수 없습니다.

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Hely** | **주소** | **로컬 Azure 지역**| **ER Direct** | **Szolgáltatók** |
| --- | --- | --- | --- | --- |
| **Chicago** | [서-ix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | – | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [서-ix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | – | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [서-ix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | – | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | – | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Texas | – | CenturyLink Cloud Connect, Megaport |
| **Szilícium-völgy** | [서-ix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | – | 10G, 100G | Equinix, Level 3 Communications, Verizon |
| **Seattle** | [서-ix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | – | – | Equinix, Megaport |
| **Washington, D.C.** | [서-ix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 동부, US Gov 버지니아 | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kína
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | 중국 통신, GDS |
| **Sanghaj** |China Telecom |
| **Shanghai2** | 중국 통신, GDS |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Hely** | **Szolgáltatók** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Exchange 공급자를 통한 연결
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

## <a name="connectivity-through-satellite-operators"></a>위성 연산자를 통한 연결
원격이 고 파이버 연결이 없거나 다른 연결 옵션을 탐색 하려는 경우 다음 위성 연산자를 확인할 수 있습니다. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>추가 서비스 공급자를 통한 연결
| **Hely** | **Exchange** | **연결 공급자** |
| --- | --- | --- |
| **Amszterdam** | 수준 3 통신, Interxion | BICS, CloudXpress, Eurofiber, Fastweb S. p. A, Gulf Bridge 국제, Kalaam 텔레콤 바레인 Bics, MainOne, Nianet, POST 통신 룩셈부르크, Proximus, TDC Erhverv, 텔레콤 Italia 반짝, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Ccs (crown 성
| **Fokváros** | Teraco | MTN |
| **Chicago** | Equinix| Ccs (crown 성, 스펙트럼 Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro 텔레콤, Cox Business, Ccs (crown 성, Data Foundry, 스펙트럼 Enterprise, and Stelco |
| **Frankfurt** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **함부르크** | Equinix | Cinia |
| **Hong Kong SAR** | Equinix | 최고, Macroview 텔레콤 |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, 서-Ix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Ccs (crown 성, 스펙트럼 Enterprise, and Stelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Aptum 기술, Iwmentor, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Ccs (crown 성, 스펙트럼 Enterprise, Webair |
| **Párizs** | Equinix | Proximus |
| **Quebec város** | Megaport | Fibrenoire |
| **상 상사 인 paula** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Szilícium-völgy** |Coresite, | Cox Business, 스펙트럼 Enterprise, Windstream, X2nsat i n c. |
| **Szingapúr** |Equinix |1CLOUDSTAR, BICS, CMC 통신, 엡실론 통신 제한, LGA 텔레콤, UIH (미국 정보 고속도로) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokió** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | 락 게이트 기술 Inc., Beanfield Metroconnect, Aptum 기술, IVedha Inc., Iwmentor, Thinktel, Zirro|
| **Washington, D.C.** |Equinix | Altice Business, BICS, Cox Business, Ccs (crown 성, Gtt Communications Inc., 엡실론 통신 제한, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Kontinens** | **Rendszerintegrátorok** |
| --- | --- |
| **Ázsia** |Avanade Inc., OneAs1a |
| **Ausztrália** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Európa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Észak-Amerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Dél-Amerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"
