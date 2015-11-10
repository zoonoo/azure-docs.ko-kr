<properties
   pageTitle="Express 경로 위치 | Microsoft Azure"
   description="이 문서에서는 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2015"
   ms.author="cherylmc" />

# Express 경로 파트너 및 피어링 위치

이 문서의 표에서는 Express 경로 연결 공급자, Express 경로 지역 범위, Express 경로를 통해 지원되는 Microsoft 클라우드 서비스 및 Express 경로 SI(시스템 통합업체)에 대한 정보를 제공합니다.

## Express 경로 연결 공급자

Express 경로는 모든 Azure 지역 및 위치에서 지원됩니다. 다음 맵에 Azure 지역 및 Express 경로 위치 목록을 제공합니다. Express 경로 위치는 Microsoft가 여러 서비스 공급자와 피어링하는 위치를 말합니다.

![](./media/expressroute-locations/expressroute-locations-map.png)

지역 내 하나 이상의 Express 경로 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 다음 표에서 지역 내 Express 경로 위치에 대한 Azure 지역의 맵을 제공합니다.

|**지역**|**Azure 지역**|**Express 경로 위치**|
|---|---|---|
|**북아메리카**|모든 미국 지역 - 미국 동부, 미국 서부, 미국 동부 2, 중앙 미국, 미국 중남부, 미국 중북부|애틀랜타, 시카고, 달라스, 로스앤젤레스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC|
|**남미**|브라질 남부|상파울루|
|**유럽**|북유럽, 서유럽|암스테르담, 더블린+, 런던|
|**아시아**|동아시아, 동남 아시아|홍콩, 싱가포르|
|**일본**|일본 서부, 일본 동부|오사카, 도쿄|
|**오스트레일리아**|오스트레일리아 남동부, 오스트레일리아 동부|멜버른, 시드니|
|**인도**|인도 서부, 인도 중부, 인도 남부|첸나이, 뭄바이|



아래 표에서는 국가별 클라우드의 지역 및 지정학적 경계에 대한 정보를 제공합니다.

|**지역**|**Azure 지역**|**Express 경로 위치**|
|---|---|---|---|
|**미국 정부 클라우드**|미국 정부|아이오와, 버지니아|애슈번, 시카고|


지정학적 지역에 걸친 연결은 표준 Express 경로 SKU에서 지원되지 않습니다. 전역 연결을 지원하려면 Express 경로 프리미엄 추가 기능을 사용하도록 설정해야 합니다. 국가별 클라우드 환경에 대한 연결은 지원되지 않습니다. 이러한 요구가 발생할 경우 연결 공급자와 함께 작업할 수 있습니다.


## 연결 공급자 위치

### 프로덕션 Azure

| **서비스 공급자** |**Microsoft Azure** | **Office 365** | **위치** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | 지원됨 | 지원됨 | 암스테르담, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 지원됨 | 지원됨 | 암스테르담, 런던+, 댈러스, 실리콘밸리, 워싱턴 DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 지원됨 | 지원됨 | 암스테르담, 런던, 실리콘밸리, 싱가포르, 도쿄, 워싱턴 DC |
|**China Telecom Global** | 서비스 예정 | 지원되지 않음 | 홍콩+ |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 지원됨 | 지원되지 않음 | 암스테르담, 런던 |
| **Comcast** | 지원됨 | 지원되지 않음 | 실리콘밸리, 워싱턴 DC |
| **CoreSite** | 지원됨 | 지원되지 않음 | 로스앤젤레스 | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 지원됨 | 지원됨 | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, London, Los Angeles, Melbourne, New York, Osaka, Sao Paulo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | 지원됨 | 지원되지 않음 | 도쿄 |
| **[InterCloud](https://www.intercloud.com/)** | 지원됨 | 지원됨 | 암스테르담, 런던, 싱가포르, 워싱턴 DC |
| **Internet Solutions - 클라우드 연결** | 지원됨 | 지원됨 | 암스테르담, 런던 |
| **Interxion** | 지원됨 | 지원되지 않음 | 암스테르담 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 지원됨 | 지원되지 않음 | 암스테르담, 시카고, 댈러스, 런던, 시애틀, 실리콘밸리, 워싱턴 DC |
| **Megaport** | 지원됨 | 지원되지 않음 | 멜버른, 시드니 |
| **MTN** | 지원됨 | 지원되지 않음 | 런던 |
| **NTT Communications** | 지원됨 | 지원되지 않음 | 런던+, 도쿄 |
| **NEXTDC** | 지원됨 | 지원되지 않음 | 멜버른, 시드니+ |
| **[Orange](http://www.orange-business.com/)** | 지원됨 | 지원되지 않음 | 암스테르담, 홍콩, 런던, 실리콘밸리, 싱가포르, 워싱턴 DC |
| **PCCW Global Limited** | 지원됨 | 지원되지 않음 | 홍콩 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | 지원됨 | 지원되지 않음 | 싱가포르 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 지원됨 | 지원됨 | 암스테르담, 첸나이+, 홍콩, 런던, 뭄바이, 싱가포르 |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 지원됨 | 지원됨 | 암스테르담, 런던 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 지원됨 | 지원되지 않음 | 멜버른, 시드니 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | 지원됨 | 지원됨 | 홍콩, 런던, 실리콘밸리, 시드니, 워싱턴 DC |
| **Vodafone** | 지원됨 | 지원되지 않음 | 런던 | 
| **[Zayo Group](http://www.zayo.com/)** | 지원됨 | 지원되지 않음 | 시카고, 실리콘밸리, 워싱턴 DC |

 **+**는 서비스 예정을 나타냄

### 국가별 클라우드 환경

#### 미국 정부 클라우드

| **서비스 공급자** |**Microsoft Azure** | **Office 365** | **위치** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 지원됨 | 지원되지 않음 | 시카고+, 워싱턴 DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 서비스 예정 | 지원되지 않음 | 시카고+, 워싱턴 DC+ |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 지원됨 | 지원되지 않음 | 시카고+, 워싱턴 DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 지원됨 | 지원되지 않음 | 시카고+, 워싱턴 DC |

## 나열되지 않은 서비스 공급자를 통한 연결

연결 공급자가 이전 섹션에 없는 경우에도 연결을 설정할 수 있습니다.

- 연결 공급자에게 문의하여 위의 표에 있는 Exchange 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 이더넷 Exchange에 연결되어 있습니다.

	- [Equinix 클라우드 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
- 연결 공급자가 선택한 피어링 위치로 네트워크를 확장합니다.
	- 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
- Microsoft에 연결할 연결 공급자로 Exchange를 지정하여 Express 경로 회로를 정렬합니다.
	- [Express 경로 회로 만들기](expressroute-howto-circuit-classic.md)의 단계에 따라 연결을 설정합니다.

|**연결 공급자**|**Exchange**|**피어링 위치**|
|---|---|---|
|**[XO 통신](http://www.xo.com/)**|Equinix|실리콘밸리|

## Express 경로 시스템 통합업체

사용자 요구에 맞도록 개인 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 다음 표에 나열된 시스템 통합 업체와 함께 작업하여 Express 경로에 등록할 수 있습니다.

|**시스템 통합 업체**|**대륙**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet 솔루션](http://www.dotnetsolutions.co.uk/)**|EMEA|

## 다음 단계

- Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.
- 모든 필수 조건이 충족되었는지 확인합니다. [Express 경로 필수 조건](expressroute-prerequisites.md)을 참조하세요.

<!---HONumber=Nov15_HO2-->