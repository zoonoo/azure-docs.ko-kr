---
title: Azure 오스트레일리아에서 수신 트래픽 제어
description: 보안 인터넷 게이트웨이의 오스트레일리아 정부 요구 사항을 충족 하기 위해 Azure 오스트레일리아에서 수신 트래픽을 제어 하기 위한 가이드
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 808a615885129af1be9b7fdcdb64d5a8c5a25e40
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571654"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Azure 오스트레일리아에서 수신 트래픽 제어

ICT 시스템 보안의 핵심 요소는 네트워크 트래픽을 제어 하는 것입니다. 시스템이 손상 가능성을 줄이기 위해 작동 하는 데 필요한 경우에만 트래픽을 제한 해야 합니다.

이 가이드에서는 Azure에서 인바운드 (수신) 네트워크 트래픽이 작동 하는 방법 및 인터넷에 연결 된 시스템에 대 한 네트워크 보안 제어를 구현 하기 위한 권장 사항을 설명 합니다.

네트워크 컨트롤은 ACSC (오스트레일리아 사이버 Security Center) 소비자 지침과 ACSC의 사용자 ISM (정보 보안 설명서)에 맞게 정렬 됩니다.

## <a name="requirements"></a>요구 사항

시스템에 대 한 전반적인 보안 요구 사항은 ISM에 정의 되어 있습니다. 네트워크 보안을 구현 하는 데 도움이 되는 acsc는 acsc Protect를 게시 [했습니다. 네트워크 구분 및 분리](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)구현 및 클라우드 환경에서 시스템 보안을 지원 하기 위해 acsc는 [테 넌 트에 대해 클라우드 컴퓨팅 보안](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf)을 게시 했습니다.

이러한 가이드에서는 네트워크 보안을 구현 하 고 트래픽을 제어 하 고 네트워크 설계 및 구성에 대 한 실용적인 권장 사항을 포함 하는 컨텍스트를 간략하게 설명 합니다.

