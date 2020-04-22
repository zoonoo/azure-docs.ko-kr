---
title: '위치 및 연결 공급자: Azure ExpressRoute | Microsoft Docs'
description: 이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다. 위치에 따라 정렬됩니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: cherylmc
ms.openlocfilehash: ba0573af59c2924648361fec10bffed189291d28
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768565"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 파트너 및 피어링 위치

> [!div class="op_single_selector"]
> * [공급자별 위치](expressroute-locations.md)
> * [위치별 공급자](expressroute-locations-providers.md)


이 문서의 표는 ExpressRoute 지리적 범위 및 위치, ExpressRoute 연결 공급자 및 ExpressRoute 시스템 통합업체(SI)에 대한 정보를 제공합니다.

> [!Note]
> Azure 지역 및 ExpressRoute 위치는 두 가지 개념으로 구분되며, 둘 사이의 차이점을 이해하는 것은 Azure 하이브리드 네트워킹 연결을 탐색하는 데 매우 중요합니다. 
>
>

## <a name="azure-regions"></a>Azure 지역
Azure 지역은 Azure 계산, 네트워킹 및 저장소 리소스가 있는 전역 데이터 센터입니다. Azure 리소스를 만들 때 고객은 리소스 위치를 선택해야 합니다. 리소스 위치는 리소스가 생성되는 Azure 데이터 센터(또는 가용성 영역)를 결정합니다.

## <a name="expressroute-locations"></a>익스프레스루트 위치
ExpressRoute 위치(피어링 위치 또는 모임 위치라고도 함)는 Microsoft 엔터프라이즈 에지(MSEE) 장치가 있는 공동 위치 시설입니다. ExpressRoute 위치는 Microsoft 네트워크의 진입점이며 전 세계에 분산되어 있어 고객에게 전 세계 Microsoft 네트워크에 연결할 수 있는 기회를 제공합니다. 이러한 위치는 ExpressRoute 파트너 및 ExpressRoute Direct 고객이 Microsoft 네트워크에 대한 교차 연결을 발급하는 위치입니다. 일반적으로 ExpressRoute 위치는 Azure 지역과 일치할 필요가 없습니다. 예를 들어 고객은 *시애틀* 피어링 위치에 리소스 *위치인 미국 동부를*사용하여 ExpressRoute 회로를 만들 수 있습니다.

지역 내 하나 이상의 ExpressRoute 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>지역 내 ExpressRoute 위치에 대한 Azure 지역
다음 표에서 지역 내 ExpressRoute 위치에 대한 Azure 지역의 맵을 제공합니다.

| **지역** | **Azure 지역** | **익스프레스루트 위치** |
| --- | --- | --- |
| **오스트레일리아 정부** | 오스트레일리아 중부, 오스트레일리아 중부 2 |캔버라, 캔버라2 |
| **유럽** | 프랑스 중부, 프랑스 남부, 독일 북부, 독일 서중부, 북유럽, 노르웨이 동부, 노르웨이 서부, 스위스 북부, 스위스 서부, 영국 서부, 영국 남부, 서유럽 |암스테르담, 암스테르담2, 코펜하겐, 더블린, 프랑크푸르트, 제네바, 런던, 런던2, 마르세유, 밀라노, 뮌헨, 뉴포트(웨일스), 오슬로, 파리, 스타방에르, 스톡홀름, 취리히, 뮌헨 |
| **북아메리카** | 미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |애틀랜타, 시카고, 달라스, 덴버, 라스베이거스, 로스앤젤레스, 마이애미, 뉴욕, 샌안토니오, 시애틀, 실리콘 밸리, 실리콘 밸리2, 워싱턴 DC, 워싱턴 DC2, 몬트리올, 퀘벡 시티, 토론토, 밴쿠버 |
| **아시아** | 동아시아, 동남 아시아 | 방콕, 홍콩, 홍콩2, 자카르타, 쿠알라룸푸르, 싱가포르, 싱가포르2, 타이베이 |
| **인도** | 인도 서부, 인도 중부, 인도 남부 |첸나이, 첸나이2, 뭄바이, 뭄바이2 |
| **일본** | 일본 서부, 일본 동부 |오사카, 도쿄, 도쿄2 |
| **오세아니아** | 오스트레일리아 남동부, 오스트레일리아 동부 |오클랜드, 멜버른, 퍼스, 시드니, 시드니2 | 
| **대한민국** | 한국 중부, 한국 남부 |부산, 서울|
| **아랍에미리트** | 아랍에미리트 중부, 아랍에미리트 북부 | 두바이, 두바이2 |
| **남아프리카 공화국** | 남아프리카 공화국 서부, 남아프리카 북부 |케이프타운, 요하네스버그 |
| **남아메리카** | 브라질 남부 |상파울루 |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>국가 클라우드에 대한 Azure 지역 및 지정학적 경계
아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

