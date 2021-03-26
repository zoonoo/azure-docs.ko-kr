---
title: 네트워킹 고려 사항 - Azure Dedicated HSM | Microsoft Docs
description: Azure Dedicated HSM 배포에 적용되는 네트워킹 고려 사항의 개요
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5365ba8c4fbc07c487dd40cfcdc9d566990c493c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607050"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure Dedicated HSM 네트워킹

Azure Dedicated HSM에는 보안 수준이 높은 네트워킹 환경이 필요합니다. 분산형 애플리케이션을 사용하거나 고가용성 시나리오를 위해 Azure 클라우드에서 고객의 IT 환경(온-프레미스)으로 되돌아가는 경우는 true입니다. 이는 Azure 네트워킹을 통해 가능하며 반드시 해결해야 할 4개의 고유 영역이 있습니다.

- Azure의 VNet(Virtual Network) 내에서 HSM 디바이스 만들기
- 온-프레미스를 클라우드 기반 리소스에 연결하여 HSM 디바이스 구성 및 관리
- 가상 네트워크를 만들고 연결하여 애플리케이션 리소스 및 HSM 디바이스 간 상호 연결
- 지역 간 가상 네트워크를 연결하여 상호 통신 및 고가용성 시나리오를 사용 하도록 설정

## <a name="virtual-network-for-your-dedicated-hsms"></a>전용 HSM에 대한 가상 네트워크

전용 HSM은 가상 네트워크에 통합되며 Azure에서 고객 고유의 프라이빗 네트워크에 배치됩니다. 이렇게 하면 가상 머신의 디바이스 또는 가상 네트워크의 컴퓨팅 리소스에 액세스할 수 있습니다.  
Azure 서비스를 가상 네트워크에 통합하는 방법 및 제공되는 기능에 대한 내용은 [Azure 서비스에 대한 가상 네트워크](../virtual-network/virtual-network-for-azure-services.md) 설명서를 참조하세요.

### <a name="virtual-networks"></a>가상 네트워크

전용 HSM 디바이스를 프로비전하기 전에 먼저 고객은 Azure에서 Virtual Network를 만들거나 고객 구독에 이미 있는 Virtual Network를 사용해야 합니다. 가상 네트워크는 전용 HSM 디바이스에 대한 보안 경계를 정의합니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [가상 네트워크 설명서](../virtual-network/virtual-networks-overview.md)를 참조하세요.

### <a name="subnets"></a>서브넷

서브넷은 가상 네트워크를 서브넷에 배치하는 Azure 리소스가 사용할 수 있는 개별 주소 공간으로 구분합니다. 전용 HSM은 가상 네트워크의 서브넷에 배포됩니다. 고객의 서브넷에 배포되는 각 전용 HSM 디바이스는 이 서브넷에서 개인 IP 주소를 수신합니다. HSM 디바이스를 배포하는 서브넷은 명시적으로 서비스(Microsoft.HardwareSecurityModules/dedicatedHSMs)에 위임되어야 합니다. 그러면 서브넷에 배포를 위해 HSM 서비스에 특정 권한이 부여됩니다. 전용 HSM에 위임하면 서브넷에 특정 정책 제한을 적용합니다. NSG(네트워크 보안 그룹) 및 UDR(사용자 정의 경로)은 현재 위임된 서브넷에서 지원되지 않습니다. 결과적으로 전용 HSM에 위임되는 서브넷은 HSM 리소스를 배포하는 데만 사용될 수 있습니다. 따라서 다른 고객의 모든 리소스를 서브넷에 배포하지 못합니다.


### <a name="expressroute-gateway"></a>ExpressRoute 게이트웨이

현재 아키텍처의 요구 사항은 HSM 디바이스를 Azure에 통합할 수 있도록 해당 HSM 디바이스를 배치해야 하는 고객 서브넷에서 ER 게이트웨이를 구성하는 것입니다. 이 ER 게이트웨이는 온-프레미스 위치를 Azure의 고객 HSM 디바이스에 연결하는 데 활용할 수 없습니다.

