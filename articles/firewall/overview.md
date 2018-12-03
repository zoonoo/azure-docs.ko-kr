---
title: Azure Firewall이란?
description: Azure Firewall 기능에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 11/28/2018
ms.author: victorh
ms.openlocfilehash: b90496b0ccc6c8243c2d1b3ead1e7c4faa4801ec
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582051"
---
# <a name="what-is-azure-firewall"></a>Azure Firewall이란?

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 

![방화벽 개요](media/overview/firewall-overview.png)

구독 및 가상 네트워크 전반에 걸쳐 응용 프로그램 및 네트워크 연결 정책을 중앙에서 만들고, 적용하고 기록할 수 있습니다. Azure Firewall은 가상 네트워크 리소스에 정적 공용 IP 주소를 사용하기 때문에 외부 방화벽이 사용자의 가상 네트워크에서 시작된 트래픽을 식별할 수 있습니다.  이 서비스는 로깅 및 분석을 위해 Azure Monitor와 완전히 통합됩니다.

## <a name="features"></a>기능

Azure Firewall에서는 다음과 같은 기능이 제공됩니다.

### <a name="built-in-high-availability"></a>기본 제공되는 고가용성
고가용성이 내장되어 있어서 부하 분산 장치가 추가로 필요하지 않으며 아무것도 구성할 필요가 없습니다.

### <a name="unrestricted-cloud-scalability"></a>무제한 클라우드 확장성 
Azure Firewall은 변화하는 트래픽 흐름을 수용하기 위해 필요한 만큼 확장이 가능하기 때문에 최대 트래픽에 대한 예산을 마련할 필요가 없습니다.

### <a name="application-fqdn-filtering-rules"></a>응용 프로그램 FQDN 필터링 규칙

아웃바운드 HTTP/S 트래픽을 와일드 카드를 포함한 FQDN(정규화된 도메인 이름) 목록으로 제한할 수 있습니다. 이 기능에는 SSL 종료가 필요하지 않습니다.

### <a name="network-traffic-filtering-rules"></a>네트워크 트래픽 필터링 규칙

원본 및 대상 IP 주소, 포트 및 프로토콜별로 네트워크 필터링 규칙을 중앙에서 만들거나 *허용*하거나 *거부*할 수 있습니다. Azure Firewall은 상태를 완전히 저장하기 때문에 다양한 유형의 연결에서 올바른 패킷을 구별할 수 있습니다. 여러 구독 및 가상 네트워크 전반에 규칙이 적용되고 기록됩니다.

### <a name="fqdn-tags"></a>FQDN 태그

FQDN 태그를 활용하면 방화벽을 통해 잘 알려진 Azure 서비스 네트워크 트래픽을 쉽게 허용할 수 있습니다. 방화벽을 통해 Windows 업데이트 네트워크 트래픽을 허용하려는 경우를 예로 들어 보겠습니다. 이렇게 하려면 응용 프로그램 규칙을 만들고 Windows 업데이트 태그를 포함합니다. 그러면 Windows 업데이트의 네트워크 트래픽이 방화벽을 통해 이동할 수 있습니다.

### <a name="outbound-snat-support"></a>아웃바운드 SNAT 지원

모든 아웃바운드 가상 네트워크 트래픽 IP 주소는 Azure Firewall 공용 IP로 변환됩니다(원본 네트워크 주소 변환). 가상 네트워크에서 발생하는 트래픽을 식별하여 원격 인터넷 대상으로 허용할 수 있습니다.

### <a name="inbound-dnat-support"></a>인바운드 DNAT 지원

방화벽 공용 IP 주소로 전송되는 인바운드 네트워크 트래픽은 변환(Destination Network Address Translation)된 다음 가상 네트워크의 개인 IP 주소로 필터링됩니다. 

### <a name="azure-monitor-logging"></a>Azure Monitor 로깅

