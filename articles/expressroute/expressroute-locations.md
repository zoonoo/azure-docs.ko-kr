---
title: 'Kapcsolatszolgáltatók és helyek: Azure ExpressRoute | Microsoft Docs'
description: A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Kapcsolatszolgáltatók szerint rendezve.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/09/2019
ms.author: cherylmc
ms.openlocfilehash: 0fc5b1b4fcc0f5c09b06ad5d3681fcf3aed2962c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967731"
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
Express 경로 위치 (예를 들어 피어 링 위치 또는 위치 면)는 MSEE (Microsoft Enterprise Edge) 장치가 있는 공동 배치 기능입니다. Express 경로 위치는 Microsoft 네트워크에 대 한 진입점으로, 전 세계적으로 분산 되어 있어 전 세계의 Microsoft 네트워크에 연결할 수 있는 기회를 고객에 게 제공 합니다. 이러한 위치는 Express 경로 파트너와 Express 경로 다이렉트 고객이 Microsoft 네트워크에 대 한 교차 연결을 발급 하는 위치입니다. 일반적으로 Express 경로 위치는 Azure 지역과 일치 하지 않아도 됩니다. 예를 들어, 고객은 *시애틀* 피어 링 위치에 *미국 동부*의 리소스 위치로 express 경로 회로를 만들 수 있습니다.

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz.

## <a name="locations"></a>지정 학적 지역 내에서 Express 경로 위치로 Azure 지역
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Ausztrália kormánya** |Ausztrália középső régiója, Ausztrália 2. középső régiója |Canberra, Canberra2 |
| **Európa** | Közép-Franciaország, Dél-Franciaország, Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati régiója, az Egyesült Királyság déli régiója |암스테르담, Amsterdam2, 코펜하겐, 더블린, 프랑크푸르트, Geneva, 런던, London2, 마르세유에, Milan, 뮌헨, Newport (Wales), Oslo, 파리, Stavanger, 스톡홀름, 취리히 |
| **Észak-Amerika** |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |애틀랜타, 시카고, 달라스, 덴버, Las Vegas, 로스앤젤레스, 마이애미, 뉴욕, San Antonio, 시애틀, 실리콘 계곡, 실리콘 Valley2, 워싱턴 DC, 워싱턴 DC2, 몬트리올, 퀘벡 City, |
| **Ázsia** | Kelet-Ázsia, Délkelet-Ázsia |홍콩 특별 행정구, 자카르타, 콸라룸푸르, 싱가포르, 싱가포르 2, 타이베이 |
| **India** | Nyugat-India, Közép-India, Dél-India |Csennai, Csennai2, Mumbai, Mumbai2 |
| **Japán** | Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **오세아니아** | Délkelet-Ausztrália, Kelet-Ausztrália |오클랜드, 멜버른, 퍼스, 시드니, Sydney2 |
| **Dél-Korea** | Korea középső régiója, Korea déli régiója |Busan, Szöul|
| **UAE** | 아랍에미리트 중부, 아랍에미리트 북부 | Dubai, Dubai2 |
| **Dél-Afrika** | 남아프리카 공화국 서 부, 남아프리카 공화국 북부 |Fokváros, Johannesburg |
| **Dél-Amerika** | Dél-Brazília |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD – középső régió, US DoD – keleti régió  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Szilícium-völgy, Washington, D.C. |
| **Kelet-Kína** |Kelet-Kína, Kelet-Kína2 |상하이, Shanghai2 |
| **Észak-Kína** |Észak-Kína, Észak-Kína2 |베이징, Beijing2 |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók

