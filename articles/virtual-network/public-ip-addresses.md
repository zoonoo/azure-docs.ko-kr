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
ms.openlocfilehash: 9f3d95d7ae725dba700b0a060ba74552d6b83ad5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84172248"
---
# <a name="public-ip-addresses"></a>공용 IP 주소

공용 IP 주소를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 공용 IP 주소를 사용 하면 Azure 리소스가 인터넷 및 공용 Azure 서비스와 통신할 수 있습니다. 주소는 사용자가 할당을 해제 하기 전까지 리소스 전용입니다. 공용 IP가 할당 되지 않은 리소스는 아웃 바운드 통신할 수 있습니다. Azure는 리소스 전용으로 사용 가능한 IP 주소를 동적으로 할당 합니다. Azure에서 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

Azure 리소스 관리자에서 [공용 IP](virtual-network-public-ip-address.md) 주소는 고유 속성을 가진 리소스입니다. 공용 IP 주소 리소스를 연결할 수 있는 리소스는 다음과 같습니다.

* 가상 머신 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* VPN 게이트웨이
* 애플리케이션 게이트웨이
* Azure Firewall

## <a name="ip-address-version"></a>IP 주소 버전

공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 

## <a name="sku"></a>SKU

공용 IP 주소는 다음 SKU 중 하나로 만들어집니다.

