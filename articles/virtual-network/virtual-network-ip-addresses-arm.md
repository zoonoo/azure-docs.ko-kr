<properties 
   pageTitle="Azure 네트워크 리소스 공급자의 공용 및 개인 IP 주소 | Microsoft Azure"
   description="Azure 리소스 관리자의 네트워크 리소스 공급자에 대한 공용 및 개인 IP 주소에 대해 알아보기"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# Azure 가상 네트워크의 IP 주소
이 문서에서는 가상 컴퓨터, 부하 분산 장치, VPN 게이트웨이 및 앱 게이트웨이의 IP 주소를 다룹니다.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## 공용 IP 주소
공용 IP 주소를 사용하면 Azure 리소스가 [Azure Redis Cache](https://azure.microsoft.com/services/cache), [Azure 이벤트 허브](https://azure.microsoft.com/services/event-hubs) 같은 Azure의 공개 서비스 및 인터넷과 통신할 수 있습니다. 공용 IP 주소는 독립 리소스이며 [VM(가상 컴퓨터)](virtual-machines-about.md), [LB(부하 분산 장치)](load-balancer-overview.md) 등 다른 유형의 Azure 리소스에 연결할 수 있습니다.

### 공용 IP 주소 할당 방법
공용 IP 주소는 지정된 위치에서 사용할 수 있는 IP 주소 풀에서 할당됩니다. Microsoft Azure 데이터 센터에서 사용하는 IP 주소 범위의 전체 목록은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 게시되어 있습니다.

IP 주소는 *동적* 또는 *정적*의 두 가지 방법으로 공용 IP 리소스에 할당됩니다. 기본 할당 방법은 *동적*입니다. *동적* 할당의 경우 주소가 생성되는 시점에는 **공용 IP 주소 리소스**에 IP 주소가 할당되지 *않습니다*. *공용 IP 주소 리소스*에 연결된 리소스(VM 또는 부하 분산 장치)가 생성되거나 시작되면 IP 주소 풀에서 IP 주소가 할당됩니다. 연결된 리소스가 삭제 또는 중지되면 이 IP 주소는 할당이 취소되어 사용 가능한 풀로 회수됩니다.

*정적* 할당의 경우 주소가 생성되는 시점에 *공용 IP 주소 리소스*에 IP 주소가 할당됩니다. 이 경우에 연결된 리소스의 상태에 관계없이 동일한 IP 주소가 할당된 상태로 유지됩니다. *공용 IP 주소 리소스*가 삭제되거나 할당 방법이 *동적*으로 변경되는 경우에만 IP 주소가 사용 가능한 풀로 회수됩니다.

### DNS 확인
공용 IP 리소스에 대한 DNS 도메인 이름 레이블을 지정할 수 있습니다. 이렇게 하면 Azure DNS 서버에 해당 DNS 항목이 생성됩니다. 해당 FQDN *domainnamelabel*.*location*.cloudapp.azure.com은 공용 IP 리소스에 할당된 IP 주소에 대해 전역적으로 확인할 수 있습니다.

아래에서는 공용 IP 주소와 연결할 수 있는 다양한 리소스 유형을 살펴보겠습니다.

### 가상 컴퓨터
공용 IP 주소는 NIC(네트워크 인터페이스 카드)를 통해 [VM(가상 컴퓨터)](virtual-machines-about.md)과 연결됩니다. 각 VM은 연결된 NIC 수에 관계없이 하나의 공용 IP 주소만 가질 수 있습니다. *동적으로* 할당된 *공용 IP 주소 리소스*만 VM NIC와 연결할 수 있습니다. 다중 NIC VM인 경우 *공용 IP 주소 리소스*를 기본 NIC에만 연결할 수 있습니다.

### Azure 부하 분산 장치
공용 IP 주소를 [Azure LB(부하 분산 장치)](load-balancer-overview.md) 프런트 엔드 구성에 연결할 수 있습니다. 이렇게 연결된 공용 IP 주소는 인터넷을 통해 연결할 수 있는 부하 분산 VIP(가상 IP 주소) 역할을 합니다. *동적* 또는 *정적* 공용 IP 리소스 모두 LB와 연결할 수 있습니다. 여러 *공용 IP 주소 리소스*를 LB 프런트 엔드 구성 하나에 연결할 수 있습니다. 이 방법으로 다중 SSL 기반 웹 사이트가 있는 다중 테넌트 같은 시나리오를 처리할 수 있습니다.

### 응용 프로그램 게이트웨이
공용 IP 주소를 [Azure 응용 프로그램 게이트웨이](application-gateway-introduction.md) 프런트 엔드 구성과 연결하여 인터넷 연결이 가능한 부하 분산 VIP(가상 IP)로 구성할 수 있습니다. 현재는 *동적으로* 할당된 공용 IP 리소스만 응용 프로그램 게이트웨이와 연결할 수 있습니다. 하지만 여러 공용 IP 주소를 연결할 수 있습니다.

### VPN 게이트웨이
Azure 가상 네트워크와 온-프레미스 네트워크 또는 또 다른 Azure 가상 네트워크 사이에 VPN 연결을 만드는 과정에서 공용 IP 주소를 [Azure VPN 게이트웨이](vpn-gateway-about-vpngateways.md) IP 구성과 연결해야 합니다. 현재는 *동적으로* 할당된 공용 IP 리소스만 VPN 게이트웨이와 연결할 수 있습니다.

### 요약

|리소스|정적 할당|동적 할당|여러 IP 주소|
|---|---|---|---|
|VM(가상 컴퓨터)/NIC(네트워크 인터페이스 카드)|예|아니요|아니요|
|부하 분산 장치 프런트 엔드|예|예|예|
|응용 프로그램 게이트웨이 프런트 엔드|예|아니요|예|
|VPN 게이트웨이|예|아니요|아니요|

## 개인 IP 주소
개인 IP 주소를 사용하면 인터넷 연결이 가능한 IP 주소를 사용하지 않고 가상 네트워크의 리소스 간 통신이 가능합니다. 이 IP 주소는 가상 네트워크의 서브넷 주소 범위 내에서 할당됩니다.

### 개인 IP 주소 할당 방법
IP 주소를 할당하는 방법으로 *동적* 할당 또는 *정적* 할당의 두 가지가 있습니다. 기본 할당 방법은 DHCP를 사용하여 IP 주소가 할당되는 *동적* 할당입니다. 또는 할당 방법을 *정적*으로 명시적으로 지정하고 IP 주소를 지정할 수도 있습니다. 이 경우 IP 주소가 서브넷 주소 범위 내에 있고 다른 리소스에 할당되지 않았다면 지정된 IP 주소에 리소스가 할당됩니다.

개인 IP 주소를 지정할 수 있는 여러 리소스 유형이 있습니다. 이러한 리소스 유형에는 두 가지 할당 방법(*정적* 또는 *동적*)을 모두 사용할 수 있습니다.

### 가상 컴퓨터
개인 IP 주소는 [VM(가상 컴퓨터)](virtual-machines-about.md)의 NIC(네트워크 인터페이스 카드)에 할당됩니다. 각 VM은 연결된 NIC 수만큼 개인 IP 주소를 가질 수 있습니다.

#### 내부 DNS 호스트 이름 확인
모든 Azure VM은 생성 시 명시적으로 지정하지 않는 한 [Azure에서 관리하는 DNS 서버](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)를 통해 구성됩니다. Azure에서 관리하는 DNS 서버를 사용할 경우 VM의 개인 IP 주소에 대해 VM 호스트 이름을 확인하는 DNS 레코드가 자동으로 생성됩니다. 다중 NIC VM의 경우 기본 NIC의 개인 IP 주소에 대해 호스트 이름이 확인됩니다.

### 내부 부하 분산 장치
개인 IP 주소를 [Azure ILB(내부 부하 분산 장치)](load-balancer-internal-overview.md) 프런트 엔드에 연결할 수 있습니다. 이렇게 연결한 개인 IP 주소는 가상 네트워크 내부의 리소스에 대한 부하 분산 VIP(가상 IP) 주소 역할을 합니다. 이렇게 하면 IP 주소를 인터넷에 노출하지 않고 부하를 분산할 수 있습니다.

### 응용 프로그램 게이트웨이
개인 IP 주소를 [Azure 응용 프로그램 게이트웨이](application-gateway-introduction.md) 프런트 엔드에 할당하고, ILB(내부 부하 분산 장치) 끝점이라고 알려진 인터넷에 노출되지 않은 내부 끝점을 사용하여 구성할 수 있습니다. 동작 및 할당 방법은 위에서 설명한 ILB와 비슷합니다.

### 요약
|리소스|정적 할당|동적 할당|여러 IP 주소|
|---|---|---|---|
|VM(가상 컴퓨터)/NIC(네트워크 인터페이스 카드)|예|예|예|
|부하 분산 장치 프런트 엔드|예|예|예|
|응용 프로그램 게이트웨이 프런트 엔드|예|예|예|

## 다음 단계


[네트워킹을 위한 Azure PowerShell 참조](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure 리소스 관리자 템플릿 언어](../resource-group-authoring-templates.md)

[Azure 네트워킹- 일반적으로 사용되는 템플릿](https://github.com/Azure/azure-quickstart-templates)

[계산 리소스 공급자](../virtual-machines-azurerm-versus-azuresm)

[Azure 리소스 관리자 개요](../resource-group-overview)

[Azure 리소스 관리자의 역할 기반 액세스 제어](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Azure 리소스 관리자에서 태그 사용](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[템플릿 배포 ](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->