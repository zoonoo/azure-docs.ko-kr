---
title: Azure 프로덕션 네트워크
description: 이 문서에서는 Microsoft Azure 프로덕션 네트워크에 대한 일반적인 설명을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101845"
---
# <a name="azure-production-network"></a>Azure 프로덕션 네트워크
Azure 프로덕션 네트워크의 사용자에는 프로덕션 네트워크를 관리하는 내부 Microsoft Azure 지원 담당자 뿐만 아니라 고유한 Microsoft Azure 응용 프로그램에 액세스하는 외부 고객도 포함됩니다. 이 문서에는 보안 액세스 방법과 Azure 프로덕션 네트워크에 대한 연결을 설정하기 위한 보호 메커니즘이 설명되어 있습니다.

## <a name="internet-routing-and-fault-tolerance"></a>인터넷 라우팅 및 내결함성
NetScaler와 같은 추가 Microsoft Azure 네트워크 보안 프로토콜은 DDoS(분산 서비스 거부) 공격을 방지하고 Microsoft Azure DNS 서비스의 무결성을 보호하지만, 여러 기본 및 보조 DNS(Domain Name Service) 서버 클러스터와 함께 전역 중복 내부 및 외부 WADNS(Microsoft Azure Domain Name Service) 인프라는 내결함성을 제공합니다.

WADNS 서버는 여러 데이터 센터 시설에 있습니다. WADNS 구현은 Azure 고객 도메인 이름을 공개적으로 확인하기 위해 보조/기본 DNS 서버 계층을 통합합니다. 도메인 이름은 일반적으로 고객의 서비스에 대한 VIP(가상 IP) 주소를 래핑하는 CloudApp.net 주소로 확인됩니다. Azure에만 고유한 것으로, 테넌트 변환의 내부 DIP(전용 IP) 주소에 해당하는 VIP는 해당 VIP를 담당하는 Microsoft 부하 분산 장치에서 수행됩니다.

Azure는 미국 내의 지리적으로 분산된 Azure 데이터 센터에서 호스트되고 강력하고 확장 가능한 아키텍처 표준을 구현하는 최첨단 라우팅 플랫폼 위에 구축되었습니다. 주목할 만한 일부 기능은 다음과 같습니다.

- 중단이 발생할 경우 효율적인 연결 사용률 및 서비스의 점진적 저하를 제공하는 MPLS(다중 프로토콜 레이블 전환) 기반 트래픽 엔지니어링
- 네트워크는 "필요 수+1"(N+1) 중복 아키텍처 또는 그 이상으로 구현됩니다.
- 외부에서 데이터 센터는 전 세계적으로 여러 피어링 지점에 있으면서 초당 2000기가바이트(Gbps)를 초과하는 에지 용량을 제공하는 1,200개 이상의 인터넷 서비스 공급자에 중복해서 속성을 연결하는 높은 대역폭의 전용 네트워크 회로를 통해 작동됩니다.

Microsoft는 데이터 센터 간에 자체 네트워크 회로를 소유하므로 이러한 특성은 Azure가 전형적인 타사 인터넷 서비스 공급자 없이도 99.9+%의 네트워크 가용성을 달성하도록 도와줍니다.

## <a name="connection-to-production-network-and-associated-firewalls"></a>프로덕션 네트워크 및 관련 방화벽에 대한 연결
Azure 네트워크 인터넷 트래픽 흐름 정책은 미국 내 가장 가까운 지역 데이터 센터에 있는 Azure 프로덕션 네트워크로 트래픽을 전송합니다. Azure 프로덕션 데이터 센터는 일관된 네트워크 아키텍처 및 하드웨어를 유지 관리하므로, 아래에 나오는 트래픽 흐름 설명이 모든 데이터 센터에 일관되게 적용됩니다.