## <a name="connecting-your-on-premises-it-to-azure"></a>온-프레미스 IT를 Azure에 연결

클라우드 기반 리소스를 만드는 경우 일반적인 요구 사항은 온-프레미스 IT 리소스로 다시 프라이빗 연결하는 것입니다. 전용 HSM의 경우 이 요구 사항은 HSM 클라이언트 소프트웨어가 HSM 디바이스를 구성하고, 또한 분석을 위해 HSM에서 로그를 백업하고 풀링하는 것과 같은 작업이 대부분입니다. 옵션이 있으므로 여기서 주요 결정 포인트는 연결의 특성입니다.  가장 유연한 옵션은 사이트 간 VPN입니다. Azure 클라우드의 리소스(HSM 포함)와 보안 통신이 필요한 여러 온-프레미스 리소스가 있을 수 있기 때문입니다. VPN 디바이스 연결을 용이하게 하려면 고객 조직이 필요합니다. 단일 관리 워크스테이션 같은 단일 엔드포인트 온-프레미스만 있는 경우 지점 및 사이트 간 VPN 연결을 사용할 수 있습니다.
연결 옵션에 대한 자세한 내용은 [VPN Gateway 계획 옵션](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)을 참조하세요.

> [!NOTE]
> 이때 ExpressRoute는 온-프레미스 리소스에 연결하는 옵션이 아닙니다. 위에서 설명한 대로 사용된 ExpressRoute 게이트웨이는 온-프레미스 인프라에 연결하기 위함이 아닙니다.

### <a name="point-to-site-vpn"></a>지점 및 사이트 간 VPN

지점 및 사이트 간 가상 사설망은 단일 엔드포인트 온-프레미스에 보안 연결할 수 있는 가장 간단한 형태입니다. 이 VPN은 Azure 기반 전용 HSM에 대한 단일 관리 워크스테이션만 보유하려는 경우 관련이 있을 수 있습니다.

### <a name="site-to-site-vpn"></a>사이트 간 VPN

사이트 간 가상 사설망을 사용하면 Azure 기반 전용 HSM 및 온-프레미스 IT 간 보안 통신이 가능합니다. 이 작업을 수행 하는 이유는 HSM의 온-프레미스에 대 한 백업 기능이 있고 백업을 실행 하기 위해 둘 사이에 연결이 필요한 이유입니다.

## <a name="connecting-virtual-networks"></a>가상 네트워크 연결

전용 HSM에 대한 일반적인 배포 아키텍처는 단일 가상 네트워크 및 HSM 디바이스를 생성하고 프로비전하는 해당 서브넷에서 시작합니다. 동일한 지역 내에 추가 가상 네트워크 및 전용 HSM를 활용할 수 있는 애플리케이션 구성 요소에 대한 서브넷이 있을 수 있습니다. 이러한 네트워크에서 통신을 사용하도록 설정하려면 Virtual Network 피어링을 사용합니다.

### <a name="virtual-network-peering"></a>가상 네트워크 피어링

서로 다른 사용자의 리소스에 액세스해야 하는 지역 내에 여러 가상 네트워크가 있는 경우 가상 네트워크 간 보안 통신 채널을 만드는 데 Virtual Network 피어링을 사용할 수 있습니다.  가상 네트워크 피어링은 보안 통신을 제공할 뿐 아니라 Azure의 리소스 간 짧은 대기 시간 및 높은 대역폭 연결을 보장합니다.

