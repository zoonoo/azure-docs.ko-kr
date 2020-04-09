---
title: Azure 가상 네트워크에 대한 IPv6 개요
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크의 IPv6 끝점 및 데이터 경로에 대한 IPv6 설명입니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 312e9db594983f85372285bdff415a2d5dc76ed3
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984013"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Azure 가상 네트워크에 대한 IPv6란 무엇입니까?

VNet(Azure 가상 네트워크용 IPv6)을 사용하면 가상 네트워크 내에서 와 인터넷에서 IPv6 및 IPv4 연결을 통해 Azure에서 응용 프로그램을 호스팅할 수 있습니다. 공용 IPv4 주소가 고갈되면서 이동성 및 사물 인터넷(IoT)을 위한 새로운 네트워크가 종종 IPv6에 구축됩니다. 오랫동안 확립된 ISP와 모바일 네트워크도 IPv6로 변환되고 있습니다. IPv4 전용 서비스는 기존 시장과 신흥 시장 모두에서 실질적인 불이익을 받을 수 있습니다. 듀얼 스택 IPv4/IPv6 연결을 사용하면 Azure 호스팅 서비스가 기존 IPv4 및 이러한 새로운 IPv6 장치 및 네트워크와 쉽게 연결할 수 있는 전 세계적으로 사용 가능한 이중 스택 서비스를 통해 이러한 기술 격차를 해소할 수 있습니다.

Azure의 원래 IPv6 연결을 사용하면 Azure에서 호스팅되는 응용 프로그램에 대해 이중 스택(IPv4/IPv6) 인터넷 연결을 쉽게 제공할 수 있습니다. 인바운드 및 아웃바운드 시작 연결 모두에 대해 부하 균형 이IPv6 연결을 통해 VM을 간단하게 배포할 수 있습니다. 이 기능은 여전히 사용할 수 있으며 자세한 내용은 여기에서 확인할 수 [있습니다.](../load-balancer/load-balancer-ipv6-overview.md)
Azure 가상 네트워크에 대한 IPv6는 훨씬 더 완전한 기능을 갖추고 있으며 전체 IPv6 솔루션 아키텍처를 Azure에 배포할 수 있습니다.


다음 다이어그램은 Azure의 간단한 이중 스택(IPv4/IPv6) 배포를 설명합니다.

