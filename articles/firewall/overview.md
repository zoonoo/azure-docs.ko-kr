---
title: Azure Firewall이란?
description: Azure Firewall 기능에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 5e8048dc6b49a0f6c9a465e82a7278e491351034
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574133"
---
# <a name="what-is-azure-firewall"></a>Azure Firewall이란?

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

![방화벽 개요](media/overview/firewall-overview.png)



구독 및 가상 네트워크 전반에 걸쳐 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다. Azure Firewall은 가상 네트워크 리소스에 정적 공용 IP 주소를 사용하기 때문에 외부 방화벽이 사용자의 가상 네트워크에서 시작된 트래픽을 식별할 수 있습니다.  이 서비스는 로깅 및 분석을 위해 Azure Monitor와 완전히 통합됩니다.

## <a name="features"></a>기능

Azure Firewall 공개 미리 보기에는 다음과 같은 기능이 제공됩니다.

### <a name="built-in-high-availability"></a>기본 제공되는 고가용성
고가용성이 내장되어 있어서 부하 분산 장치가 추가로 필요하지 않으며 아무것도 구성할 필요가 없습니다.

### <a name="unrestricted-cloud-scalability"></a>무제한 클라우드 확장성 
Azure Firewall은 변화하는 트래픽 흐름을 수용하기 위해 필요한 만큼 확장이 가능하기 때문에 최대 트래픽에 대한 예산을 마련할 필요가 없습니다.

### <a name="fqdn-filtering"></a>FQDN 필터링 
아웃바운드 HTTP/S 트래픽을 와일드 카드를 포함한 FQDN(정규화된 도메인 이름) 목록으로 제한할 수 있습니다. 이 기능에는 SSL 종료가 필요하지 않습니다.

### <a name="network-traffic-filtering-rules"></a>네트워크 트래픽 필터링 규칙

원본 및 대상 IP 주소, 포트 및 프로토콜별로 네트워크 필터링 규칙을 중앙에서 만들거나 *허용*하거나 *거부*할 수 있습니다. Azure Firewall은 상태를 완전히 저장하기 때문에 다양한 유형의 연결에서 올바른 패킷을 구별할 수 있습니다. 여러 구독 및 가상 네트워크 전반에 규칙이 적용되고 기록됩니다.

### <a name="outbound-snat-support"></a>아웃바운드 SNAT 지원

모든 아웃바운드 가상 네트워크 트래픽 IP 주소는 Azure Firewall 공용 IP로 변환됩니다(원본 네트워크 주소 변환). 가상 네트워크에서 발생하는 트래픽을 식별하여 원격 인터넷 대상으로 허용할 수 있습니다.

### <a name="azure-monitor-logging"></a>Azure Monitor 로깅

모든 이벤트가 Azure Monitor와 통합되기 때문에 저장소 계정에 로그를 보관하고 Event Hub에 이벤트를 스트리밍하거나 Log Analytics에 보낼 수 있습니다.

## <a name="known-issues"></a>알려진 문제

Azure Firewall 공개 미리 보기에는 다음과 같이 알려진 문제가 있습니다.


|문제  |설명  |해결 방법  |
|---------|---------|---------|
|NSG와의 상호 운용성     |방화벽 서브넷에 NSG(네트워크 보안 그룹)가 적용되면 NSG가 아웃바운드 인터넷 액세스를 허용하도록 구성되어 있어도 아웃바운드 인터넷 연결을 차단할 수 있습니다. 아웃바운드 인터넷 연결이 VirtualNetwork에서 오는 것으로 표시되며 대상은 인터넷입니다. NSG는 VirtualNetwork에서 VirtualNetwork로 연결을 기본적으로 *허용*하지만 대상이 인터넷일 때는 그렇지 않습니다.|이 문제를 완화하려면 방화벽 서브넷에 적용된 NSG에 다음 인바운드 규칙을 추가합니다.<br><br>원본: VirtualNetwork Source ports: Any <br><br>대상: Any Destination Ports: Any <br><br>프로토콜: All Access: Allow|
|ASC(Azure Security Center) JIT(Just-in-Time) 기능과의 충돌|JIT를 사용하여 가상 머신에 액세스하는 경우, Azure Firewall을 기본 게이트웨이로 가리키는 사용자 정의 경로가 포함된 서브넷에 가상 머신이 있으면 ASC JIT가 작동하지 않습니다. 들어오는 패킷은 가상 머신 공용 IP(JIT가 액세스를 여는)를 통하지만 반환 경로는 방화벽을 경유하는 비대칭 라우팅 때문에 발생합니다. 이렇게 하면 방화벽에 세션이 설정되어 있지 않기 때문에 패킷이 손실됩니다.|이 문제를 해결하려면 JIT 가상 머신을 방화벽에 대한 사용자 정의 경로가 없는 별도의 서브넷에 배치합니다.|
|글로벌 피어링을 통한 허브 및 스포크가 작동하지 않음|글로벌 VNet 피어링을 통해 허브에 연결되어 있고 하나의 Azure 지역에 허브와 방화벽이 배포되어 있고 또 다른 Azure 지역에 스포크가 있는 허브 및 스포크 모델은 지원되지 않습니다.|자세한 내용은 [가상 네트워크 피어링 만들기, 변경 또는 삭제](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)를 참조하세요.|
TCP/UDP 프로토콜이 아닌 프로토콜(예: ICMP)에 대한 네트워크 필터링 규칙은 인터넷 바운드 트래픽에 작동하지 않습니다.|TCP/UDP 프로토콜이 아닌 프로토콜에 대한 네트워크 필터링 규칙은 공용 IP 주소에 대한 SNAT에 작동하지 않습니다. TCP/UDP 프로토콜이 아닌 프로토콜은 스포크 서브넷과 VNet 간에 지원됩니다.|Azure Firewall은 표준 Load Balancer를 사용하기 때문에 [현재 IP 프로토콜을 위한 SNAT를 지원하지 않습니다](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). 향후 릴리스에서 이 시나리오를 지원할 수 있는 옵션을 모색하고 있습니다.



## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)
- [템플릿을 사용하여 Azure Firewall 배포](deploy-template.md)
- [Azure Firewall 테스트 환경 만들기](scripts/sample-create-firewall-test.md)

