---
title: Azure의 공용 IP 주소
titlesuffix: Azure Virtual Network
description: Azure의 공용 IP 주소에 대해 알아보기
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: 121c22e3a25a95fa64f6f779ebc0827bb6c123c7
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108015954"
---
# <a name="public-ip-addresses"></a>공용 IP 주소

공용 IP 주소를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 공용 IP를 사용하면 Azure 리소스에서 인터넷 및 공용 Azure 서비스에 통신할 수 있습니다. 주소는 사용자가 할당 해제하지 않는 한 리소스에 전용됩니다. 공용 IP가 할당되지 않은 리소스는 아웃바운드로 통신할 수 있습니다. Azure는 사용 가능하며 리소스 전용이 아닌 IP 주소를 동적으로 할당합니다. Azure에서 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

Azure 리소스 관리자에서 [공용 IP](virtual-network-public-ip-address.md) 주소는 고유 속성을 가진 리소스입니다. 공용 IP 리소스를 연결할 수 있는 리소스는 다음과 같습니다.

* 가상 머신 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* VPN 게이트웨이
* 애플리케이션 게이트웨이
* Azure Firewall

## <a name="ip-address-version"></a>IP 주소 버전

공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 

## <a name="sku"></a>SKU

SKU 업그레이드에 관한 자세한 내용은 [공용 IP 업그레이드](../virtual-network/virtual-network-public-ip-address-upgrade.md)를 참조하세요.

공용 IP 주소는 다음 SKU 중 하나로 만들어집니다.

