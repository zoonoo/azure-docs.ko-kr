<properties 
   pageTitle="가상 네트워크에 대한 보안 프레미스 간 연결 정보 | Microsoft Azure"
   description="사이트 간, 지점 및 사이트 간, Express 경로 연결을 포함하여 가상 네트워크에 대한 보안 프레미스 간 연결 형식에 대해 알아봅니다."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="cherylmc" />

# 가상 네트워크에 대한 보안 프레미스 간 연결 정보

온-프레미스 사이트를 가상 네트워크에 안전하게 연결하려는 경우 사이트 간, 지점 및 사이트 간, Express 경로의 세 가지 옵션이 있습니다.

선택하는 옵션은 다음과 같은 다양한 고려 사항에 따라 달라질 수 있습니다.


- 솔루션에 어떤 종류의 처리량이 필요한가요?
- 보안 VPN을 통한 공용 인터넷을 통해 통신하시겠어요? 아니면 개인 연결을 통해 통신하시겠어요?
- 사용할 수 있는 공용 IP 주소가 있나요?
- VPN 장치를 사용할 계획인가요? 그렇다면 해당 장치가 호환되나요?
- 컴퓨터 몇 대만 연결하시겠어요? 아니면 사이트에 대한 영구 연결을 원하세요?
- 만들려는 솔루션에 어떤 형식의 VPN 게이트웨이가 필요한가요?

아래 표는 솔루션에 대한 최상의 연결 옵션을 결정하는 데 도움이 될 수 있습니다.


