---
title: "Azure의 IP 주소 유형 | Microsoft Docs"
description: "Azure의 공용 및 개인 IP 주소에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 8ddd582ed159e10add896252c40feb19780c42fb
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP 주소 유형 및 Azure에서 할당 메서드

다른 Azure 리소스, 온-프레미스 네트워크 및 인터넷과 통신하기 위해 Azure 리소스에 IP 주소를 할당할 수 있습니다. Azure에서 두 가지 유형의 IP 주소를 사용할 수 있습니다.

* **공용 IP 주소**: Azure 공용 웹 서비스를 포함한 인터넷과의 통신에 사용됩니다.
* **개인 IP 주소**: VPN 게이트웨이 또는 Express 경로 회로를 사용하여 Azure로 네트워크를 확장할 때 Azure 가상 네트워크(VNet) 및 온-프레미스 네트워크 내에서 통신하는 데 사용됩니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 [클래식 배포 모델](virtual-network-ip-addresses-overview-classic.md) 대신 이 모델을 사용하도록 권장합니다.
> 

클래식 배포 모델에 익숙한 경우 [클래식과 Resource Manager 간 IP 주소 지정의 차이](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)를 확인하십시오.

## <a name="public-ip-addresses"></a>공용 IP 주소

공용 IP 주소를 사용하면 Azure 리소스가 [Azure Redis Cache](https://azure.microsoft.com/services/cache), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs), [SQL Database](../sql-database/sql-database-technical-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Azure Storage](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)와 같은 Azure의 공용 서비스 및 인터넷과 통신할 수 있습니다.

Azure Resource Manager에서 [공용 IP](virtual-network-public-ip-address.md) 주소는 고유 속성을 가진 리소스입니다. 공용 IP 주소 리소스를 연결할 수 있는 리소스는 다음과 같습니다.

* 가상 컴퓨터 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* VPN 게이트웨이
* 응용 프로그램 게이트웨이

### <a name="ip-address-version"></a>IP 주소 버전

공용 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 공용 IPv6 주소는 인터넷 연결 부하 분산 장치에만 할당할 수 있습니다.

### <a name="sku"></a>SKU

공용 IP 주소는 다음 SKU 중 하나로 만들어집니다.

#### <a name="basic"></a>Basic

SKU 도입 전에 생성된 모든 공용 IP 주소는 기본 SKU 공용 IP 주소입니다. SKU의 도입으로 공용 IP 주소로 선택하려는 SKU를 지정하는 옵션이 만들어집니다. 기본 SKU 주소는 다음과 같습니다.

- 고정 또는 동적 할당 방법으로 할당됩니다.
- 네트워크 인터페이스, VPN Gateway, Application Gateway 및 인터넷 연결 부하 분산 장치 등 공용 IP 주소를 할당할 수 있는 모든 Azure 리소스에 할당됩니다.
- 특정 영역에 할당될 수 있습니다.
- 영역이 중복되지 않습니다. 사용 가능한 영역에 대한 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

#### <a name="standard"></a>Standard

표준 SKU 공용 IP 주소는 다음과 같습니다.

- 고정 할당 방법으로만 할당됩니다.
- 네트워크 인터페이스 또는 표준 인터넷 연결 부하 분산 장치에 할당됩니다. Azure Load Balancer SKU에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
- 기본적으로 중복된 영역입니다. 영역을 만들고 특정 가용성 영역에서 보장할 수 있습니다.  사용 가능한 영역에 대한 자세한 내용은 [가용성 영역 개요](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.
 
> [!NOTE]
> 가상 컴퓨터의 네트워크 인터페이스에 표준 SKU 공용 IP 주소를 할당할 때 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 사용하여 원하는 트래픽을 명시적으로 허용해야 합니다.  네트워크 보안 그룹을 만들어 연결하고 원하는 트래픽을 명시적으로 허용해야 리소스와 통신할 수 있습니다.

표준 SKU는 미리 보기 릴리스 상태입니다. 표준 SKU 공용 IP 주소를 만들기 전에 먼저 미리 보기에 등록하고 지원되는 위치에 주소를 만들어야 합니다. 미리 보기에 등록하려면 [표준 SKU 미리 보기에 등록](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview)을 참조하세요. 지원되는 위치(지역) 목록은 [지역 가용성](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability)을 참조하고 추가 지역 지원은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 모니터링하세요.


### <a name="allocation-method"></a>할당 방법

IP 주소는 *동적* 또는 *고정*이라는 두 가지 방법으로 공용 IP 리소스에 IP 주소를 할당합니다. 기본 할당 방법은 IP 주소를 만들 때 할당하지 *않는***동적** 방법입니다. 대신, 연결된 리소스(예: VM 또는 부하 분산 장치)를 시작하거나 만들 때 공용 IP 주소가 할당됩니다. 리소스를 중지(또는 삭제)하면 IP 주소가 해제됩니다. 예를 들어 IP 주소는 리소스 A에서 해제된 후에 다른 리소스에 할당될 수 있습니다. 리소스 A가 중지된 동안 IP 주소가 다른 리소스에 할당되면 리소스 A를 다시 시작할 때 다른 IP 주소가 할당됩니다.

연결된 리소스의 IP 주소를 동일하게 유지하려면 할당 방법을 명시적으로 *static*으로 설정하면 됩니다. 고정 IP 주소가 즉시 할당됩니다. 리소스를 삭제하거나 할당 방법을 *동적*으로 변경하는 경우에만 주소가 해제됩니다.

> [!NOTE]
> 할당 방법을 *고정*으로 설정한 경우에도 공용 IP 주소 리소스에 할당된 실제 IP 주소를 지정할 수 없습니다. Azure는 리소스가 생성된 Azure 위치에서 사용 가능한 IP 주소 풀의 IP 주소를 할당됩니다.
>

정적 공용 IP 주소는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

* Azure 리소스와 통신하도록 방화벽 규칙을 업데이트해야 하는 경우
* DNS 이름을 확인, IP 주소를 변경하려면 A 레코드를 업데이트해야 하는 경우
* Azure 리소스가 IP 기반 보안 모델을 사용하는 다른 앱 또는 서비스와 통신하는 경우
* IP 주소에 연결된 SSL 인증서를 사용하는 경우

> [!NOTE]
> Azure는 각 Azure 지역에 고유한 범위에서 공용 IP 주소를 할당합니다. 자세한 내용은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.
>

### <a name="dns-hostname-resolution"></a>DNS 호스트 이름 확인
Azure 관리 DNS 서버에서 공용 IP 주소에 대한 *domainnamelabel*.*location*.cloudapp.azure.com 매핑을 만드는 공용 IP 리소스에 대한 DNS 도메인 이름 레이블을 지정할 수 있습니다. 예를 들어 **미국 서부** Azure *위치*에서 *domainnamelabel*로 **contoso**를 사용하여 공용 IP 리소스를 만들면 FQDN(정규화된 도메인 이름) **contoso.westus.cloudapp.azure.com**이 리소스의 공용 IP 주소로 결정됩니다. FQDN을 사용하여 Azure의 공용 IP 주소를 가리키는 사용자 지정 도메인 CNAME 레코드를 만들 수 있습니다.

> [!IMPORTANT]
> 생성된 각 도메인 이름은 Azure 위치 내에서 고유해야 합니다.  
>

### <a name="virtual-machines"></a>가상 컴퓨터

공용 IP 주소를 **네트워크 인터페이스**에 할당하여 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 컴퓨터와 연결할 수 있습니다. 가상 컴퓨터에 동적 또는 고정 공용 IP 주소를 할당할 수 있습니다. [네트워크 인터페이스에 IP 주소를 할당](virtual-network-network-interface-addresses.md)하는 방법에 대해 자세히 알아봅니다.

### <a name="internet-facing-load-balancers"></a>인터넷 연결 부하 분산 장치

공용 IP 주소를 부하 분산 장치 **프런트 엔드** 구성에 할당하여 [SKU](#SKU) 또는 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)와 연결할 수 있습니다. 공용 IP 주소는 부하가 분산된 VIP(가상 IP 주소)로 사용됩니다. 부하 분산 장치 프런트 엔드에 동적 또는 고정 공용 IP 주소를 할당할 수 있습니다. 또한 부하 분산 장치 프런트 엔드에 여러 공용 IP 주소를 할당하여 SSL 기반 웹 사이트를 사용하는 다중 테넌트 환경과 같은 [다중 VIP](../load-balancer/load-balancer-multivip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 시나리오를 구현할 수도 있습니다. Azure Load Balancer SKU에 대한 자세한 내용은 [Azure Load Balancer 표준 SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

### <a name="vpn-gateways"></a>VPN 게이트웨이

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)는 Azure 가상 네트워크를 다른 Azure 가상 네트워크 또는 온-프레미스 네트워크에 연결합니다. 공용 IP 주소를 VPN Gateway에 할당하여 원격 네트워크와의 통신을 구현할 수 있습니다. VPN Gateway에 *동적* 공용 IP 주소만을 할당할 수 있습니다.

### <a name="application-gateways"></a>응용 프로그램 게이트웨이

공용 IP 주소를 게이트웨이의 [프런트 엔드](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)구성에 할당하여 Azure **응용 프로그램 게이트웨이** 와 연결할 수 있습니다. 이 공용 IP 주소는 부하 분산 VIP로 사용됩니다. 응용 프로그램 게이트웨이 프런트 엔드 구성에 *동적* 공용 IP 주소만을 할당할 수 있습니다.

### <a name="at-a-glance"></a>요약
다음 표는 공용 IP 주소를 최상위 리소스와 연결할 수 있는 특정 속성 및 사용 가능한 할당 메서드(동적 또는 고정)를 보여줍니다.

| 최상위 리소스 | IP 주소 연결 | 않는 | 공용 |
| --- | --- | --- | --- |
| 가상 컴퓨터 |Linux |예 |예 |
| 인터넷 연결 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| VPN 게이트웨이 |게이트웨이 IP 구성 |예 |아니요 |
| 프런트 엔드 |프런트 엔드 구성 |예 |아니요 |

## <a name="private-ip-addresses"></a>개인 IP 주소
개인 IP 주소를 사용하면 Azure 리소스가 인터넷 연결이 가능한 IP 주소를 사용하지 않고 VPN 게이트웨이 또는 Express 경로 회로를 통해 [가상 네트워크](virtual-networks-overview.md) 또는 온-프레미스 네트워크의 다른 리소스와 통신할 수 있습니다.

Azure Resource Manager 배포 모델에서 개인 IP 주소는 다음과 같은 유형의 Azure 리소스에 연결됩니다.

* 가상 컴퓨터 네트워크 인터페이스
* 내부 부하 분산 장치(ILB)
* 응용 프로그램 게이트웨이

### <a name="ip-address-version"></a>IP 주소 버전

개인 IP 주소는 IPv4 또는 IPv6 주소를 사용하여 만들어집니다. 개인 IPv6 주소는 동적 할당 메서드를 사용하여 할당될 수 있습니다. 가상 네트워크의 개인 IPv6 주소 간에 통신할 수 없습니다. 인터넷 연결 부하 분산 장치를 통해 인터넷의 개인 IPv6 주소에 대한 인바운드와 통신할 수 있습니다. 자세한 내용은 [IPv6으로 인터넷 연결 부하 분산 장치 만들기](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

### <a name="allocation-method"></a>할당 방법

리소스가 연결된 서브넷의 주소 범위에서 개인 IP 주소가 할당됩니다. 서브넷 자체의 주소 범위는 가상 네트워크 주소 범위의 일부입니다.

개인 IP 주소를 할당하는 방법으로 *동적* 또는 *정적*이라는 두 가지 방법이 있습니다. 기본 할당 방법은 DHCP를 사용하여 리소스의 서브넷에서 IP 주소가 자동으로 할당되는 *동적*할당입니다. 이 IP 주소는 리소스를 중지하고 시작할 때 변경될 수 있습니다.

할당 방법을 *정적* 으로 설정하면 IP 주소를 동일하게 유지할 수 있습니다. *고정*을 지정한 경우 리소스 서브넷의 일부인 유효한 IP 주소도 지정합니다.

정적 개인 IP 주소가 일반적으로 사용되는 대상은 다음과 같습니다.

* 도메인 컨트롤러 또는 DNS 서버 역할을 하는 가상 컴퓨터
* IP 주소를 사용하는 방화벽 규칙이 필요한 리소스
* IP 주소를 통해 다른 앱/리소스에서 액세스하는 리소스

### <a name="virtual-machines"></a>가상 컴퓨터

개인 IP 주소는 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 컴퓨터의 **네트워크 인터페이스**에 할당됩니다. 가상 컴퓨터에 여러 네트워크 인터페이스가 있는 경우 개인 IP 주소는 각 네트워크 인터페이스에 할당됩니다. 네트워크 인터페이스의 할당 방법을 동적 또는 정적으로 지정할 수 있습니다.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>내부 DNS 호스트 이름 확인(가상 컴퓨터)

모든 Azure 가상 컴퓨터는 명시적으로 사용자 지정 DNS 서버를 구성하지 않으면 기본적으로 [Azure 관리 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)로 구성됩니다. 이러한 DNS 서버는 동일한 가상 네트워크 내에 있는 가상 컴퓨터에 대한 내부 이름 확인을 제공합니다.

가상 컴퓨터를 만들 때 개인 IP 주소에 호스트 이름을 매핑하는 작업이 Azure 관리 DNS 서버에 추가됩니다. 가상 컴퓨터에 다중 네트워크 인터페이스가 있는 경우 기본 네트워크 인터페이스의 개인 IP 주소에 호스트 이름이 매핑됩니다.

Azure 관리 DNS 서버를 사용하여 구성된 가상 컴퓨터는 동일한 가상 네트워크 내에 있는 모든 가상 컴퓨터의 호스트 이름을 해당 개인 IP 주소로 결정할 수 있게 됩니다.

### <a name="internal-load-balancers-ilb--application-gateways"></a>ILB(내부 부하 분산 장치) 및 응용 프로그램 게이트웨이

[Azure 내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)(ILB) 또는 [Azure Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)의 **프런트 엔드** 구성에 개인 IP 주소를 할당할 수 있습니다. 이 개인 IP 주소는 가상 네트워크 내의 리소스와 가상 네트워크에 연결된 원격 네트워크에만 액세스할 수 있는 내부 끝점으로 사용됩니다. 프런트 엔드 구성에 동적 또는 고정 개인 IP 주소를 할당할 수 있습니다.

### <a name="at-a-glance"></a>요약
다음 표는 개인 IP 주소가 최상위 리소스와 연결될 수 있는 특정 속성 및 사용 가능한 할당 메서드(동적 또는 정적)를 보여 줍니다.

| 최상위 리소스 | IP 주소 연결 | 않는 | 공용 |
| --- | --- | --- | --- |
| 가상 컴퓨터 |Linux |예 |예 |
| 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| 프런트 엔드 |프런트 엔드 구성 |예 |예 |

## <a name="limits"></a>제한
IP 주소 지정에 적용되는 제한은 Azure에서 [네트워킹에 대한 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) 전체 집합에 나와 있습니다. 제한은 지역별, 구독별로 적용됩니다. 비즈니스에 따라 최대 한도까지 기본 제한을 증가시키려면 [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 하세요.

## <a name="pricing"></a>가격
공용 IP 주소에는 명목 요금이 부과될 수 있습니다. Azure의 IP 주소 가격 책정에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-portal.md)
* [템플릿을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-template.md)
* [Azure 포털을 사용하여 고정 개인 IP 주소를 사용하는 VM 배포](virtual-networks-static-private-ip-arm-pportal.md)