서비스 신뢰 포털의 오스트레일리아 페이지에 있는 [Microsoft Azure 테 넌 트의 테 넌 트에 대 한 Microsoft 클라우드 컴퓨팅 보안](https://aka.ms/au-irap) 가이드에는 acsc 게시의 조언을 충족 시킬 수 있는 특정 Microsoft 기술이 강조 표시 되어 있습니다.

ACSC의 게시에서 확인 되는 다음 주요 요구 사항은 Azure에서 수신 트래픽을 제어 하는 데 중요 합니다.

|Description|Source|
|---|---|
|**호스트 기반 방화벽 및 CSP의 네트워크 액세스 제어를 사용 하 여 필요한 포트/프로토콜로만 인바운드 및 아웃 바운드 VM 네트워크 연결을 제한 하는 n 계층 아키텍처와 같은 네트워크 조각화 및 분리를 구현 합니다.**| _테 넌 트에 대 한 클라우드 컴퓨팅_|
|테 넌 트의 가용성 요구 사항을 충족 하기 위해 테 넌 트 (테 넌 트의 원격 사용자 포함)와 클라우드 서비스 간에 **적절 한 높은 대역폭, 짧은 대기 시간, 안정적인 네트워크 연결을 구현** 합니다.  | _테 넌 트에 대 한 클라우드 컴퓨팅_|
|**네트워크 계층 이상 에서만 기술을 적용**합니다. 각 호스트 및 네트워크는 가능 하면 실제로 관리할 수 있는 가장 낮은 수준에서 조각화 및 분리 되어야 합니다. 대부분의 경우 조각화 및 분리는 응용 프로그램 계층을 포함 하 여 데이터 링크 계층에서 적용 됩니다. 그러나 중요 한 환경에서는 물리적 격리가 적절할 수 있습니다. 호스트 기반 및 네트워크 차원의 측정값을 보완 방식으로 배포 하 고 중앙 집중식으로 모니터링 해야 합니다. 유일한 보안 조치로 방화벽이 나 보안 어플라이언스를 사용 하는 것 만으로는 충분 하지 않습니다. |_ACSC 보호: 네트워크 분할 및 분리 구현_|
|**최소 권한의 원칙을 사용 하 고 ‐ ‐ 해야**합니다. 호스트, 서비스 또는 네트워크가 다른 호스트, 서비스 또는 네트워크와 통신할 필요가 없는 경우에는이를 허용 하지 않아야 합니다. 호스트, 서비스 또는 네트워크에서 특정 포트나 프로토콜을 사용 하는 다른 호스트, 서비스 또는 네트워크와만 통신 해야 하는 경우 다른 포트나 프로토콜을 사용 하지 않도록 설정 해야 합니다. 네트워크에서 이러한 원칙을 채택 하면 사용자 권한 minimisation을 보완 하 고 환경의 전반적인 보안 상태를 크게 증가 시킵니다. |_ACSC 보호: 네트워크 분할 및 분리 구현_|
|**비즈니스 운영에 대 한 민감도 또는 중요도에 따라 호스트와 네트워크를 분리**합니다. 특정 호스트 또는 네트워크에 대 한 다양 한 보안 분류, 보안 도메인 또는 가용성/무결성 요구 사항에 따라 다른 하드웨어 또는 플랫폼을 사용 하 여 분리를 구현할 수 있습니다. 특히, 관리 네트워크를 분리 하 고 중요 한 환경에 대해 대역 외 관리 네트워크를 물리적으로 격리 하는 것이 좋습니다. |_ACSC 보호: 네트워크 분할 및 분리 구현_|
|**모든 엔터티에서 다른 모든 엔터티에 대 한 ise 액세스를 식별, 인증 및 권한**부여 합니다. 모든 사용자, 호스트 및 서비스는 지정 된 의무 또는 기능을 수행 하는 데 필요한 다른 사용자, 호스트 및 서비스에 대 한 액세스를 제한 해야 합니다. Id, 인증 및 권한 부여 서비스의 강도를 우회 하거나 다운 그레이드 하는 모든 레거시 또는 로컬 서비스를 사용 하지 않도록 설정 하 고 해당 사용을 면밀 하 게 모니터링 해야 합니다. |_ACSC 보호: 네트워크 분할 및 분리 구현_|
|**거부 목록 대신 네트워크 트래픽 목록 허용을 구현**합니다. 알려진 불량 네트워크 트래픽 (예: 특정 주소 또는 서비스 차단)에 대 한 액세스를 거부 하는 것이 아니라 알려진 양호한 네트워크 트래픽 (즉, 식별 된, 인증 된 및 공인)에 대 한 액세스만 허용 합니다. 허용 목록를 통해 뛰어난 보안 정책을 블랙 리스트에 표시 하 고 잠재적인 네트워크 침입을 검색 하 고 평가 하는 조직의 용량을 크게 향상 시킬 수 있습니다. |_ACSC 보호: 네트워크 분할 및 분리 구현_|
|

이 문서에서는 IaaS (Infrastructure as a Service) 및 PaaS (Platform as a service)를 사용 하 여 Azure에 배포 된 시스템에 대 한 이러한 요구 사항을 충족 하는 방법에 대 한 정보와 권장 사항을 제공 합니다. Azure 내에서 네트워크 트래픽 제어를 완전히 이해 하려면 [Azure 오스트레일리아에서 송신 트래픽 제어](gateway-egress-traffic.md) 에 대 한 문서도 참조 해야 합니다.

## <a name="architecture"></a>아키텍처

네트워크 보안 및 수신 트래픽 제어의 설계 또는 구현에 참여 하는 경우 먼저 Azure 내에서 네트워크 트래픽이 IaaS 및 PaaS 모두에서 작동 하는 방식을 이해 해야 합니다. 이 섹션에서는 네트워크 트래픽이 Azure에서 호스트 되는 리소스에 도달할 수 있는 가능한 진입점의 개요를 제공 하 고 해당 트래픽을 제한 하 고 제어 하는 데 사용할 수 있는 보안 제어를 제공 합니다. 이러한 각 구성 요소에 대해서는이 가이드의 나머지 단원에서 자세히 설명 합니다.

### <a name="architecture-components"></a>아키텍처 구성 요소

여기에 표시 된 아키텍처 다이어그램에서는 네트워크 트래픽이 Azure에서 호스팅되는 서비스에 연결 하는 데 사용할 수 있는 경로를 보여 줍니다. 이러한 구성 요소는 수신 트래픽에 대해 제공 하는 기능에 따라 Azure, IaaS 수신, PaaS 수신 및 보안 제어로 나뉩니다.

![아키텍처](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure 구성 요소

|구성 요소 | Description|
|---|---|
|**DDoS Protection** | DDoS (배포 된 서비스 거부) 공격은 응용 프로그램의 리소스를 고갈 시켜 합법적인 사용자가 응용 프로그램을 사용할 수 없게 합니다. 인터넷을 통해 공개적으로 도달 가능한 모든 엔드포인트는 DDoS 공격의 대상이 될 수 있습니다. Azure는 Azure 플랫폼을 통해 DDoS Protection를 자동으로 포함 하 고, 보다 세분화 된 제어를 위해 특정 응용 프로그램에 사용할 수 있는 추가 완화 기능을 제공 합니다.|
| **Traffic Manager** | Azure Traffic Manager는 고가용성 및 응답성을 제공 하면서 Azure 지역 전체에서 서비스에 최적의 트래픽을 분산할 수 있는 DNS (Domain Name System) 기반 트래픽 부하 분산 장치입니다. Traffic Manager는 DNS를 사용 하 여 트래픽 라우팅 메서드 및 끝점의 상태에 따라 가장 적합 한 끝점으로 클라이언트 요청을 보냅니다.|
| **ExpressRoute** | Express 경로는 Microsoft 클라우드 서비스를 사용 하기 위한 전용 네트워크 연결입니다. 이는 연결 공급자를 통해 프로 비전 되며 인터넷을 통한 일반 연결 보다 안정성, 빠른 속도, 짧은 대기 시간 및 높은 보안을 제공 합니다. Express 경로 회로는 연결 공급자를 통한 온-프레미스 인프라와 Microsoft 클라우드 서비스 간의 논리적 연결을 나타냅니다.|
| **Express 경로 개인 피어 링** | Express 경로 개인 피어 링은 온-프레미스 환경과 개인 Azure 가상 네트워크 간의 연결입니다. 개인 피어 링을 사용 하면 가상 네트워크 내에 배포 된 Virtual Machines와 같은 Azure 서비스에 액세스할 수 있습니다. 개인 피어 링을 통해 액세스 하는 리소스와 가상 네트워크는 조직의 핵심 네트워크를 확장 하는 것으로 간주 됩니다. 개인 피어 링은 개인 IP 주소를 사용 하 여 온-프레미스 네트워크와 Azure 가상 네트워크 간에 양방향 연결을 제공 합니다.|
| **Express 경로 Microsoft 피어 링** | Express 경로 Microsoft 피어 링은 온-프레미스 환경과 Microsoft 및 Azure 공용 서비스 간의 연결입니다. 여기에는 Office 365, Dynamics 365 및 Azure PaaS 서비스에 대 한 연결이 포함 됩니다. 피어 링은 조직 또는 연결 공급자가 소유한 공용 IP 주소에 대해 설정 됩니다. Express 경로 Microsoft 피어 링을 통해 기본적으로 액세스할 수 있는 서비스가 없으며, 조직에서 필요한 서비스를 옵트인 (opt in) 해야 합니다. 그런 다음이 프로세스는 인터넷에서 사용할 수 있는 동일한 끝점에 대 한 연결을 제공 합니다.|
|

### <a name="iaas-ingress-components"></a>IaaS 수신 구성 요소

|구성 요소 | 설명|
|---|---|
|**네트워크 인터페이스** | 네트워크 인터페이스는 Azure에 존재 하는 리소스입니다. 가상 컴퓨터에 연결 되 고, 연결 된 서브넷에서 인터넷을 통해 라우팅할 수 있는 개인 IP 주소가 할당 됩니다. 이 IP 주소는 Azure Resource Manager을 통해 동적 또는 정적으로 할당 됩니다.|
|**서브넷** | 서브넷은 VNet 내에 생성 되는 IP 주소 범위입니다. 네트워크 조각화를 위해 VNet 내에서 여러 서브넷을 만들 수 있습니다.|
| **VNet (Virtual Network)** | VNet은 Azure 내에서 리소스를 배포 하 고 통신을 가능 하 게 하는 플랫폼 및 경계를 제공 하는 기본 리소스입니다. VNet은 Azure 지역 내에 있으며 Virtual Machines와 같은 VNet 통합 리소스에 대 한 IP 주소 공간 및 라우팅 경계를 정의 합니다.|
| **VNet 피어링** | VNet 피어 링은 Virtual Network 게이트웨이가 필요 하지 않고 두 Vnet 간의 직접 통신을 가능 하 게 하는 Azure 구성 옵션입니다. 피어 링 하면 두 Vnet가 직접 통신할 수 있으며 추가 구성은 Virtual Network 게이트웨이 및 기타 전송 옵션의 사용을 제어할 수 있습니다.|
| **공용 IP** | 공용 IP는 가상 네트워크 내에서 사용 하기 위해 지정 된 지역에서 Microsoft 소유의 공용 인터넷 라우팅 가능 IP 주소 중 하나를 예약 하는 리소스입니다. 이를 특정 네트워크 인터페이스와 연결 하 여 인터넷, Express 경로 및 PaaS 시스템에서 리소스에 액세스할 수 있습니다.|
| **Express 경로 게이트웨이** | Express 경로 게이트웨이는 Express 경로 회로의 개인 피어 링을 통해 Virtual Network에서 온-프레미스 네트워크로의 연결과 라우팅을 제공 하는 Virtual Network의 개체입니다.|
| **VPN Gateway** | VPN Gateway은 Virtual Network에서 외부 네트워크로 암호화 된 터널을 제공 하는 Virtual Network의 개체입니다. 암호화 된 터널은 단일 끝점을 사용 하는 통신을 위해 온-프레미스 환경, 다른 가상 네트워크 또는 클라우드 환경 또는 지점 및 사이트 간 통신을 위해 양방향 통신을 위해 사이트 간 사이트 일 수 있습니다.|
| **PaaS VNet 통합** | 많은 PaaS 기능을 Virtual Network에 배포 하거나에 통합할 수 있습니다. 일부 PaaS 기능은 VNet과 완전히 통합 되 고 개인 IP 주소를 통해서만 액세스할 수 있습니다. Azure Load Balancer 및 Azure 애플리케이션 게이트웨이와 같은 다른 항목에는 공용 IP 주소를 포함 하는 외부 인터페이스와 가상 네트워크 내의 개인 IP 주소가 있는 내부 인터페이스가 있을 수 있습니다. 이 인스턴스에서 트래픽은 PaaS 기능을 통해 Virtual Network으로 수신 될 수 있습니다.|
|

### <a name="paas-ingress-components"></a>PaaS 수신 구성 요소

|구성 요소 | 설명|
|---|---|
|**호스트 이름** | Azure PaaS 기능은 리소스를 만들 때 할당 되는 고유한 공용 호스트 이름으로 식별 됩니다. 그런 다음 공용 IP 주소로 확인할 수 있는 공용 DNS 도메인에이 호스트 이름을 등록 합니다.|
|**공용 IP** | VNet 통합 구성에 배포 되지 않은 경우 Azure PaaS 기능은 공용의 인터넷 라우팅 가능한 IP 주소를 통해 액세스 됩니다. 이 주소는 공용 Load Balancer 같은 특정 리소스에 대해 전용 이거나, 저장소 또는 SQL과 같은 여러 인스턴스에 대 한 공유 진입점이 있는 특정 기능과 연결 될 수 있습니다. 이 공용 IP 주소는 Azure 백본 네트워크를 통해 인터넷, Express 경로 또는 IaaS 공용 IP 주소에서 액세스할 수 있습니다.|
|**서비스 엔드포인트** | 서비스 끝점은 Virtual Network에서 특정 PaaS 기능으로의 직접 개인 연결을 제공 합니다. PaaS 기능 하위 집합에만 사용할 수 있는 서비스 끝점은 PaaS에 액세스 하는 VNet의 리소스에 대 한 향상 된 성능 및 보안을 제공 합니다.|
|

### <a name="security-controls"></a>보안 컨트롤

|구성 요소 | Description|
|---|---|
|**NSGs (네트워크 보안 그룹)** | NSGs는 Azure에서 가상 네트워크 리소스에 대 한 트래픽을 제어 합니다. NSGs는 Azure 내의 트래픽과 온-프레미스 또는 인터넷과 같은 외부 네트워크와 Azure 간의 트래픽을 포함 하 여 허용 되거나 거부 되는 트래픽 흐름에 대 한 규칙을 적용 합니다. NSGs는 가상 네트워크 내의 서브넷 또는 개별 네트워크 인터페이스에 적용 됩니다.|
|**PaaS 방화벽** | 저장소 및 SQL과 같은 많은 PaaS 기능에는 특정 리소스에 대 한 수신 네트워크 트래픽을 제어 하기 위한 기본 제공 방화벽이 있습니다. 특정 IP 주소 및/또는 가상 네트워크에서 연결을 허용 하거나 거부 하는 규칙을 만들 수 있습니다.|
|**PaaS 인증 및 Access Control** | 계층화 된 보안 방식의 일부로 PaaS 기능은 사용자를 인증 하 고 권한 및 액세스를 제어 하는 여러 메커니즘을 제공 합니다.|
|**Azure Policy** | Azure Policy는 정책을 만들고 할당 하 고 관리 하는 Azure의 서비스입니다. 이러한 정책은 규칙을 사용 하 여 배포할 수 있는 리소스 유형과 해당 리소스의 구성을 제어 합니다. 정책을 사용 하면 요구 사항을 충족 하지 않거나 모니터링에 호환성 상태를 보고 하는 데 사용할 수 있는 경우 리소스를 배포 하지 못하도록 하 여 준수를 강제 적용할 수 있습니다.|
|

## <a name="general-guidance"></a>일반 지침

Azure 내에서 보안 솔루션을 설계 하 고 빌드하려면 네트워크 트래픽을 이해 하 고 제어 하는 것이 중요 하며,이를 확인 하 고 공인 통신만 수행할 수 있습니다. 이 지침의 용도와 이후 섹션에서 특정 구성 요소 지침은 _acsc Protect에 설명 된 원칙을 적용 하기 위해 utilised 수 있는 도구와 서비스를 설명 하는 것입니다. Azure 워크 로드에서 네트워크_ 조각화 및 분리 구현. 여기에는 온-프레미스 환경에서 사용할 수 있는 것과 동일한 기존 물리적 및 네트워크 컨트롤을 적용할 수 없는 경우 리소스를 보호 하기 위한 가상 아키텍처를 만드는 방법을 자세히 설명 합니다.

### <a name="specific-focus-areas"></a>특정 주요 영역

* 진입점의 수를 가상 네트워크로 제한
* 공용 IP 주소의 수를 제한 합니다.
* Microsoft 가상 데이터 센터 (VDC) 설명서에 설명 된 대로 가상 네트워크에 대 한 허브 및 스포크 네트워크 디자인을 utilising 고려 합니다.
* 인터넷의 인바운드 연결에 대 한 기본 제공 보안 기능으로 제품 제품 (예: Application Gateway, API 게이트웨이, 네트워크 가상 어플라이언스)
* 시스템 기능에 필요한 경우에만 통신 흐름을 PaaS 기능으로 제한
* 분리 및 제어를 강화 하기 위해 VNet 통합 구성에서 PaaS 배포
* ACSC 소비자 지침 및 ISM에서 온라인으로 암호화 메커니즘을 사용 하도록 시스템 구성
* 기존 네트워크 컨트롤 외에도 인증 및 역할 기반 액세스 제어와 같은 id 기반 보호를 사용 합니다.
* 온-프레미스 네트워크와 연결 하기 위한 Express 경로 구현
* 관리 트래픽 및 외부 네트워크와의 통합을 위한 Vpn 구현
* 제품은 지역 및 리소스를 시스템 기능에 필요한 것 으로만 제한 하는 Azure Policy 합니다.
* 인터넷 액세스 가능한 리소스에 대 한 기준선 보안 구성을 적용 Azure Policy 제품

### <a name="additional-resources"></a>추가 자료

|리소스 | 링크|
|---|---|
|오스트레일리아 규정 및 정책 준수 문서 (소비자 지침 포함)|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 가상 데이터 센터|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC 네트워크 조각화|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|테 넌 트 용 ACSC 클라우드 보안| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC Information Security 수동|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>구성 요소 지침

이 섹션에서는 Azure에 배포 된 시스템에 대 한 수신 트래픽과 관련 된 개별 구성 요소에 대 한 추가 지침을 제공 합니다. 각 섹션에서는 디자인 및 빌드 작업을 지 원하는 데 사용할 수 있는 설명서 및 구성 가이드에 대 한 링크가 있는 특정 구성 요소의 의도를 설명 합니다.

## <a name="azure"></a>Azure

Azure 내의 리소스에 대 한 모든 통신은 연결 및 보안 기능을 제공 하는 Microsoft에서 유지 관리 하는 네트워크 인프라를 통과 합니다. Azure 플랫폼과 네트워크 인프라를 보호 하기 위해 Microsoft에서 자동으로 보호 범위를 설정 하 고, 추가 기능을 Azure 내에서 서비스로 사용 하 여 네트워크 트래픽을 제어 하 고 네트워크 조각화를 설정 하 고, 분리.

### <a name="ddos-protection"></a>DDoS Protection

인터넷에 액세스할 수 있는 리소스는 DDoS 공격에 취약 합니다. 이러한 공격 으로부터 보호 하기 위해 Azure는 기본 및 표준 수준에서 DDoS 보호 기능을 제공 합니다.

기본은 상시 트래픽 모니터링과 일반적인 네트워크 수준 공격에 대 한 실시간 완화를 포함 하 여 Azure 플랫폼의 일부로 자동으로 사용 하도록 설정 되어 Microsoft 온라인 서비스에서 동일한 defences utilised을 제공 합니다. Azure 글로벌 네트워크의 전체 규모를 활용하여 전체 지역에서 공격 트래픽을 분산하고 완화할 수 있습니다. IPv4 및 IPv6 Azure 공용 IP 주소에 대해 보호가 제공 됩니다.

Standard는 Azure Virtual Network 리소스에 맞게 특별히 조정 된 기본 서비스 계층에 대 한 추가 완화 기능을 제공 합니다. 보호 정책은 전용 트래픽 모니터링 및 기계 학습 알고리즘을 통해 조정됩니다. IPv4 Azure 공용 IP 주소에 대한 보호가 제공됩니다.

|리소스|링크|
|---|---|
|Azure DDoS Protection 개요|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Azure DDoS 모범 사례|[https://docs.microsoft.com/azure/security/azure-ddos-best-practices](https://docs.microsoft.com/azure/security/azure-ddos-best-practices)|
|DDoS Protection 관리|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager는 연결을 수신 하는 응용 프로그램의 끝점을 제어 하 여 수신 트래픽을 관리 하는 데 사용 됩니다. 사이버 보안 공격으로 인 한 시스템 또는 응용 프로그램의 가용성 손실이 나 시스템 손상 후 서비스를 복구 하는 것을 방지 하기 위해 Traffic Manager를 사용 하 여 사용 가능한 작동 중인 응용 프로그램 인스턴스로 트래픽을 리디렉션할 수 있습니다.

|리소스|링크|
|---|---|
|Traffic Manager 개요 | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Azure DNS 및 Traffic Manager를 사용 하 여 재해 복구 가이드 | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

Express 경로를 사용 하 여 온-프레미스 환경에서 Azure에 호스트 된 시스템으로 개인 경로를 설정할 수 있습니다. 이 연결을 통해 네트워크 통신을 위해 향상 된 개인 정보를 통해 더 높은 안정성 및 보장 된 성능을 제공할 수 있습니다. Express 경로를 통해 사내 엔터티는 온-프레미스 환경에서 인바운드 트래픽을 제어 하 고 인바운드 방화벽 규칙 및 액세스 제어 목록에 사용할 조직에 특정 한 전용 주소를 정의할 수 있습니다.

|리소스 | 링크|
|---|---|
|ExpressRoute 개요 | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|Express 경로 연결 모델 | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Express 경로 개인 피어 링

개인 피어 링은 개인 IP 주소만 사용 하 여 온-프레미스 환경을 Azure로 확장 하는 메커니즘을 제공 합니다. 이를 통해 Azure 가상 네트워크 및 주소 범위를 기존 온-프레미스 시스템 및 서비스와 통합할 수 있습니다. 개인 피어 링은 조직에서 공인 하는 가상 네트워크에만 Express 경로 간 통신을 보장 합니다. 개인 피어 링을 사용 하는 경우 Azure VPN Gateway 대신 NVA (네트워크 가상 어플라이언스)를 구현 하 여 ACSC 소비자 지침에서 요구 하는 대로 온-프레미스 네트워크에 대 한 보안 VPN 통신을 설정 해야 합니다.

|리소스 | 링크|
|---|---|
|Express 경로 개인 피어 링 개요 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Express 경로 개인 피어 링 방법 가이드 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>Express 경로 Microsoft 피어 링

Microsoft 피어 링은 인터넷을 트래버스 하지 않고도 Microsoft 공용 서비스에 대 한 고속 및 짧은 대기 시간 연결을 제공 합니다. 이를 통해 연결에 대 한 안정성, 성능 및 개인 정보를 향상 합니다. 경로 필터를 사용 하 여 Azure 지역에만 필요한 Azure 지역에 대 한 통신을 제한할 수 있지만 여기에는 다른 조직에서 호스트 되는 서비스가 포함 되며, 온-프레미스 환경 및 Microsoft.

사내 엔터티는 피어 링 관계를 통해 설정 된 전용 공용 IP 주소를 사용 하 여 PaaS 기능 내의 방화벽 및 액세스 제어 목록에서 사용할 온-프레미스 환경을 고유 하 게 식별할 수 있습니다.

대 안으로, Azure VPN Gateway를 통해 VPN 연결을 설정 하기 위한 언더레이 네트워크로 Express 경로 Microsoft 피어 링을 사용할 수 있습니다. 이 모델에서는 Express 경로를 통해 내부 온-프레미스 네트워크에서 Azure 공용 서비스에 대 한 활성 통신이 없으며, ACSC 소비자 지침에 따라 개인 가상 네트워크에 대 한 보안 연결을 구현 합니다.

|리소스 | 링크|
|---|---|
|Express 경로 Microsoft 피어 링 개요 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Express 경로 Microsoft 피어 링 방법 가이드 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS 수신

이 섹션에서는 IaaS 구성 요소에 대 한 수신 트래픽을 제어 하기 위한 구성 요소 지침을 제공 합니다. IaaS에는 Azure의 Virtual Network 내에서 배포 및 관리할 수 있는 Virtual Machines 및 기타 계산 리소스가 포함 되어 있습니다. IaaS를 사용 하 여 배포 된 시스템에 트래픽이 도착 하려면 Virtual Network에 대 한 진입점이 있어야 합니다 .이 진입점은 공용 IP 주소, Virtual Network 게이트웨이 또는 Virtual Network 피어 링 관계를 통해 설정할 수 있습니다.

### <a name="network-interface"></a>네트워크 인터페이스

네트워크 인터페이스는 가상 머신에 대 한 모든 트래픽에 대 한 수신 위치입니다. 네트워크 인터페이스를 사용 하면 IP 주소 지정을 구성할 수 있으며, NSGs를 적용 하거나 네트워크 가상 어플라이언스를 통해 트래픽을 라우팅하는 데 사용할 수 있습니다. Virtual Machines에 대 한 네트워크 인터페이스는 전체 네트워크 조각화 및 분리 목표에 맞게 계획 하 고 적절 하 게 구성 해야 합니다.

|리소스 | 링크|
|---|---|
|네트워크 인터페이스 만들기, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|네트워크 인터페이스 IP 주소 지정 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

서브넷은 Azure 내에서 네트워크 조각화 및 분리을 위한 중요 한 구성 요소입니다. 서브넷은 유사 하 게 시스템을 분리 하는 데 사용할 수 있습니다. NSGs는 서브넷에 적용 하 여 수신 통신 흐름을 시스템 기능에 필요한 것 으로만 제한할 수 있습니다. 서브넷은 방화벽 규칙과 액세스 제어 목록의 원본 및 대상 주소로 사용할 수 있으며, 서비스 끝점에서 PaaS 기능에 대 한 연결을 제공 하도록 구성할 수 있습니다.

|리소스 | 링크|
|---|---|
|가상 네트워크 서브넷 추가, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Virtual Network(VNet)

Vnet는 Azure의 네트워킹에 대 한 기본 구성 요소 중 하나입니다. 가상 네트워크는 다양 한 시스템에서 사용할 IP 주소 공간 및 라우팅 경계를 정의 합니다. 가상 네트워크는 서브넷으로 나뉘어 Virtual Network 내의 모든 서브넷은 서로 직접 네트워크 경로를 가집니다. Virtual Network 게이트웨이 (Express 경로 또는 VPN)를 사용 하 여 온-프레미스 및 외부 환경에서 Virtual Network 내의 시스템에 액세스할 수 있습니다. 가상 네트워크 및 관련 구성 매개 변수 및 라우팅에 대 한 이해는 수신 네트워크 트래픽을 이해 하 고 제어 하는 데 매우 중요 합니다.

|리소스 | 링크|
|---|---|
|가상 네트워크 개요 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Virtual Networks 계획 방법 가이드 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Virtual Network 빠른 시작 만들기 | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>VNet 피어 링

VNet 피어 링은 두 가상 네트워크 간의 직접 통신 경로를 제공 하는 데 사용 됩니다. 피어 링이 설정 되 면 한 Virtual Network의 호스트는 다른 Virtual Network의 호스트에 직접 고속 라우팅 경로를 가집니다. NSGs는 정상적으로 트래픽에 적용 되 고 고급 구성 매개 변수를 사용 하 여 Virtual Network 게이트웨이 또는 다른 외부 시스템을 통한 통신이 허용 되는지 여부를 정의할 수 있습니다.

|리소스 | 링크|
|---|---|
|Virtual Network 피어 링 개요 |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|가상 네트워크 피어링 만들기, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>VNET의 공용 IP

공용 IP 주소는 Virtual Network에 배포 된 서비스에 대 한 수신 통신 경로를 제공 하는 데 사용 됩니다. 이러한 엔터티는 공용 IP 주소 할당을 신중 하 게 계획 하 고, 진짜 요구 사항이 있는 리소스에만 할당 해야 합니다. 일반적으로 Application Gateway 또는 네트워크 가상 어플라이언스와 같은 기본 제공 보안 기능을 사용 하 여 리소스에 공용 IP 주소를 할당 하 여 Virtual Network에 대 한 보안 제어 된 공용 진입점을 제공 해야 합니다.

|리소스 | 링크|
|---|---|
|공용 IP 주소 개요 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|공용 IP 주소 만들기, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>Express 경로 게이트웨이

Express 경로 게이트웨이는 온-프레미스 환경에서 수신 지점을 제공 하므로 보안, 가용성, 재무 및 성능 요구 사항을 충족 하기 위해 배포 해야 합니다. Express 경로 게이트웨이는 정의 된 네트워크 대역폭을 제공 하 고 배포 후 사용 비용을 발생 시킵니다. 가상 네트워크는 Express 경로 게이트웨이를 하나만 포함할 수 있지만이는 여러 Express 경로 회로에 연결 될 수 있으며 VNet 피어 링을 통해 여러 가상 네트워크에서 활용 하 여 여러 가상 네트워크에서 대역폭과 연결을 공유할 수 있습니다. Express 경로 게이트웨이를 사용 하 여 온-프레미스 환경과 가상 네트워크 간의 라우팅을 구성 하 여 알려진 제어 된 네트워크 수신 지점의 종단 간 연결을 보장 해야 합니다. Express 경로 게이트웨이를 사용 하는 사내 엔터티는 ACSC 소비자 지침을 준수 하기 위해 온-프레미스 환경에 대 한 VPN 연결을 설정 하기 위해 네트워크 가상 어플라이언스도 배포 해야 합니다.

|리소스 | 링크|
|---|---|
|Express 경로 게이트웨이 개요 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|ExpressRoute에 대한 가상 네트워크 게이트웨이 구성 | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN 게이트웨이

Azure VPN Gateway는 보안 사이트 간 또는 지점 및 사이트 간 연결을 위해 외부 네트워크에서 수신 네트워크 지점을 제공 합니다. VPN gateway는 정의 된 네트워크 대역폭을 제공 하 고 배포 후 사용 비용을 발생 시킵니다. Utilising VPN Gateway은 ACSC 소비자 지침에 따라 구성 되었는지 확인 해야 합니다. 가상 네트워크에는 VPN Gateway 하나만 있을 수 있지만이는 여러 터널로 구성할 수 있으며 VNet 피어 링을 통해 여러 가상 네트워크에서 활용 하 여 여러 가상 네트워크에서 대역폭과 연결을 공유할 수 있습니다. VPN 게이트웨이는 인터넷을 통해 설정 하거나 Microsoft 피어 링을 통해 Express 경로를 통해 설정할 수 있습니다.

|리소스 | 링크|
|---|---|
|VPN Gateway 개요 | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|VPN Gateway 계획 및 설계 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|오스트레일리아 정부 기관에 대 한 VPN Gateway 구성|[오스트레일리아 정부 기관에 필요한 IPSEC 구성](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS VNet 통합

PaaS를 활용 하면 향상 된 기능 및 가용성을 제공 하 고 관리 오버 헤드를 줄일 수 있지만 적절 하 게 보호 해야 합니다. 제어를 강화 하거나, 네트워크 조각화를 적용 하거나, 응용 프로그램 및 서비스에 대 한 보안 수신 진입점을 제공 하기 위해 많은 PaaS 기능을 Virtual Network와 통합할 수 있습니다.

보안 진입점을 제공 하기 위해 Application Gateway와 같은 PaaS 기능을 외부 공용 인터페이스 및 응용 프로그램 서비스와 통신 하기 위한 내부 전용 인터페이스를 사용 하 여 구성할 수 있습니다. 이렇게 하면 공용 IP 주소를 사용 하 여 응용 프로그램 서버를 구성 하 여 외부 네트워크에 노출할 필요가 없습니다.

시스템 또는 응용 프로그램 아키텍처의 통합 된 일부로 PaaS를 사용 하기 위해 Microsoft는 PaaS를 Virtual Network에 배포 하는 여러 메커니즘을 제공 합니다. 배포 방법론은 내부 시스템 및 응용 프로그램과의 연결 및 통합을 제공 하는 동시에 인터넷과 같은 외부 네트워크에서 인바운드 액세스를 제한 합니다. 예를 들면 App Service 환경, SQL 관리 되는 인스턴스 등이 있습니다.

|리소스 | 링크|
|---|---|
|Azure 서비스에 대한 가상 네트워크 통합 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Azure Virtual Network 방법 가이드와 앱 통합 | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS 수신

PaaS 기능은 향상 된 기능 및 간소화 된 관리에 대 한 기회를 제공 하지만 네트워크 구분 및 분리 요구 사항에 대 한 복잡성을 소개 합니다. PaaS 기능은 일반적으로 공용 IP 주소를 사용 하 여 구성 되며 인터넷에서 액세스할 수 있습니다.  PaaS 기능을 사용 하 여 시스템을 구축 하는 경우 시스템 내의 구성 요소와이 통신만 허용 하도록 만들어진 네트워크 보안 규칙 간의 모든 필요한 통신 흐름을 식별 해야 합니다. 보안에 대 한 심층적인 심층 방어 방식을 방식의 일부로, 암호화, 인증 및 적절 한 액세스 제어 및 권한으로 PaaS 기능을 구성 해야 합니다.  

### <a name="hostname"></a>Hostname

PaaS 기능은 동일한 서비스의 여러 인스턴스가 동일한 공용 IP 주소에서 호스팅될 수 있도록 호스트 이름으로 고유 하 게 식별 됩니다. 리소스를 만들고 Microsoft 소유의 DNS 도메인 내에 있는 경우 고유한 호스트 이름을 지정 합니다. 응용 프로그램 수준 필터링 기능을 사용 하 여 보안 도구 내에서 공인 services에 대 한 특정 호스트 이름을 사용할 수 있습니다. 필요에 따라 사용자 지정 도메인을 사용 하 여 특정 서비스를 구성할 수도 있습니다.

|리소스 | 링크|
|---|---|
|Get-azurermenvironment 명령을 실행 하 여 Powershell을 통해 Azure 서비스에서 사용 되는 많은 공용 네임 스페이스를 가져올 수 있습니다. | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성 | App Services 및 다른 사용자는 사용자 지정 도메인을 가질 수 있습니다.[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>PaaS에 대 한 공용 IP

PaaS 기능에 대 한 공용 IP 주소는 서비스가 호스트 되거나 배포 되는 지역에 따라 할당 됩니다. Azure 가상 네트워크, PaaS, Express 경로 및 인터넷 연결을 포함 하는 네트워크 구분 및 분리을 위한 적절 한 네트워크 보안 규칙 및 라우팅 토폴로지를 빌드하기 위해 공용 IP 주소 할당 및 지역을 이해 하 고 있습니다. 필수. Azure는 각 Azure 지역에 할당 된 풀에서 IP 주소를 할당 합니다. Microsoft는 각 지역에서 사용 되는 주소를 다운로드할 수 있도록 하며, 이러한 주소는 정기적이 고 제어 된 방식으로 업데이트 됩니다. 또한 각 지역에서 사용할 수 있는 서비스는 새 서비스를 출시 하거나 서비스를 더욱 광범위 하 게 배포할 때 자주 변경 됩니다. 이러한 자료를 정기적으로 검토 하 고 필요에 따라 자동화를 활용 하 여 시스템을 유지 관리 해야 합니다. Microsoft 지원에 문의 하 여 각 지역에서 호스트 되는 일부 서비스에 대 한 특정 IP 주소를 얻을 수 있습니다.

|리소스 | 링크|
|---|---|
|Microsoft Azure 데이터 센터 IP 범위 | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|지역별 Azure 서비스 | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, 오스트레일리아-중부, 오스트레일리아-중부-2, 오스트레일리아-동부, 오스트레일리아-동남 & products = 모두](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>서비스 엔드포인트

Virtual Network 서비스 끝점은 특정 PaaS 기능을 사용 하기 위해 Virtual Network 내에서 서브넷에 대 한 고속 개인 수신 네트워크 연결을 제공 합니다. PaaS 기능을 완벽 하 게 네트워크로 구분 하 고 분리 하려면 필요한 가상 네트워크 에서만 연결을 허용 하도록 PaaS 기능을 구성 해야 합니다. 모든 PaaS 기능이 서비스 끝점과 기존 IP 주소 기반 규칙을 포함 하는 방화벽 규칙의 조합을 지 원하는 것은 아니므로 응용 프로그램 기능 및 관리에 필요한 통신의 흐름을 이해 해야 합니다. 이러한 보안 컨트롤의 구현은 서비스 가용성에 영향을 주지 않습니다.

|리소스 | 링크|
|---|---|
|서비스 엔드포인트 개요 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|자습서 |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>보안

IaaS 및 PaaS 기능에서 네트워크 구분 및 분리 컨트롤을 구현 하는 것은 기능을 보호 하 고와 통신할 시스템에서 제어 된 통신 경로를 구현 하 여 수행 됩니다. 기능.

Azure에서 솔루션을 설계 하 고 구축 하는 과정은 전체 Azure에서 네트워크 리소스를 이해 하 고 제어 하 고 모니터링 하기 위한 논리적 아키텍처를 만드는 프로세스입니다. 이 논리적 아키텍처는 Azure 플랫폼 내에서 정의 되는 소프트웨어로, 기존 네트워크 환경에서 구현 되는 실제 네트워크 토폴로지의 자리를 차지 합니다.

생성 되는 논리적 아키텍처는 사용 편의성에 필요한 기능을 제공 해야 하지만 보안과 무결성에 필요한 가시성과 제어를 제공 해야 합니다.

이러한 결과를 달성 하려면 필요한 네트워크 조각화 및 분리 도구를 구현 하는 것이 아니라 네트워크 토폴로지와 이러한 도구의 구현을 보호 하 고 적용 해야 합니다.

이 가이드에서 제공 하는 정보를 사용 하 여 허용 해야 하는 수신 트래픽의 원본 및 트래픽이 추가로 제어 되거나 제한 되는 방식을 식별할 수 있습니다.

### <a name="network-security-groups-nsgs"></a>NSG(네트워크 보안 그룹)

NSGs는 서브넷 또는 특정 네트워크 인터페이스에 허용 되는 인바운드 및 아웃 바운드 트래픽을 지정 하는 데 사용 됩니다. NSGs를 구성 하는 경우에는 특정 허용 문과 일치 하지 않는 모든 트래픽을 거부 하도록 기본 규칙을 구성 하는 데 필요한 트래픽을 허용 하도록 규칙을 구성 하는 허용 목록 접근 방식을 사용 해야 합니다. 필요한 모든 인바운드 및 아웃 바운드 트래픽이 적절 하 게 캡처 되도록 NSGs를 계획 하 고 구성할 때 주의 해야 합니다. 여기에는 Azure 가상 네트워크 및 온-프레미스 환경 내에서 utilised 모든 개인 IP 주소 범위를 식별 하 고 이해 하는 것과 Azure Load Balancer 및 PaaS 관리 요구 사항 등의 특정 Microsoft 서비스가 포함 됩니다. 네트워크 보안 그룹의 디자인 및 구현에 참여 하는 개인은 세분화 된 서비스 및 응용 프로그램별 보안 규칙을 만들기 위해 서비스 태그 및 응용 프로그램 보안 그룹을 사용 하는 것도 이해 해야 합니다.

|리소스 | 링크|
|---|---|
|네트워크 보안 개요 | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|네트워크 보안 그룹을 만들기, 변경 또는 삭제 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>PaaS 방화벽

PaaS 방화벽은 특정 PaaS 서비스에 적용할 수 있는 네트워크 액세스 제어 기능입니다. 특정 가상 네트워크에서 IP 주소 필터링 또는 필터링을 구성 하 여 특정 PaaS 인스턴스로 수신 트래픽을 제한할 수 있습니다. 방화벽을 포함 하는 PaaS 기능의 경우 응용 프로그램 요구 사항에 따라 필요한 수신 트래픽만 허용 하도록 네트워크 액세스 제어 정책을 구성 해야 합니다.  

|리소스 | 링크|
|---|---|
|Azure SQL Database 및 SQL Data Warehouse IP 방화벽 규칙 | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|저장소 네트워크 보안 | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS 인증 및 액세스 제어

PaaS 기능과 용도에 따라 네트워크 컨트롤을 사용 하 여 액세스를 제한 하는 것은 불가능 하거나 실용적이 지 않을 수 있습니다. PaaS 용 계층화 된 보안 모델의 일부로, Azure는 네트워크 트래픽이 허용 되는 경우에도 서비스에 대 한 액세스를 제한 하는 다양 한 인증 및 액세스 제어 메커니즘을 제공 합니다. PaaS 기능을 위한 일반적인 인증 메커니즘에는 Azure Active Directory, 응용 프로그램 수준 인증, 공유 키 또는 액세스 서명이 포함 됩니다. 사용자가 안전 하 게 식별 되 면 역할을 utilised 하 여 사용자가 수행할 수 있는 작업을 제어할 수 있습니다. 이러한 도구는 서비스에 대 한 액세스를 제한 하기 위한 대안 또는 utilised 수 있습니다.

|리소스 | 링크|
|---|---|
|SQL Database 및 SQL Data Warehouse에 대한 액세스 제어 및 권한 부여 | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Azure Storage Services에 대 한 권한 부여 | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy는 Azure 환경의 논리적 아키텍처에 대 한 무결성을 적용 하 고 유지 관리 하기 위한 핵심 구성 요소입니다. Azure 서비스를 통해 사용할 수 있는 다양 한 서비스 및 수신 네트워크 트래픽 경로를 고려 하 여 해당 환경 내에 존재 하는 리소스와 사용 가능한 네트워크 수신 지점의 리소스를 인식 하는 것이 중요 합니다. 무단 네트워크 수신 지점이 Azure 환경에서 생성 되지 않도록 하기 위해, 해당 리소스의 구성 및 배포할 수 있는 리소스의 유형을 제어 하기 위해 Azure Policy를 활용 해야 합니다. 실제 예에는 리소스를 공인로 제한 하 고 사용 하도록 승인 하 여 저장소에 대해 HTTPS 암호화를 적용 하 고 서브넷에 NSGs를 추가 해야 합니다.

|리소스 | 링크|
|---|---|
|Azure Policy 개요 | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|허용 되는 리소스 종류 샘플 정책 | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|HTTPS 저장소 계정 샘플 정책 확인|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|서브넷 샘플 정책에 대 한 NSG 강제 적용| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>다음 단계

Azure에서 게이트웨이 구성 요소를 사용 하 여 Azure 환경에서 다른 네트워크로의 트래픽 흐름을 관리 하는 방법에 대 한 자세한 내용은 [게이트웨이 송신 트래픽 관리 및 제어](gateway-egress-traffic.md) 문서를 참조 하세요.
