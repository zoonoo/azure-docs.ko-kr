<properties
   pageTitle="Express 경로 위치"
   description="이 페이지에서 서비스가 제공되는 위치 및 Azure 지역에 연결하는 방법의 자세한 개요를 제공합니다."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="cherylmc" />

# Express 경로 파트너 및 피어링 위치
이 페이지의 표에는 Express 경로 연결 공급자(EXP 및 NSP), Express 경로 지역 범위, Express 경로를 통해 지원되는 Microsoft 클라우드 서비스 및 Express 경로 SI(시스템 통합업체)에 대한 정보가 나와 있습니다.

## Express 경로 연결 공급자
Express 경로는 모든 Azure 지역 및 위치에서 지원됩니다. 아래 맵에 Azure 지역 및 Express 경로 위치 목록을 제공합니다. Express 경로 위치는 Microsoft가 여러 서비스 공급자와 피어링하는 위치를 말합니다.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

지역 내 하나 이상의 Express 경로 위치와 연결된 경우, 지역 내의 모든 지역에 걸쳐 Azure 서비스에 대한 액세스 권한을 갖습니다. 다음 표에서 지역 내 Express 경로 위치에 대한 azure 지역의 맵을 제공합니다.

|**지역**|**Azure 지역**|**Express 경로 위치**|
|---|---|---|
|**US**|모든 미국 지역-미국 동부, 미국 서부, 미국 동부 2, 중앙 미국, 미국 중남부, 미국 중북부|애틀랜타, 시카고, 달라스, 로스앤젤레스, 뉴욕, 시애틀, 실리콘밸리, 워싱턴 DC|
|**미국령**|아이오와, 버지니아|애슈번, 시카고|
|**남미**|브라질 남부|상파울루|
|**유럽**|북유럽, 서유럽|암스테르담, 런던|
|**아시아**|동아시아, 동남 아시아|홍콩, 싱가포르|
|**일본**|일본 서부, 일본 동부|도쿄|
|**오스트레일리아**|오스트레일리아 남동부, 오스트레일리아 동부|멜버른, 시드니|
|**인도**|인도 서부, 인도 중부, 인도 남부|첸나이, 뭄바이|

지역에 걸친 연결은 지원되지 않습니다. 해당 네트워크를 사용하여 지역에 걸쳐 연결을 확장하여 연결 공급자를 작업할 수 있습니다.


## EXP(Exchange 공급자) 위치