| **지역** | **Azure 지역** | **익스프레스루트 위치** |
| --- | --- | --- |
| **미국 정부 클라우드** |US Gov 애리조나, US Gov 아이오와, US Gov 텍사스, US Gov 버지니아, US DoD 중부, US DoD 동부  |애틀랜타, 시카고, 달라스, 뉴욕, 피닉스, 샌안토니오, 시애틀, 실리콘 밸리, 워싱턴 DC |
| **중국 동부** |중국 동부, 중국 동부2 |상하이, 상하이2 |
| **중국 북부** |중국 북부, 중국 북부2 |베이징, 베이징2 |
| **독일** |독일 중부, 독일 동부 |베를린, 프랑크푸르트 |

지정학적 지역에 걸친 연결은 표준 ExpressRoute SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 ExpressRoute 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 연결 공급자

다음 테이블에서는 각 위치에 대한 연결 위치 및 서비스 공급자를 보여 줍니다. 서비스를 제공할 수 있는 서비스 공급자 및 위치를 보려면 [서비스 공급자별 위치](expressroute-locations.md)를 참조하세요.

* **로컬 Azure 지역은** 각 피어링 위치에서 [ExpressRoute Local에](expressroute-faqs.md) 액세스할 수 있는 지역입니다. **n/a는** 해당 피어링 위치에서 ExpressRoute 로컬을 사용할 수 없음을 나타냅니다.

