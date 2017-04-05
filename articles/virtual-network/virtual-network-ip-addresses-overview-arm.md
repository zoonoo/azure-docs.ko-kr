---
title: "Azure의 IP 주소 유형 | Microsoft Docs"
description: "Azure의 공용 및 개인 IP 주소에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0506de98119576aae8f55cc6067f0bee95334c87
ms.lasthandoff: 03/31/2017


---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP 주소 유형 및 Azure에서 할당 메서드
다른 Azure 리소스, 온-프레미스 네트워크 및 인터넷과 통신하기 위해 Azure 리소스에 IP 주소를 할당할 수 있습니다. Azure에서 두 가지 유형의 IP 주소를 사용할 수 있습니다.

* **공용 IP 주소**: Azure 공용 웹 서비스를 포함한 인터넷과의 통신에 사용됩니다.
* **개인 IP 주소**: VPN 게이트웨이 또는 Express 경로 회로를 사용하여 Azure로 네트워크를 확장할 때 Azure 가상 네트워크(VNet) 및 온-프레미스 네트워크 내에서 통신하는 데 사용됩니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Resource Manager 배포 모델 사용을 설명하며 Microsoft에서는 대부분의 새로운 배포에 대해 [클래식 배포 모델](virtual-network-ip-addresses-overview-classic.md) 대신 이 모델을 사용하도록 권장합니다.
> 

클래식 배포 모델에 익숙한 경우 [클래식과 Resource Manager 간 IP 주소 지정의 차이](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments)를 확인하십시오.

