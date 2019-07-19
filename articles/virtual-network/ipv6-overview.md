---
title: Azure Virtual Network에 대 한 IPv6 개요 (미리 보기)
titlesuffix: Azure Virtual Network
description: Azure 가상 네트워크의 IPv6 끝점 및 데이터 경로에 대 한 IPv6 설명입니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5093b74484cd04a0c0c7afed8e2ebc725af033f5
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249828"
---
# <a name="what-is-ipv6-for-azure-virtual-network-preview"></a>Azure Virtual Network에 대 한 i p v 6은 무엇 인가요? (미리 보기)

Azure Virtual Network (VNet)에 대 한 i p v 6을 사용 하면 가상 네트워크와 인터넷 간에 IPv6 및 IPv4 연결을 사용 하 여 Azure에서 응용 프로그램을 호스트할 수 있습니다. 공용 IPv4 주소가 고갈 되기 때문에 IoT (mobility and 사물 인터넷) 용 새 네트워크는 일반적으로 i p v 6에서 빌드됩니다. 긴 설정 된 ISP와 모바일 네트워크도 i p v 6으로 변환 됩니다. IPv4 전용 서비스는 기존 및 새로운 시장 모두에서 진정한 단점을 얻을 수 있습니다. 이중 스택 IPv4/IPv6 연결을 사용 하면 Azure에서 호스팅되는 서비스에서 기존 IPv4 및 이러한 새 IPv6 장치 및 네트워크 모두에 쉽게 연결 하는, 전 세계적으로 사용 가능한 이중 누적 서비스를 통해이 기술 격차를 트래버스할 수 있습니다.

Azure의 원래 IPv6 연결을 사용 하면 Azure에서 호스트 되는 응용 프로그램에 대 한 이중 스택 (IPv4/IPv6) 인터넷 연결을 쉽게 제공할 수 있습니다. 인바운드 및 아웃 바운드 시작 된 연결에 대해 부하가 분산 된 IPv6 연결을 사용 하 여 Vm을 간단히 배포할 수 있습니다. 이 기능을 계속 사용할 수 있으며 [여기](../load-balancer/load-balancer-ipv6-overview.md)에서 자세한 정보를 확인할 수 있습니다.
Azure virtual network에 대 한 i p v 6은 전체 IPv6 솔루션 아키텍처를 Azure에 배포할 수 있는 보다 완전 한 기능을 제공 합니다.

> [!Important]
> Azure Virtual Network에 대 한 i p v 6은 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

다음 다이어그램은 Azure에서 간단한 이중 스택 (IPv4/IPv6) 배포를 보여 줍니다.