Azure에 대한 인터넷 트래픽이 가장 가까운 데이터 센터로 라우팅되면 액세스 라우터에 대한 연결이 설정됩니다. 이러한 액세스 라우터는 Azure 노드와 고객이 인스턴스화한 VM 간에 트래픽을 격리하는 역할을 합니다. 액세스 및 에지 위치에 있는 네트워크 인프라는 장치는 수신 및/또는 송신 필터가 적용되는 경계 지점입니다. 이러한 라우터는 원치 않는 네트워크 트래픽을 필터링하고 필요한 경우 트래픽 속도 제한을 적용하도록 계층화된 ACL을 통해 구성됩니다. ACL에서 허용하는 트래픽은 부하 분산 장치로 라우팅됩니다. 배포 라우터는 Microsoft에서 승인한 IP 주소만 허용하고, ACL을 사용하여 스푸핑 방지 및 설정된 TCP 연결을 제공하도록 디자인되었습니다.

외부 부하 분산 장치는 액세스 라우터 뒤에 있으며, 인터넷 라우팅이 가능한 IP에서 Azure 내부 IP로의 NAT(Network Address Translation)를 수행합니다. 또한 올바른 프로덕션 내부 IP 및 포트로 패킷을 라우팅하며, 내부 프로덕션 네트워크 주소 공간의 노출을 제한하는 보호 메커니즘의 역할도 합니다.

기본적으로 Microsoft는 로그인과 모든 이후 트래픽을 포함하여 고객의 웹 브라우저로 전송되는 모든 트래픽에 대해 HTTP(Hypertext Transfer Protocol)를 적용합니다. TLS v1.2를 사용하면 트래픽이 보안 터널을 통과할 수 있습니다. 액세스에 대한 ACL 및 핵심 라우터를 통해 트래픽의 원본이 예상되는 항목과 일관된 상태를 유지할 수 있습니다.

기존의 보안 아키텍처에 비해 이 아키텍처의 중요한 차이점은 Azure 프로덕션 환경에 연결되기 전에, 일반적으로 요구되는 전용 하드웨어 방화벽, 특수한 침입 감지/방지 장치 또는 기타 보안 어플라이언스가 없다는 것입니다. 고객은 일반적으로 Azure 네트워크에서 이러한 하드웨어 방화벽 장치를 기대하지만 Azure 내에는 배포된 것이 없습니다. 거의 단독으로, 이러한 보안 기능이 Azure 환경을 운영하는 소프트웨어에 기본적으로 제공되어 방화벽 기능을 포함하는 강력한 다중 계층 보안 메커니즘을 제공합니다. 또한 경계 및 중요한 보안 장치의 관련 확산 범위는 Azure를 운영하는 소프트웨어에서 관리되므로 그림에 나온 것처럼 보다 쉽게 관리하고 인벤토리할 수 있습니다.

## <a name="core-security-and-firewall-features"></a>핵심 보안 및 방화벽 기능
Azure는 강력한 소프트웨어 보안 및 방화벽 기능을 다양한 수준에서 구현하여 기존 환경에서 일반적으로 예상되는 보안 기능을 통해 핵심 보안 권한 부여 경계가 보호됩니다.

### <a name="azure-security-features"></a>Azure 보안 기능
Azure는 프로덕션 네트워크 내에서 호스트 기반 소프트웨어 방화벽을 구현합니다. 몇 가지 핵심 보안 및 방화벽 기능이 핵심 Azure 환경 내에 상주합니다. 이러한 보안 기능에는 Azure 환경 내의 심층 방어 전략이 반영됩니다. Microsoft Azure에서 고객 데이터는 다음 방화벽으로 보호됩니다.

**하이퍼바이저 방화벽(패킷 필터)**: 이 방화벽은 하이퍼바이저에서 구현되고 FC 에이전트에서 구성됩니다. 이 방화벽은 VM 내에서 실행되는 테넌트를 무단 액세스로부터 보호합니다. 기본적으로는 VM을 만들 때 모든 트래픽이 차단되며, FC 에이전트는 필터에 규칙/예외를 추가하여 권한이 부여된 트래픽을 허용합니다.

프로그래밍하는 규칙에는 다음과 같은 두 가지 범주가 있습니다.