Az alábbi táblázat a szolgáltatók szerint jeleníti meg a helyeket. Ha az elérhető szolgáltatókat hely szerint kívánja megtekinteni, tekintse meg a következőt: [Szolgáltatók hely szerint](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>글로벌 상용 Azure

| **Szolgáltató** | **Microsoft Azure** | **Office 365**  | **Helyek** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Támogatott | Támogatott | Csennai2, Mumbai2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Támogatott |Támogatott |암스테르담, 시카고, 달라스, 홍콩 특별 행정구, 상 파울로, 시애틀, 실리콘 계곡, 싱가포르, 도쿄, 워싱턴 DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Támogatott |Támogatott |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Támogatott |Támogatott |Montréal, Toronto, Québec város |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Támogatott |Támogatott |암스테르담, 홍콩 특별 행정구, 요하네스버그, 런던, Newport (Wales), 상 파울로, 실리콘 계곡, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Támogatott |Támogatott |Miami |
| **CDC** | Támogatott | Támogatott | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |Amsterdam2, 시카고, 홍콩, Las Vegas, 뉴욕, 파리, San Antonio, 실리콘 계곡, 도쿄, 전, 워싱턴 DC, 워싱턴 DC2 |
| **[최고 통신](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |Támogatott |Támogatott |홍콩, 타이베이 |
| **China Telecom Global** |Támogatott |Támogatott |홍콩 특별 행정구 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Dallas, Montreal, Toronto, Washington, D.C. |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Támogatott |암스테르담, Amsterdam2, 시카고, 더블린, 프랑크푸르트, 런던, London2, Newport, 뉴욕, 오사카, 파리, 실리콘 계곡, 실리콘 Valley2, 싱가포르 2, 도쿄 |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Támogatott |Támogatott |시카고, 덴버, 로스앤젤레스, 뉴욕, 실리콘 계곡, 실리콘 Valley2, 워싱턴 DC, 워싱턴 DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Támogatott |Támogatott |Amsterdam2, 프랑크푸르트, 마르세유에|
| **[Devoli](https://devoli.com/expressroute)** | Támogatott |Támogatott | 오클랜드, 멜버른, 시드니 |
| **du datamena** |Támogatott |Támogatott | Dubai2 |
| **eir** |Támogatott |Támogatott |Dublin|
| **[엡실론 글로벌 통신](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |암스테르담, 애틀랜타, 시카고, 달라스, 더블린, 홍콩 특별 행정구, 런던, London2, 로스앤젤레스, 멜버른, 마이애미, 뉴욕, 오사카, 파리, 상 파울로, 시애틀, 실리콘 계곡, 싱가포르, 스톡홀름, 시드니, 도쿄, 전, 워싱턴 DC |
| **Etisalat 아랍에미리트** |Támogatott |Támogatott |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Támogatott |Támogatott |암스테르담, Amsterdam2, 더블린, 런던 |
| **FarEasTone** |Támogatott |Támogatott |Tajpej|
| **GÉANT** |Támogatott |Támogatott |암스테르담, 프랑크푸르트, 마르세유에 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Támogatott| Támogatott | Csennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Támogatott |Támogatott |암스테르담, 시카고, 홍콩, 런던, 뉴욕, 파리, 실리콘 계곡, 싱가포르, 워싱턴 DC, 취리히 |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Támogatott |Támogatott |시카고, 달라스, 실리콘 계곡, 워싱턴 DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Internet Solutions – Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Támogatott |Támogatott |Fokváros, Johannesburg, London |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Támogatott |Támogatott |암스테르담, Amsterdam2, 코펜하겐, 더블린, 프랑크푸르트, 런던, 마르세유에, 파리, 취리히 |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Támogatott |Támogatott | 암스테르담, London2, 실리콘 계곡, 이상 |
| **재규어 네트워크** |Támogatott |Támogatott |Marseille|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Támogatott |Támogatott |London |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Támogatott |Támogatott |Szöul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Támogatott |Támogatott |오클랜드, 시드니 |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Támogatott | Támogatott | Amszterdam |
| **KT** | Támogatott | Támogatott | Szöul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |암스테르담, 시카고, 달라스, 런던, Newport (Wales), 상 파울로, 시애틀, 실리콘 계곡, 싱가포르, 워싱턴 DC |
| **LG CNS** |Támogatott |Támogatott |Busan, Szöul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott |Támogatott |암스테르담, 애틀랜타, 오클랜드, 시카고, 달라스, 덴버, Dubai2, 더블린, 홍콩 특별 행정구, Las Vegas, 런던, London2, 로스앤젤레스, 멜버른, 마이애미, 몬트리올, 뉴욕, 퍼스, 퀘벡 City, San Antonio, 시애틀, 실리콘 계곡, 싱가포르, 싱가포르 2, 시드니, 도쿄, 취리히, 워싱턴 DC, |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Támogatott |Támogatott |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Támogatott |Támogatott |달라스, 로스앤젤레스, 마이애미, 상 파울로, 워싱턴 DC |
| **[Következő generációs adatok](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Támogatott |Támogatott |Newport (Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Támogatott |Támogatott |멜버른, 퍼스, 시드니, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Támogatott |Támogatott |암스테르담, 홍콩 특별 행정구, 런던, 로스앤젤레스, 오사카, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Támogatott |Támogatott |Tokió |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Támogatott |Támogatott |Oszaka |
| **[Optus](https://www.optus.com.au/enterprise/)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Támogatott |Támogatott |암스테르담, Amsterdam2, 프랑크푸르트, 홍콩 특별 행정구, 요하네스버그, 런던, 파리, 상 파울로, 실리콘 계곡, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Támogatott | Támogatott | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Támogatott |Támogatott |시카고, 홍콩 특별 행정구, 런던 |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Támogatott |Támogatott |Szöul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Támogatott |Támogatott | Washington, D.C. |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Támogatott |Támogatott |Csennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Támogatott |Támogatott |Szingapúr, Szingapúr2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Támogatott |Támogatott |오클랜드, 시드니 |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Támogatott | Támogatott | Zürich |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Támogatott |Támogatott |암스테르담, 첸나이, 홍콩 특별 행정구, 런던, 뭄바이, 상 파울로, 실리콘 계곡, 싱가포르, 워싱턴 DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Támogatott |Támogatott |Amszterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Támogatott |Támogatott |런던, London2 |
| **Telenor** |Támogatott |Támogatott |암스테르담, 런던, Oslo |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Támogatott | Támogatott |암스테르담, 시카고, 달라스, 프랑크푸르트, 홍콩, 런던, 파리, 실리콘 계곡, 스톡홀름, 워싱턴 DC |
| **Telmex Uninet**| Támogatott | Támogatott | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Támogatott |Támogatott |Melbourne, Sydney, Szingapúr |
| **[Telus](https://www.telus.com)** |Támogatott |Támogatott |몬트리올, 시애틀, 전 |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Támogatott |Támogatott |Fokváros, Johannesburg |
| **[시간 dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Támogatott | Támogatott | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Támogatott |Támogatott |Dallas, Los Angeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Támogatott |Támogatott |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Támogatott |Támogatott |암스테르담, 시카고, 달라스, 홍콩 특별 행정구, 런던, 뭄바이, 실리콘 계곡, 싱가포르, 시드니, 도쿄, 전, 워싱턴 DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Támogatott | Támogatott | Washington DC2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Támogatott | Támogatott | 오클랜드, 시드니 |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Támogatott |Támogatott |Amsterdam2, 런던, 싱가포르 |
| **[Vodafone 아이디어](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Támogatott | Támogatott | 뭄바이, Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Támogatott |Támogatott |암스테르담, 시카고, 달라스, 덴버, 런던, 로스앤젤레스, 몬트리올, 뉴욕, 파리, 시애틀, 실리콘 계곡, 전, 워싱턴 DC, 워싱턴 DC2 |

 **+** = hamarosan elérhető

### <a name="national-cloud-environment"></a>Országos felhőkörnyezet

Azure 국가별 클라우드는 서로 격리 되며 전역 주석 erical Azure에서 격리 됩니다. 한 Azure 클라우드의 Express 경로는 다른 azure 지역에 연결할 수 없습니다. 

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |시카고, Phoenix, 워싱턴 DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Támogatott |Támogatott |뉴욕, Phoenix, San Antonio, 워싱턴 DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Seattle, Szilícium-völgy, Washington, D.C. |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott | Támogatott | 시카고, 달라스, San Antonio, 시애틀, 워싱턴 DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Szilícium-völgy, Washington, D.C. |

### <a name="china"></a>Kína

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **China Telecom** |Támogatott |Nem támogatott |베이징, Beijing2, 상하이, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Támogatott |Nem támogatott |Beijing2, Shanghai2 |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország

| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Nem támogatott |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Nem támogatott |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Támogatott |Nem támogatott |Berlin |
| **Interxion** |Támogatott |Nem támogatott |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott  | Nem támogatott | Berlin |
| **T-Systems** |Támogatott |Nem támogatott |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Exchange 공급자를 통한 연결

Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
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

## <a name="connectivity-through-additional-service-providers"></a>추가 서비스 공급자를 통한 연결

| **Kapcsolatszolgáltató** | **Exchange** | **Helyek** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Szingapúr |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington, D.C. |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokió |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | 암스테르담, 프랑크푸르트, 런던, 싱가포르, 워싱턴 DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokió |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | 홍콩 특별 행정구 |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amszterdam | 
| **[CMC 통신](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Szingapúr | 
| **[Aptum 기술](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montréal, Toronto |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Szilícium-völgy, Washington, D.C. |
| **[Ccs (crown 성](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | 애틀랜타, 시카고, 달라스, 로스앤젤레스, 뉴욕, 워싱턴 DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Szingapúr, Washington, D.C. |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amszterdam |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amszterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington, D.C. |
| **[국제 Gulf](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amszterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam 텔레콤 바레인 B.c](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Amszterdam |
| **LGA Telecom** |Equinix |Szingapúr|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |홍콩 특별 행정구 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amszterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington, D.C. |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Fokváros, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[통신 후 룩셈부르크](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amszterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amszterdam, Dublin, London, Párizs |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **Rogers** | Cologix, Equinix | Montréal, Toronto |
| **[스펙트럼 엔터프라이즈](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | 시카고, 달라스, 로스앤젤레스, 뉴욕, 실리콘 계곡 | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amszterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amszterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amszterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Szingapúr |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Szilícium-völgy, Washington, D.C. |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |실리콘 계곡, 실리콘 유역 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| 3-as szint | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montréal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>데이터 센터 공급자를 통한 연결

| **Szolgáltató** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS 데이터 센터](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[데이터 센터 스트리밍]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Data Centers](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>국가 연구 및 교육 네트워크를 통한 연결 (NREN)

| **Szolgáltató**|
| --- |
| **AARNET**| 
| **DeIC, a GÉANT-on keresztül**|
| **GARR, a GÉANT-on keresztül**|
| **GÉANT**|
| **HEAnet, a GÉANT-on keresztül**|
| **Internet2**|
| **JISC**|
| **RedIRIS, a GÉANT-on keresztül**|
| **SINET**|
| **Surfnet, a GÉANT-on keresztül**|

* Ha a kapcsolatszolgáltatója nem szerepel itt, ellenőrizze, hogy kapcsolódik-e a fent felsorolt ExpressRoute adatcsere-partnerek bármelyikéhez.

## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Rendszerintegrátor** | **Kontinens** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Európa |
| **[Avanade Inc.](https://www.avanade.com/)** | Ázsia, Európa, Észak-Amerika, Dél-Amerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Európa
| **[Ensyst](https://www.ensyst.com.au)** | Ázsia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Észak-Amerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Észak-Amerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Észak-Amerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Ausztrália |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Ausztrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Európa (Németország) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Európa |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | Európa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Ázsia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Európa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Észak-Amerika |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Észak-Amerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Európa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Dél-Amerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Ausztrália |

## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"