>[!IMPORTANT]
> 부하 분산 장치 및 공용 IP 리소스에 대해 일치 하는 Sku가 필요 합니다. 기본 SKU 리소스와 표준 SKU 리소스를 함께 사용할 수 없습니다. 독립 실행형 가상 머신, 가용성 집합 리소스의 가상 머신 또는 가상 머신 확장 집합 리소스를 두 SKU에 동시에 연결할 수 없습니다.  새 디자인에서는 표준 SKU 리소스를 사용하도록 고려해야 합니다.  자세한 내용은 [표준 Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 검토하세요.

### <a name="standard"></a>표준

표준 SKU 공용 IP 주소:

- 항상 고정 할당 메서드를 사용합니다.
- 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.
- 기본적으로 안전 하 게 보호 하 고 인바운드 트래픽에 대해 닫힘. [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용 하 여 인바운드 트래픽 나열을 허용 합니다.
- 네트워크 인터페이스, 표준 공용 부하 분산 장치 또는 응용 프로그램 게이트웨이에 할당 됩니다. 표준 부하 분산 장치에 대 한 자세한 내용은 [Azure 표준 Load Balancer](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조 하세요.
- 영역 중복 또는 영역 일 수 있습니다 (특정 가용성 영역에서 영역 및 보증을 만들 수 있음). 사용 가능한 영역에 대해 자세히 알아보려면 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [표준 부하 분산 장치 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.
 
> [!NOTE]
> [네트워크 보안 그룹](security-overview.md#network-security-groups)을 만들어 연결하고 원하는 인바운드 트래픽을 명시적으로 허용해야 표준 SKU 리소스와 인바운드 통신할 수 있습니다.

> [!NOTE]
> [인스턴스 메타 데이터 서비스 IMDS](../virtual-machines/windows/instance-metadata-service.md)를 사용 하는 경우 기본 SKU를 사용 하는 공용 IP 주소만 사용할 수 있습니다. 표준 SKU는 지원 되지 않습니다.

### <a name="basic"></a>Basic

SKU 도입 전에 생성된 모든 공용 IP 주소는 기본 SKU 공용 IP 주소입니다. 

Sku를 소개 하 고 공용 IP 주소를 원하는 SKU를 지정 합니다. 

기본 SKU 주소:

- 고정 또는 동적 할당 방법으로 할당됩니다.
- 조정 가능한 인바운드 발생 흐름 유휴 시간 제한은 4-30분(기본값은 4분)으로, 고정 아웃바운드 발생 흐름 유휴 시간 제한은 4분으로 정합니다.
- 기본적으로 열려 있습니다.  네트워크 보안 그룹을 사용하는 것이 좋지만 인바운드 또는 아웃바운드 트래픽을 제한하는 것은 선택 사항입니다.
- 다음과 같이 공용 IP 주소를 할당할 수 있는 모든 Azure 리소스에 할당 됩니다.
    * 네트워크 인터페이스
    * VPN Gateway
    * Application Gateway
    * 공용 부하 분산 장치
- 가용성 영역 시나리오를 지원 하지 않습니다. 가용성 영역 시나리오에는 표준 SKU 공용 IP를 사용 합니다. 사용 가능한 영역에 대해 자세히 알아보려면 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [표준 부하 분산 장치 및 가용성 영역](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조하세요.

## <a name="allocation-method"></a>할당 방법

기본 및 표준 공용 Ip는 **정적** 할당을 지원 합니다.  리소스는 생성 될 때 IP 주소에 할당 됩니다. 리소스를 삭제 하면 IP 주소가 해제 됩니다.

기본 SKU 공용 IP 주소는 **동적** 할당을 지원 합니다. Dynamic은 기본 할당 방법입니다. 동적을 선택 하는 경우 IP 주소는 생성 시 리소스에 제공 **되지 않습니다** .

공용 IP 주소 리소스를에 연결할 때 IP가 할당 됩니다.

* 가상 머신 
* 첫 번째 가상 머신은 부하 분산 장치의 백 엔드 풀과 연결 됩니다.

리소스를 중지(또는 삭제)하면 IP 주소가 해제됩니다.  

예를 들어 **리소스 a**라는 리소스에서 공용 IP 리소스를 해제 합니다. **리소스 A** 는 공용 ip 리소스를 다시 할당 하는 경우 시작 시 다른 IP를 수신 합니다.

할당 방법이 **정적** 에서 **동적**으로 변경 되 면 IP 주소가 해제 됩니다. 연결 된 리소스에 대 한 IP 주소가 동일 하 게 유지 되도록 하려면 할당 메서드를 명시적으로 **정적**으로 설정 합니다. 고정 IP 주소가 즉시 할당됩니다.

> [!NOTE]
> 할당 방법을 **고정**으로 설정한 경우에도 공용 IP 주소 리소스에 할당된 실제 IP 주소를 지정할 수 없습니다. Azure는 리소스가 생성된 Azure 위치에서 사용 가능한 IP 주소 풀의 IP 주소를 할당됩니다.
>

정적 공용 IP 주소는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

* Azure 리소스와 통신하도록 방화벽 규칙을 업데이트해야 하는 경우
* DNS 이름을 확인, IP 주소를 변경하려면 A 레코드를 업데이트해야 하는 경우
* Azure 리소스가 IP 기반 보안 모델을 사용하는 다른 앱 또는 서비스와 통신하는 경우
* IP 주소에 연결 된 TLS/SSL 인증서를 사용 합니다.

> [!NOTE]
> Azure는 각 Azure 클라우드 지역의 고유한 범위에서 공용 IP 주소를 할당합니다. Azure [공용](https://www.microsoft.com/download/details.aspx?id=56519), [US 정부](https://www.microsoft.com/download/details.aspx?id=57063), [중국](https://www.microsoft.com/download/details.aspx?id=57062) 및 [독일](https://www.microsoft.com/download/details.aspx?id=57064) 클라우드의 범위(접두사) 목록을 다운로드할 수 있습니다.
>

## <a name="dns-hostname-resolution"></a>DNS 호스트 이름 확인

공용 IP 리소스에 대 한 DNS 도메인 이름 레이블을 지정 하는 옵션을 선택 합니다. 

이렇게 선택 하면 **domainnamelabel**에 대 한 매핑이 만들어집니다. cloudapp.azure.com는 Azure에서 관리 되는 DNS의 공용 IP에 대 한 **위치**입니다. 

예를 들어 다음을 사용 하 여 공용 IP를 만듭니다.

* **domainnamelabel** 인 **contoso**
* **미국 서 부** Azure **위치**

FQDN (정규화 된 도메인 이름) **contoso.westus.cloudapp.azure.com** 는 리소스의 공용 IP 주소로 확인 됩니다.

> [!IMPORTANT]
> 생성된 각 도메인 이름은 Azure 위치 내에서 고유해야 합니다.  
>

## <a name="dns-recommendations"></a>DNS 권장 사항

영역 이동이 필요한 경우 공용 IP의 FQDN을 마이그레이션할 수 없습니다. FQDN을 사용 하 여 공용 IP 주소를 가리키는 사용자 지정 CNAME 레코드를 만듭니다. 

다른 공용 IP로 이동 해야 하는 경우 FQDN을 업데이트 하는 대신 CNAME 레코드를 업데이트 합니다.

DNS 레코드에 대 한 [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) 또는 외부 dns 공급자를 사용할 수 있습니다. 

## <a name="virtual-machines"></a>가상 머신

공용 IP 주소를 **네트워크 인터페이스**에 할당하여 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신과 연결할 수 있습니다. 

공용 IP 주소에 대해 **동적** 또는 **정적** 을 선택 합니다. [네트워크 인터페이스에 IP 주소를 할당](virtual-network-network-interface-addresses.md)하는 방법에 대해 자세히 알아봅니다.

## <a name="internet-facing-load-balancers"></a>인터넷 연결 부하 분산 장치

두 [SKU](#sku) 의 공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당 하 여 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)에 연결할 수 있습니다. 공용 IP는 부하 분산 된 IP로 사용 됩니다. 

부하 분산 장치 프런트 엔드에 동적 또는 고정 공용 IP 주소를 할당할 수 있습니다. 부하 분산 장치 프런트 엔드에 여러 공용 IP 주소를 할당할 수 있습니다. 이 구성을 통해 TLS 기반 웹 사이트를 사용 하는 다중 테 넌 트 환경과 같은 [다중 VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 시나리오를 사용할 수 있습니다. 

Azure Load Balancer SKU에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="vpn-gateways"></a>VPN 게이트웨이

Azure [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) azure 가상 네트워크를 연결 합니다.

* Azure 가상 네트워크
* 온-프레미스 네트워크 

공용 IP 주소는 원격 네트워크와의 통신을 사용 하기 위해 VPN Gateway에 할당 됩니다. VPN Gateway에 기본 *동적* 공용 IP 주소만을 할당할 수 있습니다.

## <a name="application-gateways"></a>애플리케이션 게이트웨이

공용 IP 주소를 게이트웨이의 [프런트 엔드](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)구성에 할당하여 Azure **Application Gateway**와 연결할 수 있습니다. 

* 응용 프로그램 게이트웨이 V1 프런트 엔드 구성에 **동적** 기본 공용 IP를 할당 합니다. 
* V2 프런트 엔드 구성에 **정적** 표준 SKU 주소를 할당 합니다.

## <a name="at-a-glance"></a>요약

다음 표에서는 공용 IP를 최상위 리소스와 연결 하는 데 사용할 수 있는 속성과 가능한 할당 메서드를 보여 줍니다.

| 최상위 리소스 | IP 주소 연결 | 동적 | 정적 |
| --- | --- | --- | --- |
| 가상 머신 |Linux |예 |예 |
| 인터넷 연결 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| VPN 게이트웨이 |게이트웨이 IP 구성 |예 |아니요 |
| 프런트 엔드 |프런트 엔드 구성 |예(V1에만 해당) |예(V2에만 해당) |

## <a name="limits"></a>제한

IP 주소 지정에 대 한 제한은 Azure에서 [네트워킹에 대 한 전체 제한](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) 집합에 나열 됩니다. 

제한은 지역별, 구독별로 적용됩니다. 비즈니스 요구에 따라 기본 제한을 최대 제한까지 늘리려면 [지원 담당자에 게 문의 하세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .

## <a name="pricing"></a>가격 책정

공용 IP 주소에는 명목 요금이 부과될 수 있습니다. Azure의 IP 주소 가격 책정에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure의 개인 IP 주소](private-ip-addresses.md) 에 대 한 자세한 정보
* [Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-portal.md)