![IPv6 네트워크 배포 다이어그램](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>이점

Azure Virtual Network IPv6 혜택:

- Azure에서 호스트 되는 응용 프로그램의 범위를 증가 하는 모바일 및 사물 인터넷 시장으로 확장 하는 데 도움이 됩니다.
- 듀얼 누적 IPv4/IPv6 Vm은 최대 서비스 배포 유연성을 제공 합니다. 단일 서비스 인스턴스는 IPv4 및 IPv6 가능 인터넷 클라이언트 모두에 연결할 수 있습니다.
- 장기 설정 된 안정적인 Azure VM 간 IPv6 연결을 기반으로 합니다.
- 인터넷에 대 한 IPv6 연결은 배포에서 명시적으로 요청 하는 경우에만 설정 되기 때문에 기본적으로 안전 합니다.

## <a name="capabilities"></a>기능

VNet 용 IPv6에는 다음과 같은 기능이 포함 되어 있습니다.

- Azure 고객은 자신의 응용 프로그램, 고객의 요구에 맞게 자신의 IPv6 가상 네트워크 주소 공간을 정의 하거나 온-프레미스 IP 공간으로 원활 하 게 통합할 수 있습니다.
- 이중 스택 (IPv4 및 IPv6) 이중 스택 서브넷을 사용 하는 가상 네트워크는 응용 프로그램이 가상 네트워크 또는 인터넷에서 IPv4 및 IPv6 리소스 모두에 연결할 수 있습니다.
- 네트워크 보안 그룹에 대 한 IPv6 규칙을 사용 하 여 리소스 보호
- 특히 네트워크 가상 어플라이언스를 활용 하 여 응용 프로그램을 보강 하는 경우 사용자 정의 경로를 사용 하 여 가상 네트워크에서 IPv6 트래픽 라우팅을 사용자 지정 합니다.
- 인터넷 클라이언트가 IPv6 (AAAA) 레코드에 대 한 Azure DNS 지원으로 선택 된 프로토콜을 사용 하 여 이중 스택 응용 프로그램에 원활 하 게 액세스할 수 있도록 합니다. 
- 표준 IPv6 공용 Load Balancer 지원: 다음을 포함 하는 복원 력이 뛰어난 응용 프로그램을 만들 수 있습니다.
    - 상태에 해당 하는 백 엔드 풀 인스턴스를 확인 하 여 새 흐름을 받을 수 있는 선택적 IPv6 상태 프로브입니다. 을 선택합니다.  
    - 아웃 바운드 연결에 대 한 모든 선언적 제어를 제공 하는 선택적 아웃 바운드 규칙은 특정 요구에 맞게이 기능을 확장 하 고 조정 합니다.
    - 단일 부하 분산 장치에서 여러 IPv6 공용 IP 주소를 사용할 수 있도록 하는 선택적 다중 프런트 엔드 구성-프런트 엔드 주소에서 동일한 프런트 엔드 프로토콜 및 포트를 재사용할 수 있습니다.
- 인스턴스 수준 공용 IP는 개별 Vm에 직접 IPv6 인터넷 연결을 제공 합니다.
- 업그레이드 원위치에서 기존 IPv4 전용 배포에 대 한 IPv6 연결을 쉽게 추가할 수 있습니다.
- 가상 머신 확장 집합을 사용 하 여 부하에 맞게 크기를 자동으로 조정 하는 이중 스택 응용 프로그램을 만듭니다.
- 미리 보기에 대 한 포털 지원에는 이중 스택 (IPv4 + IPv6) 가상 네트워크 및 서브넷, ipv6 네트워크 보안 그룹 규칙, IPv6 사용자 정의 경로 및 IPv6 공용 Ip의 대화형 생성/편집/삭제가 포함 됩니다.  

## <a name="limitations"></a>제한 사항
Azure virtual network에 대 한 i p v 6의 미리 보기 릴리스에는 다음과 같은 제한 사항이 있습니다.
- Azure 가상 네트워크 (미리 보기)의 i p v 6은 모든 글로벌 Azure 지역에서 사용할 수 있지만, 정부 클라우드가 아닌 글로벌 Azure 에서만 사용할 수 있습니다.
- 표준 Load Balancer 구성 요소에 대 한 포털 지원은 보기 전용입니다.  그러나 Azure Powershell 및 CLI (명령줄 인터페이스)를 사용 하는 표준 Load Balancer 배포에서는 전체 지원 및 설명서 (샘플 포함)를 사용할 수 있습니다.   
- 미리 보기에 대 한 Network Watcher 지원은 NSG 흐름 로그 및 네트워크 패킷 캡처로 제한 됩니다.
- 가상 네트워크 피어 링 (지역적으로 또는 전역)은 미리 보기에서 지원 되지 않습니다.
- 표준 IPv6 외부 Load Balancer를 사용 하는 경우 다음 제한이 적용 됩니다. 
  - 아웃 바운드 규칙은 여러 프런트 엔드 공용 Ip를 참조할 수 있지만 IPv6 공용 접두사를 참조할 수는 **없습니다** . IP 공용 접두사는 IPv4 접두사만 지원 합니다.
  - IPv6 부하 분산 규칙은 *부동 IP* 기능을 사용 **하지** 않을 수 있습니다. 백 엔드 인스턴스에 대 한 포트 다시 사용은 IPv4 에서만 지원 됩니다.
- 인터넷 연결 IPv6 주소의 블록 예약은 Azure 공용 IP 주소 접두사 기능에서 지원 되지 않습니다.

## <a name="pricing"></a>가격

IPv6 Azure 리소스 및 대역폭은 IPv4와 동일한 요금으로 청구 됩니다. I p v 6에 대 한 추가 요금이 나 다른 요금은 없습니다. [공용 IP 주소](https://azure.microsoft.com/pricing/details/ip-addresses/), [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/)또는 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)에 대 한 가격 책정에 대 한 세부 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell를 사용 하 여 IPv6 이중 스택 응용 프로그램을 배포](virtual-network-ipv4-ipv6-dual-stack-powershell.md)하는 방법을 알아봅니다.
- [Azure CLI를 사용 하 여 IPv6 이중 스택 응용 프로그램을 배포](virtual-network-ipv4-ipv6-dual-stack-cli.md)하는 방법을 알아봅니다.