| - | **지점 및 사이트 간** | **사이트 간** | **Express 경로** | 
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------| 
| **Azure 지원 서비스** | 클라우드 서비스 및 가상 컴퓨터 | 클라우드 서비스 및 가상 컴퓨터 | [서비스 목록](../expressroute/expressroute-faqs.md#supported-services) | 
| **일반 대역폭** | 일반적으로 100Mbps 미만 집계 | 일반적으로 100Mbps 미만 집계 | 50Mbps, 100Mbps, 200Mbps, 500Mbps, 1Gbps, 2Gbps, 5Gbps, 10Gbps | 
| **지원되는 프로토콜** | SSTP(Secure Sockets Tunneling Protocol) | IPsec | VLAN을 통해 직접 연결, NSP의 VPN 기술(예: MPLS, VPLS,...) | 
| **라우팅** | 경로 기반(동적) | 정책 기반(고정 라우팅) 및 경로 기반(동적 라우팅 VPN) 지원 | BGP | 
| **연결 복원력** | 활성-수동 | 활성-수동 | 활성-활성 |
| **일반 사용 사례** | 프로토타입, 클라우드 서비스 및 가상 컴퓨터에 대한 개발 / 테스트 / 랩 시나리오 | 클라우드 서비스 및 가상 컴퓨터에 대한 개발 / 테스트 / 랩 시나리오 및 소규모 프로덕션 작업 | 모든 Azure 서비스에 대한 액세스(유효성이 검사된 목록), 엔터프라이즈급 및 중요 업무용 작업, 백업, 빅 데이터, DR 사이트인 Azure | 
| **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | 
| **가격 책정** 가격 책정 [가격 책정](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [가격 책정](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [가격 책정](http://azure.microsoft.com/pricing/details/expressroute/) | 
| **기술 문서** | [VPN 게이트웨이 설명서](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [VPN 게이트웨이 설명서](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Express 경로 설명서](https://azure.microsoft.com/documentation/services/expressroute/) | 
| **FAQ ** | [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md) | [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md) | [xpress 경로 FAQ](../expressroute/expressroute-faqs.md) |


## 사이트 간 연결

사이트 간 VPN을 사용하면 온-프레미스 사이트와 가상 네트워크 간에 보안 연결을 만들 수 있습니다. 사이트 간 연결을 만들려면 온-프레미스 네트워크에 있는 VPN 장치가 Azure VPN 게이트웨이를 사용하여 보안 연결을 만들도록 구성됩니다. 연결이 만들어지면 로컬 네트워크의 리소스와 가상 네트워크의 리소스가 직접 안전하게 통신할 수 있습니다. 사이트 간 연결은 로컬 네트워크의 각 클라이언트 컴퓨터가 가상 네트워크의 리소스에 액세스하기 위해 별도의 연결을 설정하지 않아도 됩니다.

**사이트 간 연결을 사용하는 경우:**

- 하이브리드 솔루션을 만들려고 하는 경우
- 클라이언트 쪽 구성 없이 온-프레미스 위치와 가상 네트워크 간을 연결하려는 경우
- 영구적으로 연결하려는 경우 

**요구 사항**

- 온-프레미스 VPN 장치에 인터넷 연결 IPv4 IP 주소가 있어야 합니다. 이 장치는 NAT 뒤에 있을 수 없습니다.
- 호환되는 VPN 장치가 있어야 합니다. [VPN 장치 정보](http://go.microsoft.com/fwlink/p/?LinkID=615099)를 참조하세요. 
- 사용하는 VPN 장치는 솔루션에 필요한 게이트웨이 형식과 호환되어야 합니다. [VPN 게이트웨이 정보](vpn-gateway-about-vpngateways.md)를 참조하세요.
- 게이트웨이 SKU는 집계 처리량에도 영향을 줍니다. 자세한 내용은 [게이트웨이 SKU](vpn-gateway-about-vpngateways.md#gateway-skus)를 참조하세요. 

Azure 클래식 포털 및 클래식 배포 모델을 사용하여 사이트 간 VPN 게이트웨이 연결을 구성하는 방법에 대한 자세한 내용은 [사이트 간 VPN 연결을 사용하여 가상 네트워크 구성](vpn-gateway-site-to-site-create.md)을 참조하세요. 리소스 관리자 배포 모델을 사용하여 사이트 간 VPN을 구성하는 방법에 대한 자세한 내용은 [사이트 간 VPN 연결을 사용하여 가상 네트워크 만들기](vpn-gateway-create-site-to-site-rm-powershell.md)를 참조하세요.


## 지점 및 사이트 간 연결

또한 지점 및 사이트 간 VPN을 사용하여 가상 네트워크에 보안 연결을 만들 수 있습니다. 지점 및 사이트 간 구성에서는 가상 네트워크에 연결하려는 각 클라이언트 컴퓨터에서 개별적으로 연결이 구성됩니다. 지점 및 사이트 간 연결에는 VPN 장치가 필요하지 않습니다. 이 연결 형식은 각 클라이언트 컴퓨터에 설치하는 VPN 클라이언트를 사용합니다. 온-프레미스 클라이언트 컴퓨터에서 연결을 수동으로 시작하면 VPN이 설정됩니다.

지점 및 사이트 간과 사이트 간 구성은 동시에 존재할 수 있지만 사이트 간 연결과 달리 지점 및 사이트 간 연결은 동일한 가상 네트워크에 대한 Express 경로 연결을 사용하여 동시에 구성할 수 없습니다.

**지점 및 사이트 간 연결을 사용하는 경우:**

- 가상 네트워크에 연결하는 데 몇 개의 클라이언트만 구성하려고 하는 경우

- 원격 위치에서 가상 네트워크에 연결하려고 하는 경우. 예: 커피숍 또는 컨퍼런스 장소에서 연결

- 사이트 간 연결이 있지만 원격 위치에서 연결해야 하는 일부 클라이언트가 있는 경우

- 사이트 간 연결에 대한 최소 요구 사항을 충족하는 VPN 장치에 대한 액세스 권한이 없는 경우

- VPN 장치에 대한 인터넷 연결 IPv4 IP 주소가 없는 경우

지점 및 사이트 간 연결을 구성하는 방법에 대한 자세한 내용은 [가상 네트워크에 지점 및 사이트 간 VPN 연결 구성](vpn-gateway-point-to-site-create.md)을 참조하세요.

## Express 경로 연결

Azure Express 경로를 사용하면 온-프레미스 또는 공동 배치 환경의 인프라와 Azure 데이터 센터 간에 개인 연결을 만들 수 있습니다. Express 경로 연결은 공용 인터넷을 사용하지 않으며 인터넷을 통한 일반 연결보다 안정적이고 속도가 빠르며 대기 시간이 짧고 보안성이 높습니다.

경우에 따라 온-프레미스와 Azure 간 데이터 전송에 Express 경로 연결을 사용하면 상당한 비용 혜택을 얻을 수 있습니다. Express 경로를 사용하면 Express 경로 위치(Exchange 공급자 시설)에서 Azure에 대한 연결을 설정하거나, 네트워크 서비스 공급자에서 제공한 기존 WAN 네트워크(예: MPLS VPN)에서 Azure에 직접 연결할 수 있습니다.

Express 경로에 대한 자세한 내용은 [Express 경로 기술 개요](../expressroute/expressroute-introduction.md)를 참조하세요.


## 다음 단계

자세한 내용은 [Express 경로 FAQ](../expressroute/expressroute-faqs.md) 및 [VPN 게이트웨이 FAQ](vpn-gateway-vpn-faq.md)를 참조하세요.

<!---HONumber=AcomDC_1217_2015-->