| **서비스 공급자** |**Microsoft Azure** | **Office 365** | **위치** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | 지원됨 | 지원되지 않음 | 실리콘밸리, 싱가포르, 워싱턴 DC |
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 지원됨 | 지원되지 않음 | 암스테르담, 런던 |
| **Comcast** | 지원됨 | 지원되지 않음 | 실리콘밸리, 워싱턴 DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 지원됨 | 서비스 예정 | 암스테르담, 애틀랜타, 시카고, 댈러스, 홍콩, 런던, 로스앤젤레스, 멜버른, 뉴욕, 상 파울로, 시애틀, 실리콘밸리, 싱가포르, 시드니, 도쿄, 워싱턴 DC |
| **[InterCloud](https://www.intercloud.com/)** | 지원됨 | 지원되지 않음 | 암스테르담, 런던, 싱가포르, 워싱턴 DC |
| **Internet Solutions - 클라우드 연결** | 지원됨 | 지원되지 않음 | 암스테르담, 런던 |
| **Interxion** | 지원됨 | 지원되지 않음 | 암스테르담 |
| **[Level 3 Communications - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 지원됨 | 지원되지 않음 | 시카고, 댈러스, 런던, 시애틀, 실리콘밸리, 워싱턴 DC |
| **NEXTDC** | 지원됨 | 지원되지 않음 | 멜버른, 시드니+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 지원됨 | 서비스 예정 | 암스테르담, 런던 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 지원됨 | 지원되지 않음 | 멜버른+, 시드니 |
| **[Zayo Group](http://www.zayo.com/)** | 지원됨 | 지원되지 않음 | 워싱턴 DC |

 **+**는 서비스 예정을 나타냄

연결을 설정하는 단계는 [EXP 연결 구성](expressroute-configuring-exps.md)을 참조하세요.

## NSP(네트워크 서비스 공급자) 위치


| **서비스 공급자** |**Microsoft Azure** | **Office 365** | **위치** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 지원됨 | 서비스 예정 | 암스테르담+, 런던+, 댈러스, 실리콘밸리, 워싱턴 DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 지원됨 | 서비스 예정 | 암스테르담, 런던, 실리콘밸리+, 워싱턴 DC |
|**China Telecom Global** | 서비스 예정 | 지원되지 않음 | 홍콩+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 지원됨 | 지원되지 않음 | 암스테르담, 런던 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | 지원됨 | 지원되지 않음 | 도쿄 |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 지원됨 | 지원되지 않음 | 시카고, 댈러스, 런던, 시애틀, 실리콘밸리, 워싱턴 DC |
| **NTT Communications** | 서비스 예정 | 지원되지 않음 | 도쿄+ | 
| **[Orange](http://www.orange-business.com/)** | 지원됨 | 지원되지 않음 | 암스테르담, 런던, 실리콘밸리, 워싱턴 DC |
| **PCCW Global Limited** | 지원됨 | 지원되지 않음 | 홍콩 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | 지원됨 | 지원되지 않음 | 싱가포르 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 지원됨 | 서비스 예정 | 암스테르담, 첸나이+, 홍콩, 런던, 뭄바이+, 싱가포르 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 지원됨 | 지원되지 않음 | 멜버른+, 시드니 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 지원됨 | 지원되지 않음 | 런던, 홍콩, 실리콘밸리, 워싱턴 DC |

 **+**는 서비스 예정을 나타냄

연결을 설정하는 단계는 [NSP 연결 구성](expressroute-configuring-nsps.md)을 참조하세요.

## 나열되지 않은 서비스 공급자를 통한 연결 

연결 공급자가 위 목록에 없는 경우에도 연결을 설정할 수 있습니다.

- 연결 공급자와 나열된 EXP 위치에서 Exchange 공급자 중 하나에 연결되어 있는지 확인합니다. Exchange 공급자가 제공하는 서비스에 대한 자세한 정보를 수집하려면 아래 링크를 확인할 수 있습니다. 여러 연결 공급자는 이미 EXP의 이더넷 교환에 연결되어 있습니다.
	- [Equinix 클라우드 Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- 연결 공급자가 선택한 Exchange 위치로 네트워크를 확장합니다.
	- 연결 공급자는 단일 실패 지점이 없도록 항상 사용 가능한 방식으로 연결을 확장하는지 확인합니다.
	- 연결 공급자(특히 이더넷 공급자)는 높은 가용성을 보장하려면 한 쌍의 회로를 이더넷 교환에 조달해야 할 수 있습니다. 
- Azure에 연결하려면 Exchange 공급자를 통해 Express 경로 회로를 주문합니다.
	- [EXP 연결 구성](expressroute-configuring-exps.md)의 단계에 따라 연결을 설정합니다.

|**연결 공급자**|**Exchange 공급자**|**피어링 위치**|
|---|---|---|
|**[XO 통신](http://www.xo.com/)**|Equinix|실리콘밸리|

## Express 경로 시스템 통합업체
사용자 요구에 맞도록 개인 연결을 사용하도록 설정하면 네트워크의 크기에 따라 어려울 수 있습니다. 아래 표에 나열된 시스템 통합 업체와 작업하여 Express 경로에 등록합니다.


|**시스템 통합 업체**|**대륙**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|US||
|**[Dotnet 솔루션](http://www.dotnetsolutions.co.uk/)**|EMEA|

## 다음 단계
- [Express 경로 필수 구성 요소](expressroute-prerequisites.md)를 충족하는 지 확인합니다.
- 자세한 내용은 [FAQ](expressroute-faqs.md)를 방문하세요.
- Express 경로 연결을 구성하려면 [EXP 연결 구성](expressroute-configuring-exps.md) 또는 [NSP 연결 구성](expressroute-configuring-nsps.md)을 참조하세요.
- 동일한 가상 네트워크에 대해 사이트 간 VPN 연결과 Express 경로를 둘 다 구성하려면 [공존하는 Express 경로 및 사이트 간 VPN 구성](expressroute-coexist.md)을 참조하세요.
 

<!---HONumber=August15_HO7-->