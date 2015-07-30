<properties 
   pageTitle="Express 경로 소개 | Microsoft Azure"
   description="이 페이지에서는 Express 경로 연결의 작동 방식, exchange 공급자 및 네트워크 서비스 공급자 사용, Express 경로 공용, 개인 및 Microsoft 피어링 등 Express 경로 서비스에 대한 개요를 제공합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/15/2015"
   ms.author="cherylmc"/>

# Express 경로 기술 개요

Microsoft Azure Express 경로를 사용하면 온-프레미스 또는 공동 배치 환경의 인프라와 Microsoft 데이터 센터 간에 개인 연결을 만들 수 있습니다. Express 경로를 사용하면 Express 경로 파트너 공동 배치 기능으로 Microsoft Azure 및 Office 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정하거나, 기존 WAN 네트워크(예: 네트워크 서비스 공급자에서 제공된 MPLS VPN)에서 직접 연결할 수 있습니다.
 
Express 경로 연결은 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다. 경우에 따라 온-프레미스 네트워크와 Azure 간 데이터 전송에 Express 경로 연결을 사용하면 상당한 비용 혜택을 얻을 수도 있습니다. 이미 온-프레미스 네트워크에서 Azure로 크로스 프레미스 연결을 설정한 경우, 가상 네트워크를 그대로 유지 하면서 Express 경로 연결로 마이그레이션할 수 있습니다.

자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.

## Express 경로 연결은 어떻게 작동하나요?

WAN을 Microsoft 클라우드 서비스에 연결하려면, 전용 회로를 주문하고 연결 공급자를 통해 활성화해야 합니다. 두 연결 공급자 유형을 직접 exchange 공급자(EXP)를 통한 계층 3 또는 네트워크 서비스 공급자(NSP)를 통한 계층 3에서 선택할 수 있습니다. WAN과 Microsoft 클라우드 간 하나 또는 두 유형의 연결을 사용하도록 선택할 수 있습니다.

