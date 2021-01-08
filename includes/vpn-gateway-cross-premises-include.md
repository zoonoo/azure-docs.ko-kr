---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b283add6cff1400cc3141f4fba3f0f3939ee34aa
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97665203"
---
|  | **지점 및 사이트 간** | **사이트 간** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Azure 지원 서비스** |Cloud Services 및 Virtual Machines |Cloud Services 및 Virtual Machines |[서비스 목록](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **일반적인 대역폭** |게이트웨이 SKU에 기반 |일반적으로 총 1Gbps 미만 |50Mbps, 100Mbps, 200Mbps, 500Mbps, 1Gbps, 2Gbps, 5Gbps, 10Gbps |
| **지원되는 프로토콜** |SSTP(Secure Sockets Tunneling Protocol), OpenVPN 및 IPsec |IPsec |VLAN, NSP의 VPN 기술(MPLS, VPLS,...)을 통해 직접 연결 |
| **라우팅** |RouteBased(동적) |PolicyBased(정적 라우팅) 및 RouteBased(동적 라우팅 VPN) 지원 |BGP |
| **연결 복원력** |액티브-패시브 |액티브-패시브 또는 액티브-액티브 |액티브-액티브 |
| **일반적인 사용 사례** |원격 사용자를 위한 Azure 가상 네트워크에 대한 보안 액세스 |클라우드 서비스 및 가상 머신에 대한 개발/테스트/실습 시나리오 및 중소규모 프로덕션 워크로드 |모든 Azure 서비스(유효성이 검사된 목록), 엔터프라이즈 수준 및 중요 업무 워크로드, Backup, 빅데이터, Azure as a DR 사이트 액세스 |
| **SLA** |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |[SLA](https://azure.microsoft.com/support/legal/sla/) |
| **가격** |[가격](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[가격](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[가격](https://azure.microsoft.com/pricing/details/expressroute/) |
| **기술 문서** |[VPN Gateway 설명서](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[VPN Gateway 설명서](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[ExpressRoute 설명서](https://azure.microsoft.com/documentation/services/expressroute/) |
| **FAQ** |[VPN Gateway FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[VPN Gateway FAQ](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[ExpressRoute FAQ](../articles/expressroute/expressroute-faqs.md) |
