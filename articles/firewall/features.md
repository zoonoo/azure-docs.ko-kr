---
title: Azure Firewall 기능
description: Azure 방화벽 기능에 대해 알아보기
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: victorh
ms.openlocfilehash: 7429be4430b2b520fb2a66b6b2c0dd138af8e501
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850594"
---
# <a name="azure-firewall-features"></a>Azure Firewall 기능

[Azure 방화벽](overview.md) 은 azure Virtual Network 리소스를 보호 하는 관리 되는 클라우드 기반 네트워크 보안 서비스입니다.

![방화벽 개요](media/overview/firewall-threat.png)

Azure 방화벽에는 다음과 같은 기능이 포함 되어 있습니다.

- [기본 제공 되는 고가용성](#built-in-high-availability)
- [가용성 영역](#availability-zones)
- [무제한 클라우드 확장성](#unrestricted-cloud-scalability)
- [애플리케이션 FQDN 필터링 규칙](#application-fqdn-filtering-rules)
- [네트워크 트래픽 필터링 규칙](#network-traffic-filtering-rules)
- [FQDN 태그](#fqdn-tags)
- [서비스 태그](#service-tags)
- [위협 인텔리전스](#threat-intelligence)
- [아웃바운드 SNAT 지원](#outbound-snat-support)
- [인바운드 DNAT 지원](#inbound-dnat-support)
- [여러 공용 IP 주소](#multiple-public-ip-addresses)
- [Azure Monitor 로깅](#azure-monitor-logging)
- [강제 터널링](#forced-tunneling)
- [인증서](#certifications)

## <a name="built-in-high-availability"></a>기본 제공되는 고가용성

고가용성이 내장되어 있어서 부하 분산 장치가 추가로 필요하지 않으며 아무것도 구성할 필요가 없습니다.

## <a name="availability-zones"></a>가용성 영역

Azure Firewall은 가용성을 높이기 위해 여러 가용 영역에 걸쳐 배포하는 동안 구성할 수 있습니다. 가용성 영역을 사용하면 가용성이 작동 시간 99.99%로 증가합니다. 자세한 내용은 Azure Firewall [SLA(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/)를 참조하세요. 99.99% 작동 시간 SLA는 둘 이상의 가용성 영역을 선택하는 경우 제공됩니다.

서비스 표준 99.95% SLA를 사용하여 근접성을 이유로 특정 영역에 Azure Firewall을 연결할 수도 있습니다.

가용성 영역에 배포되는 방화벽에 대한 추가 비용은 없습니다. 단, 가용 영역과 관련된 인바운드 및 아웃바운드 데이터 전송에는 추가 비용이 있습니다. 자세한 내용은 [대역폭 가격 세부 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

Azure Firewall 가용성 영역은 가용 영역을 지원하는 지역에서 사용할 수 있습니다. 자세한 내용은 [Azure에서 가용성 영역을 지원하는 지역](../availability-zones/az-region.md)을 참조하세요.

> [!NOTE]
> 가용성 영역은 배포 중에만 구성할 수 있습니다. 가용성 영역을 포함하도록 기존 방화벽을 구성할 수는 없습니다.

가용성 영역에 대한 자세한 내용은 [Azure의 지역 및 가용성 영역](../availability-zones/az-overview.md)을 참조하세요.

## <a name="unrestricted-cloud-scalability"></a>무제한 클라우드 확장성

Azure Firewall은 변화하는 트래픽 흐름을 수용하기 위해 필요한 만큼 확장이 가능하기 때문에 최대 트래픽에 대한 예산을 마련할 필요가 없습니다.

## <a name="application-fqdn-filtering-rules"></a>애플리케이션 FQDN 필터링 규칙

와일드 카드를 포함 하 여 지정 된 FQDN (정규화 된 도메인 이름) 목록으로 아웃 바운드 HTTP/S 트래픽 또는 Azure SQL 트래픽을 제한할 수 있습니다. 이 기능에는 TLS 종료가 필요하지 않습니다.

## <a name="network-traffic-filtering-rules"></a>네트워크 트래픽 필터링 규칙

원본 및 대상 IP 주소, 포트 및 프로토콜별로 네트워크 필터링 규칙을 중앙에서 만들거나 *허용*하거나 *거부*할 수 있습니다. Azure Firewall은 상태를 완전히 저장하기 때문에 다양한 유형의 연결에서 올바른 패킷을 구별할 수 있습니다. 여러 구독 및 가상 네트워크 전반에 규칙이 적용되고 기록됩니다.

## <a name="fqdn-tags"></a>FQDN 태그

[FQDN 태그](fqdn-tags.md)를 활용하면 방화벽을 통해 잘 알려진 Azure 서비스 네트워크 트래픽을 쉽게 허용할 수 있습니다. 방화벽을 통해 Windows 업데이트 네트워크 트래픽을 허용하려는 경우를 예로 들어 보겠습니다. 이렇게 하려면 애플리케이션 규칙을 만들고 Windows 업데이트 태그를 포함합니다. 그러면 Windows 업데이트의 네트워크 트래픽이 방화벽을 통해 이동할 수 있습니다.

## <a name="service-tags"></a>서비스 태그

[서비스 태그](service-tags.md)는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 사용자 고유의 서비스 태그를 직접 만들 수 없거나 태그 내에 포함할 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

## <a name="threat-intelligence"></a>위협 인텔리전스

방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 [위협 인텔리전스](threat-intel.md) 기반 필터링을 사용하도록 설정할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

## <a name="outbound-snat-support"></a>아웃바운드 SNAT 지원

모든 아웃바운드 가상 네트워크 트래픽 IP 주소는 Azure Firewall 공용 IP로 변환됩니다(원본 네트워크 주소 변환). 가상 네트워크에서 발생하는 트래픽을 식별하여 원격 인터넷 대상으로 허용할 수 있습니다. [IANA RFC 1918](https://tools.ietf.org/html/rfc1918)에 따라 대상 IP가 개인 IP 범위인 경우 Azure Firewall은 SNAT를 지원하지 않습니다. 

조직에서 개인 네트워크에 대해 공용 IP 주소 범위를 사용하면 Azure Firewall은 AzureFirewallSubnet의 방화벽 개인 IP 주소 중 하나에 트래픽을 SNAT합니다. 공용 IP 주소 범위를 SNAT하지 **않도록** Azure 방화벽을 구성할 수 있습니다. 자세한 내용은 [Azure Firewall SNAT 개인 IP 주소 범위](snat-private-range.md)를 참조하세요.

## <a name="inbound-dnat-support"></a>인바운드 DNAT 지원

방화벽 공용 IP 주소로의 인바운드 인터넷 네트워크 트래픽이 변환되고(Destination Network Address Translation), 가상 네트워크의 개인 IP 주소로 필터링됩니다.

## <a name="multiple-public-ip-addresses"></a>여러 공용 IP 주소

방화벽에 [여러 공용 IP 주소](deploy-multi-public-ip-powershell.md)(최대 250개)를 연결할 수 있습니다.

따라서 다음과 같은 시나리오가 가능합니다.

- **DNAT** - 여러 표준 포트 인스턴스를 백 엔드 서버로 변환할 수 있습니다. 예를 들어 공용 IP 주소가 2개인 경우 두 IP 주소 모두에 대해 TCP 포트 3389(RDP)를 변환할 수 있습니다.
- **SNAT** - 아웃바운드 SNAT 연결에 추가 포트를 사용할 수 있기 때문에 SNAT 포트가 고갈될 가능성이 줄어듭니다. 이때 Azure Firewall은 연결에 사용할 원본 공용 IP 주소를 임의로 선택합니다. 네트워크에 다운스트림 필터링이 있는 경우, 방화벽과 연결된 모든 공용 IP 주소를 허용해야 합니다. [공용 IP 주소 접두사](../virtual-network/public-ip-address-prefix.md)를 사용하여 이 구성을 단순화하세요.

## <a name="azure-monitor-logging"></a>Azure Monitor 로깅

모든 이벤트가 Azure Monitor와 통합되기 때문에 스토리지 계정에 로그를 보관하고 Event Hub에 이벤트를 스트리밍하거나 Azure Monitor 로그에 보낼 수 있습니다. Azure Monitor 로그 예제는 [Azure 방화벽에 대 한 Azure Monitor 로그](log-analytics-samples.md)를 참조 하세요.

자세한 내용은 [자습서: Azure Firewall 로그 및 메트릭 모니터링](tutorial-diagnostics.md)을 참조하세요. 

Azure 방화벽 통합 문서는 Azure 방화벽 데이터 분석을 위한 유연한 캔버스를 제공 합니다. 이를 사용 하 여 Azure Portal 내에서 풍부한 시각적 보고서를 만들 수 있습니다. 자세한 내용은 [Azure 방화벽 통합 문서를 사용 하 여 로그 모니터링](firewall-workbook.md)을 참조 하세요.

## <a name="forced-tunneling"></a>강제 터널링

모든 인터넷 바인딩된 트래픽을 인터넷으로 직접 이동하는 대신 지정된 다음 홉으로 라우팅하도록 Azure Firewall을 구성할 수 있습니다. 예를 들어 온-프레미스 에지 방화벽이나 기타 NVA(네트워크 가상 어플라이언스)를 통해 네트워크 트래픽을 인터넷에 전달하기 전에 처리할 수 있습니다. 자세한 내용은 [Azure Firewall 강제 터널링](forced-tunneling.md)을 참조하세요.

## <a name="certifications"></a>인증서

Azure Firewall은 PCI(결제 카드 산업), SOC(서비스 조직 컨트롤), ISO(국제 표준화 기구) 및 ICSA Labs 규격을 준수합니다. 자세한 내용은 [Azure Firewall 규정 준수 인증](compliance-certifications.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Firewall 규칙 처리 논리](rule-processing.md)