>[!IMPORTANT]
> 부하 분산 장치 및 공용 IP 리소스에 일치하는 SKU가 필요합니다. 기본 SKU 리소스와 표준 SKU 리소스를 함께 사용할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 리소스의 가상 머신 또는 가상 머신 확장 집합 리소스를 두 SKU에 동시에 연결할 수 없습니다.  새 디자인에서는 표준 SKU 리소스를 사용하도록 고려해야 합니다.  자세한 내용은 [표준 Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 검토하세요.

### <a name="standard"></a>Standard

표준 SKU 공용 IP:

- 항상 고정 할당 메서드를 사용합니다.
- 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.
- 기본적으로 보호되고 인바운드 트래픽에 닫혀 있습니다. [네트워크 보안 그룹](./network-security-groups-overview.md#network-security-groups)을 사용하여 인바운드 트래픽 나열을 허용합니다.
- 네트워크 인터페이스, 표준 퍼블릭 부하 분산 장치 또는 Application Gateway에 할당되었습니다. 표준 부하 분산 장치 관련한 자세한 내용은 [Azure 표준 Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 영역 중복(3개 영역 모두에서 보급됨), 영역(특정 사전 선택된 가용성 영역에서 보장) 또는 영역 없음(미리 선택된 특정 가용성 영역에 연결되지 않음)이 될 수 있습니다. 사용 가능한 영역에 대해 자세히 알아보려면 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [표준 부하 분산 장치 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요. **영역 중복 IP는 [3개의 가용성 영역이 있는 하위 지역](../availability-zones/az-region.md)에서만 만들 수 있습니다.** 영역이 라이브 상태가 되기 전에 만들어진 IP는 영역 중복이 아닙니다.
- [영역 간 부하 분산 장치](../load-balancer/cross-region-overview.md)(미리 보기 기능)의 애니캐스트 프런트 엔드 IP로 사용할 수 있습니다.
 
> [!NOTE]
> [네트워크 보안 그룹](./network-security-groups-overview.md#network-security-groups)을 만들어 연결하고 원하는 인바운드 트래픽을 명시적으로 허용해야 표준 SKU 리소스와 인바운드 통신할 수 있습니다.

> [!NOTE]
> [인스턴스 메타데이터 서비스 IMDS](../virtual-machines/windows/instance-metadata-service.md)를 사용하는 경우 기본 SKU를 사용하는 공용 IP만 사용할 수 있습니다. 표준 SKU는 지원되지 않습니다.

> [!NOTE]
> 표준 SKU 공용 IP를 사용하는 경우 리소스 블레이드에서 진단 설정이 표시되지 않습니다. 표준 공용 IP 리소스를 위한 로깅을 사용하도록 설정하려면 Azure Monitor 블레이드에서 진단 설정으로 이동하고 IP 주소 리소스를 선택합니다.

### <a name="basic"></a>Basic

SKU 도입 전에 생성된 모든 공용 IP 주소는 기본 SKU 공용 IP 주소입니다. 

SKU를 도입하면서 공용 IP로 선택하려는 SKU를 지정합니다. 

기본 SKU 주소:

- 고정 또는 동적 할당 방법으로 할당됩니다.
- 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.
- 기본적으로 열려 있습니다.  네트워크 보안 그룹을 사용하는 것이 좋지만 인바운드 또는 아웃바운드 트래픽을 제한하는 것은 선택 사항입니다.
- 다음과 같이 공용 IP를 할당할 수 있는 모든 Azure 리소스에 할당되었습니다.
    * 네트워크 인터페이스
    * VPN Gateway
    * Application Gateway
    * 공용 부하 분산 장치
- 가용성 영역 시나리오를 지원하지 않습니다. 가용성 영역 시나리오를 위한 표준 SKU 공용 IP를 사용합니다. 사용 가능한 영역에 대해 자세히 알아보려면 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [표준 부하 분산 장치 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

## <a name="allocation-method"></a>할당 방법

기본 IP 및 표준 공용 IP는 **정적** 할당을 지원합니다.  리소스는 생성될 때 IP 주소에 할당됩니다. 이 IP 주소는 리소스가 삭제되면 해제됩니다.

기본 SKU 공용 IP는 **동적** 할당을 지원합니다. 동적이 기본 할당 방법입니다. 동적을 선택하는 경우 IP 주소는 생성 시 리소스에 제공되지 **않습니다**.

공용 IP 리소스를 아래에 연결할 때 IP가 할당됩니다.

* 가상 머신 
* 첫 번째 가상 머신은 부하 분산 장치의 백 엔드 풀과 연결됩니다.

리소스를 중지(또는 삭제)하면 IP 주소가 해제됩니다.  

예를 들어 **리소스 A** 라는 리소스에서 공용 IP 리소스를 해제합니다. **리소스 A** 는 공용 IP 리소스를 다시 할당하는 경우, 시작할 때 다른 IP를 수신합니다.

할당 방법이 **정적** 에서 **동적** 으로 변경되면 IP 주소가 해제됩니다. 연결된 리소스의 IP 주소를 동일하게 유지하려면 할당 방법을 명시적으로 **static** 으로 설정합니다. 고정 IP 주소가 즉시 할당됩니다.

> [!NOTE]
> 할당 방법을 **고정** 으로 설정한 경우에도 공용 IP 주소 리소스에 할당된 실제 IP 주소를 지정할 수 없습니다. Azure는 리소스가 생성된 Azure 위치에서 사용 가능한 IP 주소 풀의 IP 주소를 할당됩니다.
>

정적 공용 IP 주소는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

* Azure 리소스와 통신하도록 방화벽 규칙을 업데이트해야 하는 경우
* DNS 이름을 확인, IP 주소를 변경하려면 A 레코드를 업데이트해야 하는 경우
* Azure 리소스가 IP 기반 보안 모델을 사용하는 다른 앱 또는 서비스와 통신하는 경우
* IP 주소에 연결된 TLS/SSL 인증서를 사용하는 경우.

> [!NOTE]
> Azure는 각 Azure 클라우드 지역의 고유한 범위에서 공용 IP 주소를 할당합니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.
>

## <a name="dns-hostname-resolution"></a>DNS 호스트 이름 확인

공용 IP 리소스를 위한 DNS 도메인 이름 레이블을 지정하는 옵션을 선택합니다. 

이렇게 선택하면 Azure 관리형 DNS에 공용 IP를 위한 **domainnamelabel**.**location**.cloudapp.azure.com의 매핑이 생성됩니다. 

예를 들어 다음을 사용하여 공용 IP를 만듭니다.

* **domainnamelabel** 로서의 **contoso**
* **미국 서부** Azure **위치**

FQDN(정규화된 도메인 이름)인 **contoso.westus.cloudapp.azure.com** 은 리소스의 공용 IP로 확인됩니다.

> [!IMPORTANT]
> 생성된 각 도메인 이름은 Azure 위치 내에서 고유해야 합니다.  
>

## <a name="dns-recommendations"></a>DNS 권장 사항

하위 지역 이동이 필요한 경우 공용 IP의 FQDN을 마이그레이션할 수 없습니다. FQDN을 사용하여 공용 IP를 가리키는 사용자 지정 CNAME 레코드를 만듭니다. 

다른 공용 IP로 이동해야 하는 경우 FQDN을 업데이트하는 대신 CNAME 레코드를 업데이트합니다.

DNS 레코드를 위한 [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) 또는 외부 DNS 공급자를 사용할 수 있습니다. 

## <a name="virtual-machines"></a>가상 머신

공용 IP 주소를 **네트워크 인터페이스** 에 할당하여 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 

공용 IP에 **동적** 또는 **정적** 을 선택합니다. [네트워크 인터페이스에 IP 주소를 할당](virtual-network-network-interface-addresses.md)하는 방법에 대해 자세히 알아봅니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="internet-facing-load-balancers"></a>인터넷 연결 부하 분산 장치

하나의 [SKU](#sku)의 공용 IP를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)와 연결할 수 있습니다. 이 공용 IP는 부하가 분산된 IP로 사용됩니다. 

부하 분산 장치 프런트 엔드에 동적 또는 고정 공용 IP 주소를 할당할 수 있습니다. 부하 분산 장치 프런트 엔드에 여러 공용 IP를 할당할 수 있습니다. 이 구성을 통해 TLS 기반 웹 사이트를 사용하는 다중 테넌트 환경과 같은 [다중 VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 시나리오를 사용할 수 있습니다. 

Azure Load Balancer SKU에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="vpn-gateways"></a>VPN 게이트웨이

[Azure VPN Gateway ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)가 Azure 가상 네트워크를 다음에 연결합니다.

* Azure 가상 네트워크
* 온-프레미스 네트워크. 

공용 IP 주소는 원격 네트워크와의 통신을 사용하기 위해 VPN Gateway에 할당됩니다. 

* VPNGw 1-5 SKU 프런트 엔드 구성에 **동적** 기본 공용 IP를 할당합니다.
* VPNGwAZ 1-5 SKU 프런트 엔드 구성에 **정적** 표준 공용 IP를 할당합니다.

## <a name="application-gateways"></a>애플리케이션 게이트웨이

공용 IP 주소를 게이트웨이의 [프런트 엔드](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)구성에 할당하여 Azure **Application Gateway** 와 연결할 수 있습니다. 

* 애플리케이션 게이트웨이 V1 프런트 엔드 구성에 **동적** 기본 공용 IP를 할당합니다. 
* V2 프런트 엔드 구성에 **정적** 표준 공용 IP를 할당합니다.

## <a name="azure-firewall"></a>Azure Firewall

[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 사용하면 구독 및 가상 네트워크 전반에 걸쳐 애플리케이션 및 네트워크 연결 정책을 만들고, 적용하고 기록할 수 있습니다.

**정적** 표준 공용 IP만 방화벽과 연결할 수 있습니다. 이렇게 하면 외부 방화벽이 사용자의 가상 네트워크에서 시작되는 트래픽을 식별할 수 있습니다. 


## <a name="at-a-glance"></a>요약

다음 표는 공용 IP를 최상위 리소스와 연결할 수 있는 속성 및 가능한 할당 메서드를 보여줍니다.

| 최상위 리소스 | IP 주소 연결 | 동적 | 정적 |
| --- | --- | --- | --- |
| 가상 머신 |네트워크 인터페이스 |예 |예 |
| 인터넷 연결 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| VPN 게이트웨이 |게이트웨이 IP 구성 |Yes |예(VPNGwAZ에만 해당) |
| 프런트 엔드 |프런트 엔드 구성 |예(V1에만 해당) |예(V2에만 해당) |
| Azure Firewall | 프런트 엔드 구성 | 아니요 | 예|

## <a name="limits"></a>제한

IP 주소 지정에 적용되는 제한은 Azure에서 [네트워킹 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)의 전체 집합에 나열되어 있습니다. 

제한은 지역별, 구독별로 적용됩니다. 비즈니스에 따라 최대한도까지 기본 제한을 증가시키려면 [고객 지원팀에 문의하세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="pricing"></a>가격 책정

공용 IP 주소에는 명목 요금이 부과될 수 있습니다. Azure의 IP 주소 가격 책정에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure의 개인 IP 주소](private-ip-addresses.md)에 관한 자세한 정보
* [Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-portal.md)