## <a name="public-ip-addresses"></a>공용 IP 주소
공용 IP 주소를 사용하면 Azure 리소스가 [Azure Redis Cache](https://azure.microsoft.com/services/cache/), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [SQL Database](../sql-database/sql-database-technical-overview.md) 및 [Azure Storage](../storage/storage-introduction.md)와 같은 Azure의 공용 서비스 및 인터넷과 통신할 수 있습니다.

Azure 리소스 관리자에서 [공용 IP](resource-groups-networking.md#public-ip-address) 주소는 고유 속성을 가진 리소스입니다. 다음 리소스와 공용 IP 주소 리소스를 연결할 수 있습니다.

* VM(가상 컴퓨터)
* 인터넷 연결 부하 분산 장치
* VPN 게이트웨이
* 응용 프로그램 게이트웨이

### <a name="allocation-method"></a>할당 방법
IP 주소는 *동적* 또는 *정적*의 두 가지 방법으로 *공용* IP 리소스에 할당됩니다. 기본 할당 방법은 IP 주소를 만들 때 할당하지 *않는***동적** 방법입니다. 대신, 연결된 리소스(예: VM 또는 부하 분산 장치)를 시작하거나 만들 때 공용 IP 주소가 할당됩니다. 리소스를 중지(또는 삭제)하면 IP 주소가 해제됩니다. 이렇게 하면 리소스를 중지하고 시작할 때 IP 주소가 변경됩니다.

연결된 리소스의 IP 주소를 동일하게 유지하려면 할당 방법을 명시적으로 *static*으로 설정하면 됩니다. 이 경우 IP 주소가 즉시 할당됩니다. 리소스를 삭제하거나 할당 방법을 *dynamic*으로 변경하는 경우에만 해제됩니다.

> [!NOTE]
> 할당 방법을 *정적*으로 설정한 경우에도 *공용 IP 리소스*에 할당된 실제 IP 주소를 지정할 수 없습니다. 대신, 리소스가 생성된 Azure 위치의 사용 가능한 IP 주소 풀에서 할당됩니다.
>

정적 공용 IP 주소는 일반적으로 다음과 같은 시나리오에서 사용됩니다.

* 최종 사용자가 Azure 리소스와 통신하도록 방화벽 규칙을 업데이트해야 하는 경우
* DNS 이름을 확인, IP 주소를 변경하려면 A 레코드를 업데이트해야 하는 경우
* Azure 리소스가 IP 기반 보안 모델을 사용하는 다른 앱 또는 서비스와 통신하는 경우
* IP 주소에 연결된 SSL 인증서를 사용하는 경우

> [!NOTE]
> 공용 IP 주소(동적/정적)를 Azure 리소스에 할당할 때 사용되는 IP 범위 목록은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 게시되어 있습니다.
>

### <a name="dns-hostname-resolution"></a>DNS 호스트 이름 확인
Azure 관리 DNS 서버에서 공용 IP 주소에 대한 *domainnamelabel*.*location*.cloudapp.azure.com 매핑을 만드는 공용 IP 리소스에 대한 DNS 도메인 이름 레이블을 지정할 수 있습니다. 예를 들어 *domainnamelabel*에 **contoso**를, *location*에 **West US** Azure를 사용하여 공용 IP 리소스를 만들면 FQDN(정규화된 도메인 이름) **contoso.westus.cloudapp.azure.com**이 리소스의 공용 IP 주소로 확인됩니다. 이 FQDN을 사용하여 Azure의 공용 IP 주소를 가리키는 사용자 지정 도메인 CNAME 레코드를 만들 수 있습니다.

> [!IMPORTANT]
> 생성된 각 도메인 이름은 Azure 위치 내에서 고유해야 합니다.  
>

### <a name="virtual-machines"></a>가상 컴퓨터
공용 IP 주소를 **네트워크 인터페이스**에 할당하여 [Windows](../virtual-machines/windows/about.md) 또는 [Linux](../virtual-machines/virtual-machines-linux-about.md) VM과 연결할 수 있습니다. 네트워크 인터페이스가 여러 개인 VM의 경우 *기본* 네트워크 인터페이스에만 할당할 수 있습니다. VM에 동적 또는 정적 공용 IP 주소를 할당할 수 있습니다.

### <a name="internet-facing-load-balancers"></a>인터넷 연결 부하 분산 장치
공용 IP 주소를 부하 분산 장치 [프런트 엔드](../load-balancer/load-balancer-overview.md)구성에 할당하여 **Azure Load Balancer** 와 연결할 수 있습니다. 이 공용 IP 주소는 부하 분산 VIP(가상 IP 주소)로 사용됩니다. 부하 분산 장치 프런트 엔드에 동적 또는 정적 공용 IP 주소를 할당할 수 있습니다. 또한 부하 분산 장치 프런트 엔드에 여러 공용 IP 주소를 할당하여 SSL 기반 웹 사이트를 사용하는 다중 테넌트 환경과 같은 [다중 VIP](../load-balancer/load-balancer-multivip.md) 시나리오를 구현할 수도 있습니다.

### <a name="vpn-gateways"></a>VPN 게이트웨이
[Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md) 는 Azure VNet(가상 네트워크)를 다른 Azure VNet 또는 온-프레미스 네트워크에 연결하는 데 사용됩니다. 공용 IP 주소를 해당 **IP 구성** 에 할당해야 원격 네트워크와의 통신을 구현할 수 있습니다. 현재는 VPN 게이트웨이에 *동적* 공용 IP 주소만 할당할 수 있습니다.

### <a name="application-gateways"></a>응용 프로그램 게이트웨이
공용 IP 주소를 게이트웨이의 [프런트 엔드](../application-gateway/application-gateway-introduction.md)구성에 할당하여 Azure **응용 프로그램 게이트웨이** 와 연결할 수 있습니다. 이 공용 IP 주소는 부하 분산 VIP로 사용됩니다. 현재는 응용 프로그램 게이트웨이 프런트 엔드 구성에 *동적* 공용 IP 주소만 할당할 수 있습니다.

### <a name="at-a-glance"></a>요약
아래 표는 공용 IP 주소가 최상위 리소스와 연결될 수 있는 특정 속성 및 사용 가능한 할당 메서드(동적 또는 정적)를 보여 줍니다.

| 최상위 리소스 | IP 주소 연결 | 않는 | 공용 |
| --- | --- | --- | --- |
| 가상 컴퓨터 |Linux |예 |예 |
| 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| VPN 게이트웨이 |게이트웨이 IP 구성 |예 |아니요 |
| 프런트 엔드 |프런트 엔드 구성 |예 |아니요 |

## <a name="private-ip-addresses"></a>개인 IP 주소
개인 IP 주소를 사용하면 Azure 리소스가 인터넷 연결이 가능한 IP 주소를 사용하지 않고 VPN 게이트웨이 또는 Express 경로 회로를 통해 [가상 네트워크](virtual-networks-overview.md) 또는 온-프레미스 네트워크의 다른 리소스와 통신할 수 있습니다.

Azure Resource Manager 배포 모델에서 개인 IP 주소는 다음과 같은 유형의 Azure 리소스에 연결됩니다.

* VM
* 내부 부하 분산 장치(ILB)
* 응용 프로그램 게이트웨이

### <a name="allocation-method"></a>할당 방법
리소스가 연결된 서브넷의 주소 범위에서 개인 IP 주소가 할당됩니다. 서브넷 자체의 주소 범위는 VNet 주소 범위의 일부입니다.

개인 IP 주소를 할당하는 방법으로 *동적* 또는 *정적*이라는 두 가지 방법이 있습니다. 기본 할당 방법은 DHCP를 사용하여 리소스의 서브넷에서 IP 주소가 자동으로 할당되는 *동적*할당입니다. 이 IP 주소는 리소스를 중지하고 시작할 때 변경될 수 있습니다.

할당 방법을 *정적* 으로 설정하면 IP 주소를 동일하게 유지할 수 있습니다. 이 경우 리소스 서브넷의 일부인 유효한 IP 주소도 제공해야 합니다.

정적 개인 IP 주소가 일반적으로 사용되는 대상은 다음과 같습니다.

* 도메인 컨트롤러 또는 DNS 서버 역할을 하는 VM
* IP 주소를 사용하는 방화벽 규칙이 필요한 리소스
* IP 주소를 통해 다른 앱/리소스에서 액세스하는 리소스

### <a name="virtual-machines"></a>가상 컴퓨터
개인 IP 주소는 [Windows](../virtual-machines/windows/about.md) 또는 [Linux](../virtual-machines/virtual-machines-linux-about.md) VM의 **네트워크 인터페이스**에 할당됩니다. 다중 네트워크 인터페이스 VM의 경우 각 인터페이스에 개인 IP 주소가 할당됩니다. 네트워크 인터페이스의 할당 방법을 동적 또는 정적으로 지정할 수 있습니다.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>내부 DNS 호스트 이름 확인(VM)
모든 Azure VM은 명시적으로 사용자 지정 DNS 서버를 구성하지 않으면 기본적으로 [Azure 관리 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) 로 구성됩니다. 이러한 DNS 서버는 동일한 VNet 내에 있는 VM에 대한 내부 이름 확인을 제공합니다.

VM을 만들 때 개인 IP 주소에 대한 호스트 이름 매핑이 Azure 관리 DNS 서버에 추가됩니다. 다중 네트워크 인터페이스 VM의 경우 기본 네트워크 인터페이스의 개인 IP 주소에 호스트 이름이 매핑됩니다.

Azure 관리 DNS 서버를 사용하여 구성된 VM은 해당 VNet 내 모든 VM의 호스트 이름을 개인 IP 주소로 확인할 수 있게 됩니다.

### <a name="internal-load-balancers-ilb--application-gateways"></a>ILB(내부 부하 분산 장치) 및 응용 프로그램 게이트웨이
[Azure 내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md)(ILB) 또는 [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)의 **프런트 엔드** 구성에 개인 IP 주소를 할당할 수 있습니다. 이 개인 IP 주소는 가상 네트워크(VNet) 내 리소스와 VNet에 연결된 원격 네트워크에만 액세스할 수 있는 내부 끝점으로 사용됩니다. 프런트 엔드 구성에 동적 또는 정적 개인 IP 주소를 할당할 수 있습니다.

### <a name="at-a-glance"></a>요약
아래 표는 개인 IP 주소가 최상위 리소스와 연결될 수 있는 특정 속성 및 사용 가능한 할당 메서드(동적 또는 정적)를 보여 줍니다.

| 최상위 리소스 | IP 주소 연결 | 않는 | 공용 |
| --- | --- | --- | --- |
| 가상 컴퓨터 |Linux |예 |예 |
| 부하 분산 장치 |프런트 엔드 구성 |예 |예 |
| 프런트 엔드 |프런트 엔드 구성 |예 |예 |

## <a name="limits"></a>제한
IP 주소 지정에 적용되는 제한은 Azure에서 [네트워킹에 대한 제한](../azure-subscription-service-limits.md#networking-limits) 전체 집합에 나와 있습니다. 이러한 제한은 지역별, 구독별로 적용됩니다. 비즈니스에 따라 최대 한도까지 기본 제한을 증가시키려면 [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 하세요.

## <a name="pricing"></a>가격
공용 IP 주소에는 명목 요금이 부과될 수 있습니다. Azure의 IP 주소 가격 책정에 대한 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure 포털을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-portal.md)
* [템플릿을 사용하여 고정 공용 IP를 사용하는 VM 배포](virtual-network-deploy-static-pip-arm-template.md)
* [Azure 포털을 사용하여 고정 개인 IP 주소를 사용하는 VM 배포](virtual-networks-static-private-ip-arm-pportal.md)