* **영역은** [가격을](https://azure.microsoft.com/pricing/details/expressroute/)나타냅니다.


### <a name="global-commercial-azure"></a>글로벌 상용 Azure
| **위치** | **주소** | **영역** | **로컬 Azure 지역** | **ER 다이렉트** | **서비스 제공업체** |
| --- | --- | --- | --- | --- | --- |
| **암스테르담** | [에퀴닉스 AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 서유럽 | 10G, 100G | 아리아카 네트웍스, AT&T 넷본드, 영국 텔레콤, 콜트, 에퀴닉스, 유네트웍스, 제안트, 인터클라우드, 인터렉션, KPN, IX 리치, 레벨 3 커뮤니케이션, 메가포트, NTT 커뮤니케이션, 오렌지, 타타 커뮤니케이션, 텔레포니카, 텔레노어, 텔리아 캐리어, 버라이존, 자요 |
| **암스테르담2** | [인터렉시온 AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 서유럽 | 10G, 100G | 센추리링크 클라우드 커넥트, 콜트, DE-CIX, 유네트웍스, 인터렉시온, 오렌지, 보다폰 |
| **애틀랜타** | [에퀴닉스 AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 해당 없음 | 해당 없음 | Equinix, Megaport |
| **오클랜드** | [보커스 그룹 NZ 알바니](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 해당 없음 | 10G | 데볼리, 코르디아, 메가포트, 스파크 뉴저지, 보커스 그룹 NZ |
| **방콕** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | 해당 없음 | 10G | AIS |
| **부산** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | 한국 남부 | 해당 없음 | LG CNS |
| **캔버라** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 | 10G, 100G | CDC |
| **캔버라2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | 오스트레일리아 중부 2| 10G, 100G | CDC |
| **케이프타운** | [테라코 CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 남아프리카 공화국 서부 | 10G | BCX, 인터넷 솔루션 - 클라우드 커넥트, 리퀴드 텔레콤, 테라코 |
| **첸나이** | Tata Communications | 2 | 인도 남부 | 10G | Global CloudXchange(GCX), SIFY, Tata Communications |
| **첸나이2** | Airtel | 2 | 인도 남부 | 해당 없음 | Airtel |
| **시카고** | [에퀴닉스 CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 미국 중북부 | 10G, 100G | 아리아카 네트웍스, AT&T 넷본드, 센추리링크 클라우드 커넥트, 콜로닉스, 콜트, 컴캐스트, 코어사이트, 에퀴닉스, 인터클라우드, 인터넷2, 레벨 3 커뮤니케이션, 메가포트, 패킷패브릭, PCCW 글로벌 리미티드, 스프린트, 텔리아 캐리어, 버라이존, 자요 |
| **코펜하겐** | [인터렉시온 CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 해당 없음 | 10G | Interxion |
| **달라스** | [에퀴닉스 DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 해당 없음 | 10G, 100G | 아리아카 네트웍스, AT&T 넷본드, 콜로닉스, 에퀴닉스, 인터넷2, 레벨 3 커뮤니케이션, 메가포트, 중성자 네트웍스, 텔멕스 유니넷, 텔리아 캐리어, 트랜스텔코, 버라이존, 자요|
| **덴버** | [코어사이트 DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 미국 중서부 | 해당 없음 | 코어사이트, 메가포트, 자요 |
| **두바이** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | 아랍에미리트 북부 | 해당 없음 | 에티살라트 아랍에미리트 |
| **두바이2** | [뒤 데이터 메나](http://datamena.com/solutions/data-centre) | 3 | 아랍에미리트 북부 | 해당 없음 | 뒤 데이터 메나, 메가 포트, 오렌지, 오릭스콤 |
| **더블린** | [에퀴닉스 DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 북유럽 | 10G, 100G | 콜트, 어, 에퀴닉스, 유네트웍스, 인터렉시온, 메가포트 |
| **프랑크푸르트** | [인터렉시온 FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | 독일 웨스트 센트럴 | 10G, 100G | AT&T 넷본드, 센추리링크 클라우드 커넥트, 콜트, DE-CIX, 에퀴닉스, GEANT, 인터렉션, 메가포트, 오렌지, 텔리아 캐리어 |
| **제네바** | [에퀴닉스 GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | 스위스 웨스트 | 10G, 100G | Equinix, Megaport |
| **홍콩** | [에퀴닉스 HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 동아시아 | 해당 없음 | 아리아카 네트웍스, 브리티시 텔레콤, 센추리링크 클라우드 커넥트, 수석 텔레콤, 차이나 텔레콤 글로벌, 에퀴닉스, 인터클라우드, 메가포트, NTT 커뮤니케이션, 오렌지, PCCW 글로벌 리미티드, 타타 커뮤니케이션, 텔리아 캐리어, 버라이존 |
| **홍콩2** | [메가이](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | 해당 없음 | 10G | |
| **자카르타** | 텔콤 인도네시아 | 4 | 해당 없음 | 10G | |
| **요하네스버그** | [테라코 JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 남아프리카 북부 | 10G | BCX, 영국 텔레콤, 인터넷 솔루션 - 클라우드 커넥트, 리퀴드 텔레콤, 오렌지, 테라코 |
| **콸라룸푸르** | [타임닷컴 메나라 목표](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | 해당 없음 | 해당 없음 | TIME dotCom |
| **라스베이거스** | [스위치 LV](https://www.switch.com/las-vegas) | 1 | 해당 없음 | 해당 없음 | CenturyLink Cloud Connect, Megaport |
| **런던** | [에퀴닉스 LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 영국 남부 | 10G, 100G | AT&T 넷본드, 영국 텔레콤, 콜트, 에퀴닉스, 유네트웍스, 인터클라우드, 인터넷 솔루션 - 클라우드 커넥트, 인터렉션, 지스크, 레벨 3 커뮤니케이션, 메가포트, MTN, NTT 커뮤니케이션, 오렌지, PCCW 글로벌 리미티드, 타타 커뮤니케이션, 텔레하우스 - KDDI, 텔레노어, 텔리아 캐리어, 버라이존, 보다폰, 자요 |
| **런던2** | [텔레하우스 노스 투](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | 영국 남부 | 10G, 100G | 센추리링크 클라우드 커넥트, 콜트, IX 리치, 에퀴닉스, 메가포트, 텔레하우스 - KDDI |
| **Los Angeles** | [코어사이트 LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 해당 없음 | 해당 없음 | 코어사이트, 에퀴닉스, 메가포트, 중성자나네트웍스, NTT, 트랜스텔코, 자요 |
| **마르세유** |[인터렉시온 MRS1](https://www.interxion.com/Locations/marseille/) | 1 | 프랑스 남부 | 해당 없음 | DE-CIX, GEANT, 인터렉시온, 재규어 네트워크, 우레두 클라우드 커넥트 |
| **멜버른** | [넥스트DC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | 오스트레일리아 남동부 | 10G, 100G | AARNet, 데볼리, 에퀴닉스, 메가포트, 넥스트DC, 옵터스, 텔스트라 코퍼레이션, TPG 텔레콤 |
| **마이애미** | [에퀴닉스 MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 해당 없음 | 10G | C3ntro, 에퀴닉스, 메가포트, 중성자나 네트웍스 |
| **Milan** | [이리데오스](https://irideos.it/en/data-centers/) | 1 | 해당 없음 | 10G | |
| **Montreal** | [콜로직 MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 해당 없음 | 10G, 100G | 벨 캐나다, 콜로직스, 메가포트, 텔루스, 자요 |
| **뭄바이** | Tata Communications | 2 | 인도 서부 | 해당 없음 | 글로벌 클라우드X체인지(GCX), 릴라이언스 지오, 시피, 타타 커뮤니케이션즈, 버라이존 |
| **뭄바이2** | Airtel | 2 | 인도 서부 | 해당 없음 | Airtel, Sify, Vodafone Idea |
| **뮌헨** | [에지코네X](https://www.edgeconnex.com/locations/europe/) | 1 | 해당 없음 | 10G, 100G | |
| **뉴욕** | [에퀴닉스 NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | 해당 없음 | 해당 없음 | 센추리링크 클라우드 커넥트, 콜트, 코어사이트, 에퀴닉스, 인터클라우드, 메가포트, 패킷, 자요 |
| **뉴포트 (웨일스)** | [차세대 데이터](https://www.nextgenerationdata.co.uk) | 1 | 영국 서부 | 해당 없음 | 영국 텔레콤, 콜트, 레벨 3 통신, 차세대 데이터 |
| **오사카** | [에퀴닉스 OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 일본 서부 | 10G, 100G | 콜트, 에퀴닉스, 인터넷 이니셔티브 일본 Inc. - IIJ, 메가 포트, NTT 통신, NTT 스마트 커넥트, 소프트 뱅크 |
| **오슬로** | [디지플렉스 울벤](https://www.digiplex.com/locations/oslo-datacentre) | 1 | 노르웨이 동부 | 10G, 100G | 메가 포트, 텔레노어, 텔리아 캐리어 |
| **파리** | [인터렉시온 PAR5](https://www.interxion.com/Locations/paris/) | 1 | 프랑스 중부 | 10G, 100G | 센추리링크 클라우드 커넥트, 콜트, 에퀴닉스, 인터클라우드, 인터렉션, 오렌지, 텔리아 캐리어, 자요 |
| **퍼스** | [넥스트DC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 해당 없음 | 10G | 메가포트, 넥스트DC |
| **퀘벡 시티** | [유리한](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | 캐나다 동부 | 해당 없음 | Bell Canada, Megaport |
| **샌안토니오** | [사이러스원 SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 미국 중남부 | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **상파울루** | [에퀴닉스 SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | 브라질 남부 | 해당 없음 | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **시애틀** | [에퀴닉스 SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 미국 서부 2 | 10G, 100G | 아리아카 네트웍스, 에퀴닉스, 레벨 3 통신, 메가포트, 텔루스, 자요 |
| **서울** | [킨엑스 가산 IDC](https://www.kinx.net/support/location/?lang=en) | 2 | 한국 중부 | 10G, 100G | 킨엑스, KT, LG CNS, 세종텔레콤 |
| **실리콘밸리** | [에퀴닉스 SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 미국 서부 | 10G, 100G | 아리아카 네트웍스, AT&T NetBond, 영국 텔레콤, 센추리링크 클라우드 커넥트, 콜트, 컴캐스트, 코어사이트, 에퀴닉스, 인터클라우드, 인터넷2, IX 리치, 패킷, 패킷패브릭, 레벨 3 커뮤니케이션, 메가포트, 오렌지, 스프린트, 타타 커뮤니케이션, 텔리아 캐리어, 버라이존, 자요 |
| **실리콘 밸리2** | [코어사이트 SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 미국 서부 | 10G, 100G | 콜트, 코어사이트 | 
| **싱가포르** | [에퀴닉스 SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 동남아시아 | 10G, 100G | 아리아카 네트웍스, AT&T 넷본드, 영국 텔레콤, 차이나 모바일 인터내셔널, 엡실론 글로벌 커뮤니케이션, 에퀴닉스, 인터클라우드, 레벨 3 커뮤니케이션, 메가포트, NTT 커뮤니케이션, 오렌지, 싱텔, 타타 커뮤니케이션, 텔스트라 코퍼레이션, 버라이존, 보다폰 |
| **Singapore2** | [글로벌 스위치 타이셍](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 동남아시아 | 10G, 100G | 중국 유니콤 글로벌, 콜트, 엡실론 글로벌 커뮤니케이션, 메가포트, PCCW 글로벌 리미티드, 싱텔 |
| **스타방에르** | [그린 마운틴 DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 노르웨이 웨스트 | 10G, 100G | |
| **스톡홀름** | [에퀴닉스 SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 해당 없음 | 10G | 에퀴닉스, 텔리아 캐리어 |
| **시드니** | [에퀴닉스 SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | 오스트레일리아 동부 | 10G, 100G | AARNet, AT&T 넷본드, 영국 텔레콤, 데볼리, 에퀴닉스, 코르디아, 메가포트, NEXTDC, NTT 커뮤니케이션, 옵터스, 오렌지, 스파크 NZ, 텔스트라 코퍼레이션, TPG 텔레콤, 버라이존, 보커스 그룹 NZ |
| **시드니2** | [넥스트DC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | 오스트레일리아 동부 | 10G, 100G | 메가포트, 넥스트DC |
| **타이베이** | Chief Telecom | 2 | 해당 없음 | 10G | 수석 텔레콤, 파레스톤 |
| **도쿄** | [에퀴닉스 TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 일본 동부 | 10G, 100G | 아리아카 네트웍스, AT&T 넷본드, BBIX, 브리티시 텔레콤, 센추리링크 클라우드 커넥트, 콜트, 에퀴닉스, 인터넷 이니셔티브 재팬 사 - IIJ, 메가포트, NTT 커뮤니케이션, NTT EAST, 오렌지, 소프트뱅크, 버라이존 |
| **도쿄2** | [도쿄에서](https://www.attokyo.com/) | 2 | 일본 동부 | 10G, 100G | 도쿄에서 |
| **토론토** | [콜로닉스 토르1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | 캐나다 중부 | 10G, 100G | AT&T 넷본드, 벨 캐나다, 센추리링크 클라우드 커넥트, 콜로닉스, 에퀴닉스, IX 리치 메가포트, 텔루스, 버라이존, 자요 |
| **밴쿠버** | [콜로닉스 VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10G, 100G | |
| **워싱턴 DC** | [에퀴닉스 DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | 아리아카 네트웍스, AT&T NetBond, 영국 텔레콤, 센추리링크 클라우드 커넥트, 콜로닉스, 콜트, 컴캐스트, 코어사이트, 에퀴닉스, 인터넷2, 인터클라우드, IX 리치, 레벨 3 커뮤니케이션, 메가포트, 뉴트로나 네트웍스, NTT 커뮤니케이션, 오렌지, 패킷패브릭, SES, 스프린트, 타타 커뮤니케이션, 텔리아 캐리어, 버라이존, 자요 |
| **워싱턴 DC2** | [코어사이트 레스턴](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 미국 동부, 미국 동부 2 | 10G, 100G | 센추리링크 클라우드 커넥트, 코어사이트, 인텔샛, 비아사트, 자요 | 
| **취리히** | [인터렉시온 ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 스위스 북부 | 10G, 100G | 인터클라우드, 인터렉시온, 메가포트, 스위스콤 |

 **+** 곧 출시 될 것을 나타냅니다

### <a name="national-cloud-environments"></a>국가별 클라우드 환경

Azure 국가 클라우드는 서로 및 전역 상용 Azure에서 격리됩니다. 하나의 Azure 클라우드에 대한 ExpressRoute는 다른 Azure 지역에 연결할 수 없습니다.

### <a name="us-government-cloud"></a>미국 정부 클라우드
| **위치** | **주소** | **로컬 Azure 지역**| **ER 다이렉트** | **서비스 제공업체** |
| --- | --- | --- | --- | --- |
| **애틀랜타** | [에퀴닉스 AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | 해당 없음 | 10G, 100G | Equinix |
| **시카고** | [에퀴닉스 CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 해당 없음 | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **달라스** | [에퀴닉스 DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 해당 없음 | 10G, 100G | Equinix, Megaport, Verizon |
| **뉴욕** | [에퀴닉스 NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 해당 없음 | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [사이러스원 챈들러](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov 애리조나 | 해당 없음 | AT&T 넷본드, 센추리링크 클라우드 커넥트, 메가포트 |
| **샌안토니오** | [사이러스원 SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov 텍사스 | 해당 없음 | CenturyLink Cloud Connect, Megaport |
| **실리콘밸리** | [에퀴닉스 SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 해당 없음 | 10G, 100G | AT&T, 에퀴닉스, 레벨 3 커뮤니케이션, 버라이존 |
| **시애틀** | [에퀴닉스 SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 해당 없음 | 해당 없음 | Equinix, Megaport |
| **워싱턴 DC** | [에퀴닉스 DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 미국 DoD 이스트, 미국 정부 버지니아 | 10G, 100G | AT&T 넷본드, 센추리링크 클라우드 커넥트, 에퀴닉스, 레벨 3 커뮤니케이션, 메가포트, 버라이존 |

### <a name="china"></a>중국
| **위치** | **서비스 제공업체** |
| --- | --- |
| **베이징** |China Telecom |
| **베이징2** | 차이나 텔레콤, 차이나 유니콤, GDS |
| **상하이** |China Telecom |
| **상하이2** | 차이나 텔레콤, GDS |

자세한 내용은 [중국의 익스프레스루트를](http://www.windowsazure.cn/home/features/expressroute/) 참조하십시오.

### <a name="germany"></a>독일
| **위치** | **서비스 제공업체** |
| --- | --- |
| **베를린** |e-shelter, Megaport+, T-Systems |
| **프랑크푸르트** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Exchange 공급자를 통한 연결
연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

* 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix 클라우드 Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [인터시온 (동음이의)](https://www.interxion.com/)
  * [넥스트DC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
  * 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
* Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 ExpressRoute 회로를 정렬합니다.
  * [ExpressRoute 회로 만들기](expressroute-howto-circuit-classic.md) 의 단계에 따라 연결을 설정합니다.

## <a name="connectivity-through-satellite-operators"></a>위성 사업자를 통한 연결
원격이고 광섬유 연결이 없거나 다른 연결 옵션을 탐색하려는 경우 다음 위성 사업자를 확인할 수 있습니다. 

* 인텔샛
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>추가 서비스 공급자를 통한 연결
| **위치** | **Connector** | **연결 공급자** |
| --- | --- | --- |
| **암스테르담** | 에퀴닉스, 인터렉시온, 레벨 3 통신 | 빅스, 클라우드엑스프레스, 유로파이버, 패스트웹 S.p.A, 걸프 브리지 인터내셔널, 칼라암 텔레콤 바레인 B.S.C, 마인원, 니아넷, 포스트 텔레콤 룩셈부르크, 프록시무스, RETN, TDC 에르베르브, 텔레콤 이탈리아 스파클, 텔레콤 도이치랜드 GmbH, 텔리아 |
| **애틀랜타** | Equinix| 크라운 캐슬
| **케이프타운** | Teraco | MTN |
| **시카고** | Equinix| 크라운 성, 스펙트럼 기업, 윈드 스트림 |
| **달라스** | Equinix, Megaport | Axtel, C3ntro 텔레콤, 콕스 비즈니스, 크라운 캐슬, 데이터 파운드리, 스펙트럼 엔터프라이즈, 트랜스텔코 |
| **프랑크푸르트** | Interxion | 빅스, 시니아, 에퀴닉스, 니아넷, QSC AG, 텔레콤 도이치랜드 GmbH |
| **함부르크** | Equinix | 중국 |
| **홍콩 특별 행정구** | Equinix | Chief, Macroview Telecom |
| **요하네스버그** | Teraco | MTN |
| **런던** | 빅스, 에퀴닉스, 유네트웍스| 베제크 인터내셔널(주), 코어Azure, 엡실론 통신 유한회사, 지수 E, HSO, 넥스젠 네트웍스, 프록시무스, 타마레스 텔레콤, 자인 |
| **Los Angeles** | Equinix |크라운 캐슬, 스펙트럼 엔터프라이즈, 트랜스텔코 |
| **마드리드** | Level3 | Zertia |
| **Montreal** | Cologix| 에어게이트 테크놀로지스, 주식회사 Aptum 테크놀로지스, 로저스, 지로 |
| **뉴욕** |Equinix, Megaport | Altice 비즈니스, 크라운 캐슬, 스펙트럼 엔터프라이즈, 웹에어 |
| **파리** | Equinix | Proximus |
| **퀘벡 시티** | Megaport | Fibrenoire |
| **상파울루** | Equinix | Venha Pra Nuvem |
| **시애틀** |Equinix | Alaska Communications |
| **실리콘밸리** |코어사이트, 에퀴닉스 | 콕스 비즈니스, 스펙트럼 엔터프라이즈, 윈드 스트림, X2nsat Inc. |
| **싱가포르** |Equinix |1CLOUDSTAR, 빅스, CMC 텔레콤, 엡실론 통신 제한, LGA 텔레콤, 유나이티드 정보 고속도로 (UIH) |
| **슬라우** | Equinix | HSO|
| **시드니** | Megaport | Macquarie Telecom Group|
| **도쿄** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **토론토** | Equinix, Megaport | 에어게이트 테크놀로지스, 빈필드 메트로커넥트, Aptum 테크놀로지스, 이베다, 로저스, 씽크텔, 지로|
| **워싱턴 DC** |Equinix | Altice 비즈니스, BICS, 콕스 비즈니스, 크라운 캐슬, Gtt 커뮤니케이션, 엡실론 통신 제한, 마저, 윈드 스트림 |

## <a name="expressroute-system-integrators"></a>ExpressRoute 시스템 통합업체
사용자 요구에 맞도록 프라이빗 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 ExpressRoute에 등록할 수 있습니다.

| **Continent** | **시스템 통합 업체** |
| --- | --- |
| **아시아** |Avanade Inc., OneAs1a |
| **오스트레일리아** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **유럽** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **북아메리카** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **남아메리카** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>다음 단계
* 익스프레스루트에 대한 자세한 내용은 [익스프레스루트 FAQ를](expressroute-faqs.md)참조하십시오.
* 모든 필수 조건이 충족되었는지 확인합니다. [ExpressRoute 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "위치 맵"