## Exchange 공급자 및 네트워크 서비스 공급자
Express 경로 공급자는 네트워크 서비스 공급자(NSP) 및 Exchange 공급자(EXP)로 분류됩니다.

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Exchange 공급자**|**네트워크 서비스 공급자**|
|---|---|---|
|**일반적인 연결 모델**| 지점 간 이더넷 연결 또는 클라우드 exchange에서의 연결 | telco VPN을 통한 모든 연결 |
|**지원되는 대역폭**|200Mbps, 500Mbps, 1Gbps 및 10Gbps|10Mbps, 50Mbps, 100Mbps, 500Mbps, 1Gbps|
|**연결 공급자**|[Exchange 공급자](expressroute-locations.md)|[네트워크 서비스 공급자](expressroute-locations.md)|
|**라우팅**|CE(Customer Edge) 라우터와의 직접적인 BGP 세션| telco와의 BGP 세션|
|**가격**|[EXP 가격](http://azure.microsoft.com/pricing/details/expressroute/)|[NSP 가격](http://azure.microsoft.com/pricing/details/expressroute/)|

### EXP(Exchange 공급자)
Equinix 및 TeleCity 그룹과 같은 클라우드 exchange 서비스 공급자 및 Cole 및 Level 3와 같은 지점 간 연결 서비스 공급자와 파트너 관계로, 고객의 프레미스와 Azure 간의 연결을 제공합니다. 200Mbps에서 10Gbps까지 회로 대역폭(200Mbps, 500Mbps, 1Gbps and 10Gbps)을 제공합니다.

Exchange 공급자를 통해 직접 계층 3 연결을 사용하려는 경우, 이 중 3 가지 방법으로 수행할 수 있습니다.

- 서비스를 제공하는 위치에서 Equinix의 클라우드 Exchange 또는 TeleCity의 클라우드 IX와 같은 클라우드 exchange를 함께 배치할 수 있습니다. 이러한 경우 중복 연결을 클라우드 exchange에 주문합니다. 
- Level 3와 같은 공급자를 통해 데이터 센터와 Microsoft 간에 이더넷 회로를 설치할 수 있습니다. 
- 로컬 연결 공급자와 작업하여 가장 가까운 exchange 공급자 시설에 중복 연결을 얻고 클라우드 exchange에 연결할 수 있습니다.

중복 연결로 SLA에 대한 요구 사항을 충족하도록 요청할 수 있습니다. Microsoft edge에 직접 연결을 지원하지 않습니다. 전용 회로는 이더넷 공급자 또는 로컬 클라우드 exchange를 통해 항상 활성화됩니다. Microsoft와 네트워크 간의 계층 2 연결을 설정하는 동안 계층 2 도메인을 확장 지원하지 않습니다. 계층 3 연결을 설정하도록 edge 라우터와 Microsoft edge 라우터 사이의 중복 라우팅 세션을 설정합니다.

구성 및 실제 예에 대한 자세한 내용은 [Exchange 공급자를 통해 Express 경로 연결 구성](expressroute-configuring-exps.md)의 단계별 지침을 따릅니다.


### NSP(네트워크 서비스 공급자)

AT&T 및 British Telecom과 같은 Telcos와 파트너로 WAN과 Azure 간의 연결을 제공합니다. 10Mbps에서 1Gbps까지 회로 대역폭(10Mbps, 50Mbps, 100Mbps, 500Mbps, 1Gbps)을 제공합니다.

네트워크 서비스 공급자 중 하나에서 VPN 서비스를 사용하는 경우, 새 하드웨어를 배포하거나 기존 네트워크에 대한 주요 구성을 변경하지 않고 Azure로 네트워크를 확장할 수 있습니다.

구성 및 실제 예에 대한 자세한 내용은 [네트워크 서비스 공급자를 통해 Express 경로 연결 구성](expressroute-configuring-nsps.md)의 단계별 지침을 따릅니다.

## Express 경로 피어링
아래 그림에는 WAN 및 Microsoft 간 연결의 논리적 표현을 제공합니다. 연결 공급자(NSP/EXP)를 통해 WAN을 Microsoft에 연결하려면 *전용 회로*를 주문해야 합니다. 전용 회로는 연결 공급자를 통해 WAN과 Microsoft 간의 논리적 연결을 나타냅니다. 여러 전용 회로를 주문할 수 있으며, 각각은 동일하거나 다른 지역에 있을 수 있고 다른 서비스 공급자를 통해 WAN에 연결할 수 있습니다.

![](./media/expressroute-introduction/expressroute-basic.png)

전용 회로에는 공용, 개인 및 Microsoft와 연관된 여러 라우팅 도메인이 있습니다. 각 라우팅 도메인은 고가용성을 위해 활성-활성 또는 loadsharing 구성으로 라우터 쌍에 동일하게 구성됩니다.

![](./media/expressroute-introduction/expressroute-peerings.png)


### 개인 피어링
Azure 계산 서비스, 즉 가상 컴퓨 (IaaS) 및 가상 네트워크 내에 배포된 클라우드 서비스(PaaS)는 개인 피어링 도메인을 통해 연결될 수 있습니다. 개인 피어링 도메인은 Microsoft Azure로의 핵심 네트워크의 신뢰할 수 있는 확장으로 간주됩니다. 핵심 네트워크 및 Azure Vnet(가상 네트워크) 간의 양방향 연결을 설정할 수 있습니다. 이렇게 하면 개인 IP 주소에서 가상 컴퓨터와 클라우드 서비스에 직접 연결할 수 있습니다.

둘 이상의 가상 네트워크를 개인 피어링 도메인에 연결할 수 있습니다. 제한 및 제한 사항에 대한 내용은 [FAQ 페이지](expressroute-faqs.md)를 검토하세요.
  

### 공용 피어링
Azure 저장소, SQL 데이터베이스 및 웹사이트와 같은 서비스는 공용 IP 주소에 제공됩니다. 공용 피어링 라우팅 도메인을 통해 공용 IP 주소(클라우드 서비스의 VIP 포함)에서 호스팅되는 서비스에 개인적으로 연결할 수 있습니다. 인터넷을 통해 연결하지 않고도 공용 피어링 도메인을 엑스트라넷에 연결하고 WAN에서 해당 공용 IP 주소의 모든 Azure 서비스에 연결할 수 있습니다. 연결은 항상 사용자의 WAN에서 Microsoft Azure 서비스로 시작됩니다. Microsoft Azure 서비스가 라우팅 도메인을 통해 네트워크로의 연결을 시작할 수 없습니다. 공용 피어링을 사용하도록 설정하면 모든 Azure 서비스에 연결할 수 있습니다. Microsoft에서 경로를 보급하는 서비스는 사용자가 선택할 수 없습니다. [Microsoft Azure 데이터 센터 IP 범위](http://www.microsoft.com/download/details.aspx?id=41653) 페이지에서 이 피어링을 통해 보급하는 접두사 목록을 검토할 수 있습니다. 필요한 경로만 이용하도록 네트워크 내에 사용자 지정 경로 필터를 정의할 수 있습니다.

공용 피어링 라우팅 도메인을 통해 지원 서비스에 대한 자세한 내용은 [FAQ 페이지](expressroute-faqs.md)를 검토하세요.
 
### Microsoft 피어링
Microsoft 피어링을 통해 다른 모든 Microsoft 온라인 서비스(예: Office 365 서비스)에 대해 연결됩니다. Microsoft 피어링 라우팅 도메인을 통해 WAN 및 Microsoft 클라우드 서비스 간의 양방향 연결을 설정합니다. 사용자 또는 연결 공급자가 소유하는 공용 IP 주소에 대해서만 Microsoft 클라우드 서비스에 연결 해야 하고 정의하는 모든 규칙을 따라야 합니다. 자세한 내용은 [Express 경로 필수 구성 요소](expressroute-prerequisites.md) 페이지를 검토하세요.

지원 서비스, 비용 및 구성 세부 정보에 대한 자세한 내용은 [FAQ 페이지](expressroute-faqs.md)를 참조하세요. Microsoft 피어링이 지원하는 연결 공급자의 목록에 대한 정보는 [Express 경로 위치](expressroute-locations.md) 페이지를 검토하세요.


다음 표에서 3개의 라우팅 도메인을 비교합니다.

||**개인 피어링**|**공용 피어링**|**Microsoft 피어링**|
|---|---|---|---|
|**최대. # 피어링 지원되는 접두사**|기본은 4000, Express 경로 프리미엄에서는 10,000|기본은 4000, Express 경로 프리미엄에서는 10,000|200|
|**지원되는 IP 주소 범위**|WAN 내 유효한 IPv4 주소|사용자나 연결 공급자가 소유한 공용 IPv4 주소|사용자나 연결 공급자가 소유한 공용 IPv4 주소|
|**AS 번호 요구 사항**|개인 및 공용 AS 번호 . 고객은 자신의 공용 AS 번호를 소유해야 합니다. | 개인 및 공용 AS 번호. 고객은 자신의 공용 AS 번호를 소유해야 합니다. | 공용 AS 번호만. 소유권의 유효성을 검사하려면 라우팅 레지스트리에 대해 AS 번호의 유효성을 검사해야 합니다. |
|**라우팅 인터페이스 IP 주소**|RFC1918 및 공용 IP 주소|고객에게 등록된 공용 IP 주소 / 라우팅 레지스트리의 NSP.| 고객에게 등록된 공용 IP 주소 / 라우팅 레지스트리의 NSP.|
|**MD5 해시 지원**| 예|예|예|

자신의 전용 회로의 일부로 하나 이상의 라우팅 도메인을 사용하도록 선택할 수 있습니다. 단일 라우팅 도메인으로 수집하려는 경우 모든 라우팅 도메인을 동일 VPN(NSP 경우)에 넣도록 선택할 수 있습니다. 위의 다이어그램과 유사한 다른 라우팅 도메인에도 넣습니다. 개인 피어링이 직접 핵심 네트워크에 연결되고 공용 및 Microsoft 피어링 링크가 엑스트라넷에 연결되는 것이 권장 구성입니다.
 
3개의 모든 피어링 세션을 선택하는 경우, 3쌍의 BGP 세션(각 피어링 형식에 대해 한 쌍)이 있어야 합니다. BGP 세션 쌍은 항상 사용 가능한 링크를 제공합니다. EXP를 통해 연결하는 경우, 사용자가 라우팅을 구성하고 관리합니다(EXP가 라우팅을 관리하도록 제공하지 않는 경우). NSP를 통해 연결하도록 선택한 경우, 라우팅 관리에 NSP를 사용할 수 있습니다. Express 경로를 설정하기 위한 워크플로 검토하여 자세히 알아볼 수 있습니다.


## 다음 단계

- 서비스 공급자를 찾습니다. [Express 경로 서비스 공급자 및 위치](expressroute-locations.md)를 참조하세요.
- Express 경로 연결을 구성합니다. 지침은 [네트워크 서비스 공급자를 통해 Express 경로 연결 구성](expressroute-configuring-nsps.md) 또는 [Exchange 공급자를 통해 Express 경로 연결 구성](expressroute-configuring-exps.md)을 참조하세요. 

<!---HONumber=July15_HO2-->