모든 이벤트가 Azure Monitor와 통합되기 때문에 저장소 계정에 로그를 보관하고 Event Hub에 이벤트를 스트리밍하거나 Log Analytics에 보낼 수 있습니다.

## <a name="known-issues"></a>알려진 문제

Azure Firewall의 알려진 문제는 다음과 같습니다.


|문제  |설명  |해결 방법  |
|---------|---------|---------|
|ASC(Azure Security Center) JIT(Just-in-Time) 기능과의 충돌|JIT를 사용하여 가상 머신에 액세스하는 경우, Azure Firewall을 기본 게이트웨이로 가리키는 사용자 정의 경로가 포함된 서브넷에 가상 머신이 있으면 ASC JIT가 작동하지 않습니다. 들어오는 패킷은 가상 머신 공용 IP(JIT가 액세스를 여는)를 통하지만 반환 경로는 방화벽을 경유하는 비대칭 라우팅 때문에 발생합니다. 이렇게 하면 방화벽에 세션이 설정되어 있지 않기 때문에 패킷이 손실됩니다.|이 문제를 해결하려면 JIT 가상 머신을 방화벽에 대한 사용자 정의 경로가 없는 별도의 서브넷에 배치합니다.|
|글로벌 피어링을 통한 허브 및 스포크가 지원되지 않음|한 Azure 지역에 허브와 방화벽이 배포되어 있고 또 다른 Azure 지역에 스포크가 있는 허브 및 스포크 모델을 사용합니다. 글로벌 VNet 피어링을 통해 허브에 연결할 수 없음|의도적인 작동입니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md#azure-firewall-limits)을 참조하세요.|
TCP/UDP 프로토콜이 아닌 프로토콜(예: ICMP)에 대한 네트워크 필터링 규칙은 인터넷 바운드 트래픽에 작동하지 않습니다.|TCP/UDP 프로토콜이 아닌 프로토콜에 대한 네트워크 필터링 규칙은 공용 IP 주소에 대한 SNAT에 작동하지 않습니다. TCP/UDP 프로토콜이 아닌 프로토콜은 스포크 서브넷과 VNet 간에 지원됩니다.|Azure Firewall은 표준 Load Balancer를 사용하기 때문에 [현재 IP 프로토콜을 위한 SNAT를 지원하지 않습니다](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). 향후 릴리스에서 이 시나리오를 지원할 수 있는 옵션을 모색하고 있습니다.|
|ICMP에 대한 PowerShell 및 CLI 지원 누락|Azure PowerShell 및 CLI는 네트워크 규칙에 유효한 프로토콜로 ICMP를 지원하지 않습니다.|여전히 포털 및 REST API를 통해 ICMP를 프로토콜로 사용할 수 있습니다. 조만간 PowerShell 및 CLI에 ICMP를 추가하기 위한 작업이 진행 중입니다.|
|FQDN 태그는 프로토콜: 설정할 포트가 필요|FQDN 태그를 사용하는 응용 프로그램 규칙은 포트: 프로토콜 정의가 필요합니다.|**https**를 포트: 프로토콜 값으로 사용할 수 있습니다. FQDN 태그를 사용할 때 이 필드 옵션이 작동하도록 하기 위한 작업이 진행 중입니다.|
|방화벽을 다른 리소스 그룹 또는 구독으로 이동하는 기능은 지원되지 않습니다.|방화벽을 다른 리소스 그룹 또는 구독으로 이동하는 기능은 지원되지 않습니다.|이 기능은 로드맵에 있습니다. 방화벽을 다른 리소스 그룹 또는 구독으로 이동하려면 현재 인스턴스를 삭제하고 새 리소스 그룹 또는 구독에서 다시 만들어야 합니다.|

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](tutorial-firewall-deploy-portal.md)
- [템플릿을 사용하여 Azure Firewall 배포](deploy-template.md)
- [Azure Firewall 테스트 환경 만들기](scripts/sample-create-firewall-test.md)