- 컴퓨터 구성 또는 인프라 규칙: 기본적으로 모든 통신이 차단됩니다. 예외적으로 VM에서 DHCP(Dynamic Host Configuration Protocol) 통신, DNS 정보를 송수신하고, “공용” 인터넷으로, FC 클러스터 및 OS 정품 인증 서버 내의 다른 VM에 아웃바운드로 트래픽을 전송할 수 있도록 허용하기도 합니다. VM의 나가는 대상 허용 목록에 Microsoft Azure 라우터 서브넷 및 기타 Microsoft 속성은 포함되지 않으므로 이러한 규칙이 이러한 항목에 대한 방어 계층의 역할을 합니다.
- 역할 구성 파일: 테넌트의 서비스 모델에 기반하여 인바운드 ACL을 정의합니다. 예를 들어, 테넌트의 특정 VM에 있는 포트 80에 웹 프런트 엔드가 있는 경우 포트 80은 모든 IP 주소에 열려 있습니다. VM에 실행 중인 작업자 역할이 있는 경우 동일한 테넌트 내에서 VM에만 작업자 역할이 열려 있습니다.

**네이티브 호스트 방화벽** - Microsoft Azure 패브릭 및 Storage는 하이퍼바이저가 없는 기본 OS에서 실행됩니다. 따라서 Windows 방화벽은 위의 두 규칙 집합으로 구성됩니다.

**호스트 방화벽**: 호스트 방화벽은 하이퍼바이저를 실행하는 호스트 파티션을 보호합니다. 규칙은 FC 및 점프 상자만이 특정 포트에서 호스트 파티션과 통신하도록 프로그래밍됩니다. 다른 예외는 DHCP 응답 및 DNS 응답을 허용하는 것입니다. Azure는 호스트 파티션에 대한 방화벽 규칙의 템플릿이 있는 컴퓨터 구성 파일을 사용합니다. VM이 특정 프로토콜/포트를 통해 호스트 구성 요소, 유선 서버 및 메타데이터 서버와 통신할 수 있도록 허용하는 호스트 방화벽 예외도 있습니다.

**게스트 방화벽**: 고객이 고객 VM 및 저장소에서 구성할 수 있는 게스트 OS의 Windows 방화벽 부분입니다.

Azure 기능에 기본 제공되는 추가 보안 기능은 다음과 같습니다.

- 인프라 구성 요소에 DIP(전용 IP)의 IP 주소가 할당됩니다. 이러한 주소로는 Microsoft에 연결할 수 없으므로 인터넷의 공격자가 해당 주소로 트래픽이 전송되도록 지정할 수 없습니다. 인터넷 게이트웨이 라우터는 내부 주소로만 지정된 패킷을 필터링하므로 프로덕션 네트워크에 들어오지 못합니다. VIP로 전송된 트래픽은 허용하는 유일한 구성 요소는 부하 분산 장치입니다.
- 모든 내부 노드에서 구현되는 방화벽에는 지정된 모든 시나리오에 대해 다음과 같은 세 가지 기본 보안 아키텍처 고려 사항이 적용됩니다.

   - LB(부하 분산 장치) 뒤에 배치되며, 모든 위치의 패킷을 허용합니다. 이러한 방화벽은 외부로 노출되며, 기존 경계 방화벽의 열려 있는 포트에 해당합니다.
   - 제한된 주소 집합의 패킷만 허용합니다. 이것은 서비스 거부 공격에 대한 심층 방어 전략의 일부입니다. 이러한 연결은 암호로 인증됩니다.
   - 방화벽은 선택된 내부 노드에서만 액세스할 수 있습니다. 이 경우 모두가 Azure 네트워크 내의 DIP에 해당하는 원본 IP 주소의 열거된 목록에 있는 패킷만 허용합니다. 예를 들어, 회사 네트워크에 대한 공격은 요청을 해당 주소로 전송할 수 있지만, 패킷의 원본 주소가 Azure 네트워크 내의 열거된 목록에 있는 주소가 아니면 차단됩니다.
   - 경계의 액세스 라우터는 구성된 고정 경로 때문에 Azure 네트워크 내에 있는 주소로 지정된 아웃바운드 패킷을 차단합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라 보호를 위해 Microsoft가 수행하는 작업에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 시설, 프레미스 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라의 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 네트워크 아키텍처](azure-infrastructure-network.md)
- [Microsoft Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [Azure 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라의 무결성](azure-infrastructure-integrity.md)
- [Azure에서 고객 데이터 보호](azure-protection-of-customer-data.md)