![IPv6 네트워크 배포 다이어그램](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>이점

Azure VNET 혜택에 대한 IPv6:

- Azure 호스팅 응용 프로그램의 범위를 성장하는 모바일 및 사물 인터넷 시장으로 확장하는 데 도움이 됩니다.
- 이중 적층 IPv4/IPv6 VM은 최대 서비스 배포 유연성을 제공합니다. 단일 서비스 인스턴스는 IPv4 및 IPv6 지원 인터넷 클라이언트모두에 연결할 수 있습니다.
- 오래 지속된 안정적인 Azure VM-인터넷 IPv6 연결을 기반으로 합니다.
- 인터넷에 대한 IPv6 연결은 배포에서 명시적으로 요청할 때만 기본적으로 보안이 설정됩니다.

## <a name="capabilities"></a>기능

Azure VNet용 IPv6에는 다음과 같은 기능이 포함되어 있습니다.

- Azure 고객은 응용 프로그램, 고객의 요구를 충족하거나 온-프레미스 IP 공간에 원활하게 통합되도록 자체 IPv6 가상 네트워크 주소 공간을 정의할 수 있습니다.
- 듀얼 스택 서브넷이 있는 듀얼 스택(IPv4 및 IPv6) 가상 네트워크는 애플리케이션이 가상 네트워크 또는 IPv6 리소스와 가상 네트워크 또는 IPv6 리소스모두에 연결할 수 있도록 합니다.
    > [!IMPORTANT]
    > IPv6의 서브넷크기는 정확히 /64여야 합니다.  이렇게 하면 일부 라우터는 /64 IPv6 경로만 허용할 수 있으므로 온-프레미스 네트워크에 서브넷을 라우팅하도록 결정할 때 향후 호환성이 보장됩니다.  
- 네트워크 보안 그룹에 대한 IPv6 규칙으로 리소스를 보호합니다.
    - 또한 Azure 플랫폼의 DDoS(분산 서비스 거부) 보호는 인터넷 관련 공용 IP의
- 특히 네트워크 가상 어플라이언스를 활용하여 애플리케이션을 보강할 때 사용자 정의 경로로 가상 네트워크에서 IPv6 트래픽 라우팅을 사용자 지정합니다.
- 리눅스와 윈도우 가상 머신은 모두 Azure VNET에 대한 IPv6를 사용할 수 있습니다
- [표준 IPv6 퍼블릭 로드 밸런서](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) 지원으로 다음과 같은 복원가능하고 확장 가능한 응용 프로그램을 만들 수 있습니다.
    - 선택적 IPv6 상태 프로브를 사용하여 어떤 백 엔드 풀 인스턴스가 상태인지 확인하여 새 흐름을 받을 수 있습니다.
    - 아웃바운드 연결에 대한 완전한 선언적 제어를 제공하여 이 기능을 특정 요구에 맞게 확장하고 조정하는 선택적 아웃바운드 규칙입니다.
    - 단일 로드 밸런서가 여러 개의 IPv6 공용 IP 주소를 사용할 수 있도록 하는 선택적 다중 프런트 엔드 구성- 동일한 프런트 엔드 프로토콜 및 포트를 프런트 엔드 주소에서 재사용할 수 있습니다.
    - 로드 밸런싱 규칙의 *부동 IP* 기능을 사용하여 백 엔드 인스턴스에서 선택적 IPv6 포트를 재사용할 수 있습니다. 
    - 참고: 부하 분산은 프로토콜 변환을 수행하지 않습니다(NAT64 없음). 
- [표준 IPv6 내부 로드 밸러버](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) 지원으로 Azure VNET 내에서 복원력 있는 다중 계층 응용 프로그램을 만듭니다.   
- 레거시 배포와의 호환성을 위한 기본 IPv6 공용 로드 밸런서 지원
- [예약된 IPv6 공용 IP 주소 및 주소 범위는](ipv6-public-ip-address-prefix.md) 안정적이고 예측 가능한 IPv6 주소를 제공하여 회사 및 고객을 위해 Azure 호스팅 응용 프로그램의 허용 목록을 쉽게 지정할 수 있습니다.
- 인스턴스 수준 공용 IP는 개별 VM에 직접 IPv6 인터넷 연결을 제공합니다.
- [기존 IPv4 전용 배포에 IPv6 추가](ipv6-add-to-existing-vnet-powershell.md)- 이 기능을 사용하면 배포를 다시 만들 필요 없이 기존 IPv4 전용 배포에 IPv6 연결을 쉽게 추가할 수 있습니다.  이 과정에서 IPv4 네트워크 트래픽은 영향을 받지 않으므로 응용 프로그램 및 OS에 따라 라이브 서비스에도 IPv6를 추가할 수 있습니다.    
- 인터넷 클라이언트가 AAAA(IPv6) 레코드에 대한 Azure DNS 지원을 사용하여 선택한 프로토콜을 사용하여 이중 스택 응용 프로그램에 원활하게 액세스할 수 있도록 합니다. 
- IPv6를 사용하여 가상 시스템 스케일 세트를 사용하여 부하에 맞게 자동으로 확장되는 듀얼 스택 애플리케이션을 만듭니다.
- [가상 네트워크(VNET) 피어링(지역](virtual-network-peering-overview.md) 내 및 글로벌 피어링)을 사용하면 피어링된 네트워크의 VM에서 IPv4 및 IPv6 끝점을 모두 이중 스택 VNET를 연결할 수 있습니다. 배포를 듀얼 스택으로 전환할 때 IPv4 전용 VNET를 사용하여 듀얼 스택을 피어링할 수도 있습니다. 
- IPv6 문제 해결 및 진단은 패킷 캡처, NSG 흐름 로그, 연결 문제 해결 및 연결 모니터링과 같은 로드 밸런서 메트릭/경고 및 네트워크 감시기 기능과 함께 사용할 수 있습니다.   

## <a name="scope"></a>범위
Azure VNET용 IPv6는 고객이 Azure에서 이중 스택(IPv4+IPv6) 응용 프로그램을 호스팅할 수 있도록 하는 기본 기능 집합입니다.  시간이 지남에 따라 더 많은 Azure 네트워킹 기능에 IPv6 지원을 추가하고 궁극적으로 Azure PaaS 서비스의 이중 스택 버전을 제공하려고 하지만 그 동안 모든 Azure PaaS 서비스는 듀얼 스택 가상 컴퓨터의 IPv4 끝점을 통해 액세스할 수 있습니다.   

## <a name="limitations"></a>제한 사항
Azure 가상 네트워크 릴리스의 현재 IPv6에는 다음과 같은 제한 사항이 있습니다.
- Azure 가상 네트워크에 대한 IPv6는 모든 배포 방법을 사용하여 모든 글로벌 Azure 상용 지역에서 사용할 수 있습니다.  미국 정부 클라우드의 배포는 일시적으로 ARM(JSON) 템플릿, 명령줄 인터페이스(CLI) 및 Powershell으로 제한됩니다.  미국 정부 클라우드 포털에서 IPv6 지원을 곧 사용할 수 있습니다.  
- ExpressRoute 게이트웨이는 IPv6가 활성화된 VNET에서 IPv4 전용 트래픽에 사용할 수 있습니다.  IPv6 트래픽에 대한 지원은 로드맵에 있습니다.   
- VPN 게이트웨이는 IPv6가 활성화된 VNET에서 직접 또는 "UseRemoteGateway"로 피어를 사용하여 사용할 수 없습니다.
- Azure 플랫폼(AKS 등)은 컨테이너에 대한 IPv6 통신을 지원하지 않습니다.  

## <a name="pricing"></a>가격 책정

IPv6 Azure 리소스 및 대역폭은 IPv4와 동일한 요금으로 청구됩니다. IPv6에 대한 추가 요금또는 다른 요금은 없습니다. 공용 IP 주소, [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/)또는 [로드 밸런서에](https://azure.microsoft.com/pricing/details/load-balancer/)대한 가격 책정에 대한 세부 정보를 찾을 수 [있습니다.](https://azure.microsoft.com/pricing/details/ip-addresses/)

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 사용하여 IPv6 듀얼 스택 응용 프로그램을 배포하는](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)방법에 대해 알아봅니다.
- [Azure CLI를 사용하여 IPv6 듀얼 스택 응용 프로그램을 배포하는](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md)방법에 대해 알아봅니다.
- [JSON(리소스 관리자 템플릿)을 사용하여 IPv6 듀얼 스택 응용 프로그램을 배포하는](ipv6-configure-standard-load-balancer-template-json.md) 방법 알아보기