![네트워크 피어링](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Azure 지역 간 연결

HSM 디바이스에는 소프트웨어 라이브러리를 통해 대체 HSM에 트래픽을 리디렉션할 수 있는 기능이 있습니다. 트래픽 리디렉션은 디바이스에 오류가 발생하거나 디바이스에 대한 액세스 권한을 손실하는 경우 유용합니다. 다른 지역에서 HSM을 배포하고 지역에 걸쳐 가상 네트워크 간 통신을 사용하도록 설정하여 지역 수준 오류 시나리오를 완화할 수 있습니다.

### <a name="cross-region-ha-using-vpn-gateway"></a>VPN 게이트웨이를 사용한 지역 간 HA

글로벌 분산형 애플리케이션 또는 고가용성 지역별 장애 조치(failover) 시나리오의 경우 지역에 걸쳐 가상 네트워크를 연결해야 합니다. Azure Dedicated HSM을 사용하면 두 가상 네트워크 간 보안 터널을 제공하는 VPN Gateway를 통해 고가용성을 구현할 수 있습니다. VPN Gateway를 사용한 Vnet 간 연결에 대한 자세한 내용 [VPN Gateway란?](../vpn-gateway/design.md#V2V) 문서를 참조하세요.

> [!NOTE]
> 글로벌 Vnet 피어링은 전용 HSM을 사용한 지역 간 연결 시나리오에서 사용할 수 없습니다. 대신 VPN 게이트웨이를 사용해야 합니다. 

![다이어그램에는 두 개의 V P N 게이트웨이로 연결 된 두 개의 지역이 표시 됩니다. 각 지역은 피어 링 가상 네트워크를 포함 합니다.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>네트워킹 제한
> [!NOTE]
> HSM 배포에 대 한 대상 네트워크 아키텍처를 설계할 때 고려해 야 하는 제한 사항은 서브넷 위임을 사용한 전용 HSM 서비스의 제약 조건입니다. 서브넷 위임을 사용 하는 경우 NSGs, UDRs 및 글로벌 VNet 피어 링은 전용 HSM에 대해 지원 되지 않습니다. 아래 섹션에서는 이러한 기능에 대해 동일 하거나 비슷한 결과를 얻을 수 있는 다른 방법에 대 한 도움말을 제공 합니다. 

전용 HSM VNet에 있는 HSM NIC는 네트워크 보안 그룹 또는 사용자 정의 경로를 사용할 수 없습니다. 즉, 전용 hsm VNet의 관점에서 기본-거부 정책을 설정할 수 없으며, 전용 HSM 서비스에 대 한 액세스 권한을 얻기 위해 다른 네트워크 세그먼트가 나열 되어 있어야 합니다. 

NVA (네트워크 가상 어플라이언스) 프록시 솔루션을 추가 하면 전송/DMZ 허브의 NVA 방화벽을 HSM NIC 앞에 논리적으로 배치 하 여 NSGs 및 UDRs에 필요한 대안을 제공할 수도 있습니다.

### <a name="solution-architecture"></a>솔루션 아키텍처
이 네트워킹 디자인에는 다음 요소가 필요 합니다.
1.  NVA 프록시 계층이 있는 전송 또는 DMZ 허브 VNet입니다. 이상적으로 두 개 이상의 Nva가 있습니다. 
2.  개인 피어 링이 사용 하도록 설정 되 고 전송 허브 VNet에 대 한 연결이 설정 된 Express 경로 회로입니다.
3.  전송 허브 VNet과 전용 HSM VNet 간의 VNet 피어 링입니다.
4.  NVA 방화벽 또는 Azure 방화벽을 배포 하 여 허브에서 옵션으로 DMZ 서비스를 제공할 수 있습니다. 
5.  추가 워크 로드 스포크 Vnet는 허브 VNet에 피어 링 수 있습니다. Gemalto 클라이언트는 허브 VNet을 통해 전용 HSM 서비스에 액세스할 수 있습니다.

![다이어그램은 NSG 및 UDR 해결을 위한 NVA 프록시 계층을 포함 하는 DMZ 허브 VNet을 보여 줍니다.](media/networking/network-architecture.png)

NVA 프록시 솔루션을 추가 하면 전송/DMZ 허브의 NVA 방화벽을 HSM NIC 앞에 논리적으로 배치할 수 있으므로 필요한 기본 거부 정책을 제공 합니다. 이 예제에서는이 목적으로 Azure 방화벽을 사용 하며 다음 요소가 필요 합니다.
1. DMZ 허브 VNet의 "AzureFirewallSubnet" 서브넷에 배포 된 Azure 방화벽
2. Azure ILB 개인 끝점에 트래픽을 azure 방화벽으로 전달 하는 UDR을 포함 하는 라우팅 테이블입니다. 이 라우팅 테이블은 고객 Express 경로 가상 게이트웨이가 있는 게이트웨이 서브넷에 적용 됩니다.
3. TCP 포트 1792에서 수신 대기 하는 신뢰할 수 있는 원본 범위와 Azure .IBL 개인 끝점 간의 전달을 허용 하는 AzureFirewall 내의 네트워크 보안 규칙. 이 보안 논리는 전용 HSM 서비스에 대해 필요한 "기본 거부" 정책을 추가 합니다. 즉, 신뢰할 수 있는 원본 IP 범위만 전용 HSM 서비스에 허용 됩니다. 다른 모든 범위는 삭제 됩니다.  
4. 트래픽을 온-프레미스에서 Azure 방화벽으로 전달 하는 UDR을 포함 하는 라우팅 테이블입니다. 이 라우팅 테이블은 NVA 프록시 서브넷에 적용 됩니다. 
5. Azure 방화벽의 서브넷 범위만 원본으로 신뢰 하 고 TCP 포트 1792을 통해 HSM NIC IP 주소로 전달 하도록 허용 하기 위해 프록시 NVA 서브넷에 적용 된 NSG입니다. 

> [!NOTE]
> NVA 프록시 계층은 HSM NIC에 전달 되는 클라이언트 IP 주소를 SNAT 하므로 HSM VNet과 DMZ 허브 VNet 간에 UDRs가 필요 하지 않습니다.  

### <a name="alternative-to-udrs"></a>UDRs의 대안
위에서 언급 한 NVA 계층 솔루션은 UDRs 대신 작동 합니다. 유의 해야 할 몇 가지 중요 한 사항이 있습니다.
1.  반환 트래픽을 올바르게 라우팅할 수 있도록 NVA에서 네트워크 주소 변환을 구성 해야 합니다.
2. 고객은 NAT 용 VNA를 사용 하도록 Luna HSM 구성에서 클라이언트 ip 확인을 사용 하지 않도록 설정 해야 합니다. 다음 명령은 ce를 예로 들어 합니다.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  수신 트래픽에 대 한 UDRs를 NVA 계층에 배포 합니다. 
4. 디자인 마다 HSM 서브넷은 플랫폼 계층에 대 한 아웃 바운드 연결 요청을 시작 하지 않습니다.

### <a name="alternative-to-using-global-vnet-peering"></a>Global VNet 피어링 사용에 대 한 대안
글로벌 VNet 피어 링의 대 안으로 사용할 수 있는 몇 가지 아키텍처가 있습니다.
1.  [Vnet 간 VPN Gateway 연결](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 사용 
2.  ER 회로를 사용 하 여 다른 VNET과 HSM VNET을 연결 합니다. 이는 직접 온-프레미스 경로가 필수 또는 VPN VNET 인 경우에 가장 잘 작동 합니다. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>Direct Express 경로 연결을 사용한 HSM
![다이어그램에서 direct Express 경로 연결을 사용한 HSM 표시](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>다음 단계

- [자주 묻는 질문](faq.md)
- [지원 가능성](supportability.md)
- [고가용성](high-availability.md)
- [물리적 보안](physical-security.md)
- [Monitoring](monitoring.md)
- [배포 아키텍처](deployment-architecture.md)
