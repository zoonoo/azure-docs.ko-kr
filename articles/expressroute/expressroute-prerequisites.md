<properties
   pageTitle="Express 경로 도입을 위한 필수 구성 요소 | Microsoft Azure"
   description="이 페이지에서는 Azure Express 경로 회로를 주문하기 전에 충족해야 하는 요구 사항 목록을 제공합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/16/2016"
   ms.author="cherylmc"/>


# Express 경로 필수 구성 요소   

Express 경로를 사용하여 Microsoft 클라우드 서비스에 연결하려면 아래 섹션에 나열된 다음 요구 사항을 충족하는지 확인해야 합니다.

## 계정 요구 사항

- 유효한 활성 Microsoft Azure 계정 Express 경로 회로를 설정해야 합니다. Express 경로 회로는 Azure 구독 내에서 리소스입니다. 연결이 Office 365 서비스 및 CRM 온라인과 같은 비 Azure Microsoft 클라우드 서비스를 제한하더라도 Azure 구독은 요구됩니다.
- 활성 Office 365 구독입니다.(Office 365 서비스를 사용하는 경우) 자세한 내용은 이 문서의 [Office 365 특정 요구 사항](#office-365-specific-requirements) 섹션을 참조하세요.

## 연결 공급자 관계

- 지원 목록에서 연결 공급자와의 관계는 그를 통해 쉽게 연결할 수 있어야 합니다. 연결 공급자와 기존의 비즈니스 관계가 있어야 합니다. 연결 공급자로 가진 서비스가 Express 경로와 호환되는지 확인해야 합니다.
- 지원되는 목록에 없는 연결 공급자를 사용하려는 경우 교환을 통해 Microsoft 클라우드 서비스에 연결을 만들 수 있습니다.
	- 공급자가 지원되는 목록에 있는 Exchange 위치 중 하나에 있는지 확인하려면 연결 공급자를 확인합니다.
	- 연결 공급자가 선택한 Exchange 위치로 네트워크를 확장합니다.
	- 연결 공급자로 Exchange를 지정하여 Express 경로 회로를 정렬합니다.

## 네트워크와 연결 공급자 간의 물리적 연결

연결 모델에 대한 자세한 내용은 연결 모델 섹션을 참조합니다. 고객은 자신의 온-프레미스 인프라가 설명한 모델 중 하나를 통해 서비스 공급자 인프라에 물리적으로 연결되어 있는지 확인해야 합니다.

## 연결에 대한 중복 요구 사항

고객 인프라와 서비스 공급자 인프라 간의 물리적 연결에 대한 중복 요구 사항이 없습니다. Microsoft는 3계층에서 중복성이 필요 합니다. Microsoft는 사용하도록 설정할 각 피어링에 대한 서비스 공급자를 통해 중복 라우팅이 Microsoft Edge와 고객의 네트워크 간에 설치되도록 해야 합니다. 라우팅 세션이 중복 방식으로 구성되지 않으면 서비스 가용성 SLA이 유효하지 않습니다.

## IP 주소 및 라우팅 고려 사항

고객/연결 공급자는 Microsoft Edge 인프라와 중복 BGP 세션 설치를 담당합니다. IP VPN 공급자를 통해 연결하도록 선택한 고객은 일반적으로 연결 공급자에 의존하여 라우팅 구성을 관리합니다. Exchange와 공동 배치되거나 지점간 이더넷 공급자를 통해 Microsoft에 연결한 고객은 피어링 당 중복 BGP 세션을 구성하여 가용성 SLA 요구 사항을 충족합니다. 연결 공급자는 값을 추가한 서비스로 제공할 수도 있습니다. 제한에 대한 자세한 내용은 [Express 경로 회로 라우팅 도메인](expressroute-circuit-peerings.md) 문서의 라우팅 도메인 테이블을 참조합니다.

## 보안 및 방화벽 설치

보안 및 방화벽 설치 정보는 이 문서인 [Microsoft 클라우드 서비스 및 네트워크 보안](../best-practices-network-security.md)을 참조하세요.

## Azure 공용 네트워크 및 Microsoft 피어링에 대한 NAT 구성

요구 사항 및 구성에 대한 자세한 지침은 [Express 경로 NAT 요구 사항](expressroute-nat.md)을 참조하세요. 연결 공급자를 확인하여 NAT 설치 및 관리를 관리하는지를 확인합니다. 일반적으로 3계층 연결 공급자는 NAT를 관리합니다.

## Office 365 특정 요구 사항

Office 365 요구 사항에 대한 자세한 내용은 다음 리소스를 검토합니다.

- [Office 365에 대한 네트워크 계획 및 성능 조정](http://aka.ms/tune)
- [Office 365 네트워크 트래픽 관리](https://support.office.com/article/Office-365-network-traffic-management-e1da26c6-2d39-4379-af6f-4da213218408)
- QoS 요구 사항 및 구성에 대한 자세한 지침은 [Express 경로 서비스 품질(QoS) 요구 사항](expressroute-qos.md) 문서를 참조합니다. 연결 공급자를 확인하여 VPN에 서비스의 여러 클래스를 제공하는지를 확인합니다. 

## 다음 단계

- Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.
- 서비스 공급자를 찾습니다. [Express 경로 파트너 및 피어링 위치](expressroute-locations.md)를 확인하세요.
- [라우팅](expressroute-routing.md), [NAT](expressroute-nat.md) 및 [QoS](expressroute-qos.md)에 대한 요구 사항을 참조합니다.
- Express 경로 연결을 구성합니다.
	- [Express 경로 회로 만들기](expressroute-howto-circuit-classic.md)
	- [라우팅 구성](expressroute-howto-routing-classic.md)
	- [VNet을 Express 경로 회로에 연결](expressroute-howto-linkvnet-classic.md)

<!----HONumber=AcomDC_0309_2016-->