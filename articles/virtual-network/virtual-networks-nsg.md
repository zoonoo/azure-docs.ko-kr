<properties 
   pageTitle="NSG(네트워크 보안 그룹)란?"
	description="NSG(네트워크 보안 그룹)에 대해 알아봅니다."
	services="virtual-network"
	documentationCenter="na"
	authors="telmosampaio"
	manager="carolz"
	editor="tysonn"/>
<tags 
   ms.service="virtual-network"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/13/2015"
	ms.author="telmos"/>

# NSG(네트워크 보안 그룹)란?

NSG를 사용하여 가상 네트워크에 있는 하나 이상의 VM(가상 컴퓨터) 인스턴스에 대한 트래픽을 제어할 수 있습니다. 네트워크 보안 그룹은 구독에 연결된 최상위 수준 개체입니다. NSG에는 VM 인스턴스에 대한 트래픽을 허용하거나 거부할 수 있는 액세스 제어 규칙이 포함되어 있습니다. NSG의 규칙은 언제든지 변경할 수 있으며, 변경 내용은 연결된 모든 인스턴스에 적용됩니다. NSG를 사용하려면 지역(위치)과 연결된 VNet이 있어야 합니다.

>[AZURE.WARNING]NSG는 선호도 그룹과 연결된 VNet과 호환되지 않습니다. 지역 VNet이 없는데 끝점에 대한 트래픽을 제어하려는 경우 [네트워크 ACL(액세스 제어 목록)란?](./virtual-networks-acl.md)을 참조하세요. [VNet을 지역 VNet에 마이그레이션](./virtual-networks-migrate-to-regional-vnet.md)할 수도 있습니다.

NSG는 VM에 연결하거나 VNet 내에 있는 서브넷에 연결할 수 있습니다. NSG가 VM과 연결된 경우 VM 인스턴스에서 보내고 받는 모든 트래픽에 적용됩니다. NSG가 VNet 내에 있는 서브넷에 적용된 경우 서브넷의 모든 VM 인스턴스에서 보내고 받는 모든 트래픽에 적용됩니다. VM 또는 서브넷은 오직 1개의 NSG와 연결될 수 있으며, 각 NSG에는 규칙이 200개까지 포함될 수 있습니다. 구독당 100개의 NSG가 있을 수 있습니다.

>[AZURE.NOTE]끝점 기반 ACL과 네트워크 보안 그룹은 동일한 VM 인스턴스에서 지원되지 않습니다. NSG를 사용하려는데 끝점 ACL이 이미 있는 경우 먼저, 끝점 ACL을 제거합니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 끝점에 대한 ACL(액세스 제어 목록) 관리](virtual-networks-acl-powershell.md)를 참조하세요.

## 네트워크 보안 그룹은 어떻게 작동하나요?

네트워크 보안 그룹은 끝점 기반 ACL과 다릅니다. 끝점 ACL은 입력 끝점을 통해 노출되는 공용 포트에서만 작동합니다. NSG는 하나 이상의 VM 인스턴스에서 작동하며 VM의 모든 인바운드 및 아웃바운드 트래픽을 제어합니다.

네트워크 보안 그룹에는 *이름* 및 설명 레이블이 있습니다. 또한 네트워크 보안 그룹은 *지역*에 연결됩니다. 그리고 **인바운드** 및 **아웃바운드** 유형의 규칙이 포함되어 있습니다. 인바운드 규칙은 VM으로 들어오는 패킷에 적용되며, 아웃바운드 규칙은 VM에서 나가는 패킷에 적용됩니다. 규칙은 VM이 있는 호스트에서 적용됩니다. 들어오거나 나가는 패킷은 삭제되지 않는다면 **허용** 규칙과 일치해야 들어오거나 나갈 수 있습니다.

규칙은 우선 순위에 따라 처리됩니다. 예를 들어 낮은 우선 순위 번호(예: 100)를 가진 규칙이 더 높은 우선 순위 번호(예: 200)를 가진 규칙보다 먼저 처리됩니다. 일치하는 항목이 발견되면 추가 규칙은 처리되지 않습니다.

규칙에서는 다음을 지정합니다.

- **이름:** 규칙의 고유 식별자

- **유형:** 인바운드/아웃바운드

- **우선 순위:** <You can specify an integer between 100 and 4096>

- **원본 IP 주소:** 원본 IP 범위의 CIDR

- **원본 포트 범위:** <integer or range between 0 and 65536>

- **대상 IP 범위:** 대상 IP 범위의 CIDR

- **대상 포트 범위:** <integer or range between 0 and 65536>

- **프로토콜:** <TCP, UDP 또는 ‘*’ 허용>

- **액세스:** 허용/거부

### 기본 규칙

NSG에는 기본 규칙이 있습니다. 기본 규칙은 삭제할 수 없지만, 가장 낮은 우선순위가 할당되기 때문에 직접 만든 규칙으로 재정의할 수 있습니다. 기본 규칙은 플랫폼에서 권장하는 기본 설정을 설명합니다. 아래 기본 규칙에 설명된 대로, VNet에서 시작하고 끝나는 트래픽은 인바운드와 아웃바운드 방향 둘 다에서 허용됩니다.

인터넷에 대한 연결은 아웃바운드 방향에 대해 허용되지만, 기본적으로 인바운드 방향에 대해서는 차단됩니다. Azure의 LB(부하 분산 장치)가 VM의 상태를 검색하도록 허용하는 기본 규칙이 있습니다. NSG의 VM 또는 VM 집합이 부하 분산된 집합에 참여하지 않는 경우 이 규칙을 재정의할 수 있습니다.

기본 규칙은 다음과 같습니다.

**인바운드**

| 이름 | 우선 순위 | 원본 IP | 원본 포트 | 대상 IP | 대상 포트 | 프로토콜 | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| VNET 인바운드 허용 | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | 허용 |
| AZURE 부하 분산 장치 인바운드 허용 | 65001 | AZURE\_LOADBALANCER | * | * | * | * | 허용 |
| 모든 인바운드 거부 | 65500 | * | * | * | * | * | 거부 |

**아웃바운드**

| 이름 | 우선 순위 | 원본 IP | 원본 포트 | 대상 IP | 대상 포트 | 프로토콜 | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| VNET 아웃바운드 허용 | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | 허용 |
| 인터넷 아웃바운드 허용 | 65001 | * | * | 인터넷 | * | * | 허용 |
| 모든 아웃바운드 거부 | 65500 | * | * | * | * | * | 거부 |

### 특별 인프라 규칙

NSG 규칙은 명시적입니다. NSG 규칙에서 지정한 트래픽 이외의 트래픽은 허용되거나 거부되지 않습니다. 그러나 네트워크 보안 그룹 사양과 관계없이 항상 허용되는 트래픽 유형이 두 가지 있습니다. 이러한 프로비전은 인프라를 지원하도록 구성되었습니다.

- **호스트 노드의 가상 IP:** DHCP, DNS 및 상태 모니터링과 같은 기본 인프라 서비스는 168.63.129.16의 가상화된 호스트 IP 주소를 통해 제공됩니다. 이 공용 IP 주소는 Microsoft에 속하며, 이 목적을 위해 모든 지역에서 유일하게 사용되는 가상화된 IP 주소입니다. 이 IP 주소는 가상 컴퓨터를 호스트하는 서버 컴퓨터(호스트 노드)의 실제 IP 주소에 매핑합니다. 호스트 노드는 DHCP 릴레이, DNS 재귀 확인자, 부하 분산 장치 상태 검색 및 컴퓨터 상태 검색에 대한 검색 소스 등의 역할을 합니다. 이 IP 주소에 대한 통신은 공격으로 간주되지 않아야 합니다.

- **라이선싱(키 관리 서비스):** 가상 컴퓨터에서 실행되는 Windows 이미지는 사용이 허가되어 있어야 합니다. 사용 허가를 위해 라이선싱 요청이 해당 쿼리를 처리하는 키 관리 서비스 호스트 서버로 전송됩니다. 그리고 이 통신은 항상 아웃바운드 포트 1688을 사용합니다.

### 기본 태그

기본 태그는 IP 주소의 범주를 다루기 위해 시스템에서 제공한 식별자입니다. 기본 태그는 고객 정의 규칙에서 지정할 수 있습니다. 다음과 같은 기본 태그가 있습니다.

- **VIRTUAL\_NETWORK** - 이 기본 태그는 모든 네트워크 주소 공간을 표시합니다. 여기에는 연결된 모든 온-프레미스 주소 공간(로컬 네트워크)뿐만 아니라 가상 네트워크 주소 공간(Azure의 IP CIDR)도 포함됩니다. 또한 VNet 간 주소 공간도 포함됩니다.

- **AZURE\_LOADBALANCER** - 이 기본 태그는 Azure의 인프라 부하 분산 장치를 표시합니다. 이 태그는 Azure의 상태 검색이 시작되는 Azure 데이터 센터 IP로 변환됩니다. 이 기본 태그는 NSG와 연결된 VM 또는 VM 집합에서 부하 분산된 집합에 참여하는 경우에만 필요합니다.

- **INTERNET** - 이 기본 태그는 가상 네트워크 외부에 있으며 공용 인터넷에서 연결할 수 있는 IP 주소 공간을 표시합니다. 이 범위에는 Azure 소유의 공용 IP 공간도 포함됩니다.

### 포트 및 포트 범위

네트워크 보안 그룹 규칙은 단일 원본/대상 포트 또는 포트 범위에서 지정할 수 있습니다. 이는 FTP와 같은 응용 프로그램을 위해 다양한 범위의 포트를 열려는 경우에 특히 유용합니다. 범위는 순차적일 수만 있으며, 개별 포트 사양과 혼합할 수 없습니다.

포트의 범위를 지정하려면 아래 *DestinationPortRange* 매개 변수에 나온 대로 '-' 기호를 사용합니다.

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### ICMP 트래픽

현재 NSG 규칙은 'TCP' 또는 'UDP' 프로토콜만 고려합니다. 'ICMP'에 대한 고유 태그는 없습니다. 그러나 ICMP 트래픽은 기본적으로 VNet 내의 모든 포트 및 프로토콜 '*'의 송/수신 트래픽을 허용하는 인바운드 VNet 규칙을 통해 가상 네트워크 내에서 허용됩니다.

## NSG 연결

VM에 NSG 연결 - NSG가 VM에 직접 연결된 경우 NSG의 네트워크 액세스 규칙은 VM에 전송되는 모든 트래픽에 직접 적용됩니다. 규칙 변경을 위해 NSG가 업데이트될 때마다 변경 내용이 몇 분 내에 트래픽 처리에 반영됩니다. NSG가 VM에서 연결이 끊어지면 NSG 이전 상태(어떤 상태이든)로 돌아갑니다. 즉, NSG 사용 시 진행 중인 상태 이전의 시스템 기본값으로 돌아갑니다.

서브넷에 NSG 연결 - NSG가 서브넷에 연결된 경우 NSG의 네트워크 액세스 규칙은 서브넷의 모든 VM에 적용됩니다. NSG의 액세스 규칙이 업데이트될 때마다 변경 내용이 몇 분 내에 서브넷에 있는 모든 가상 컴퓨터에 적용됩니다.

서브넷 및 VM에 NSG 연결 - 하나의 NSG를 VM에 연결하고 다른 NSG를 VM이 있는 서브넷에 연결할 수 있습니다. 이러한 방법을 사용할 수 있으며, 이 경우 VM은 두 계층의 보호를 받을 수 있습니다. 아래 다이어그램에서 나와 있는 대로 인바운드 트래픽의 경우 패킷은 서브넷에서 지정한 액세스 규칙과 VM의 규칙을 차례로 통과하며, 아웃바운드의 경우에는 패킷이 서브넷에서 지정한 규칙을 통과하기 전에 먼저 VM에서 지정한 규칙을 통과합니다.

![NSG ACL](./media/virtual-networks-nsg/figure1.png)

NSG가 VM 또는 서브넷과 연결된 경우 네트워크 액세스 제어 규칙은 매우 명시적이 됩니다. 플랫폼은 특정 포트에 대한 트래픽을 허용하는 어떠한 암시적 규칙도 삽입하지 않습니다. 이 경우에 VM의 끝점을 만들면 인터넷에서의 트래픽을 허용하는 규칙도 만들어야 합니다. 이렇게 하지 않으면 외부에서 VIP:<Port>에 액세스할 수 없습니다.

예: 새 VM을 만들고 새 NSG도 만듭니다. NSG를 VM에 연결합니다. VM은 VNET 인바운드 허용 규칙을 통해 가상 네트워크에 있는 다른 VM과 통신할 수 있습니다. 또한 VM은 인터넷 아웃바운드 허용 규칙을 사용하여 인터넷에 아웃바운드 연결할 수도 있습니다. 나중에 포트 80에 끝점을 만들어 VM에서 실행 중인 웹사이트에 대한 트래픽을 수신합니다. 다음(아래)과 비슷한 규칙을 NSG에 추가할 때까지 인터넷에서 VIP(공용 가상 IP 주소)의 포트 80에 보내는 패킷은 VM에 도달하지 않습니다.

| 이름 | 우선 순위 | 원본 IP | 원본 포트 | 대상 IP | 대상 포트 | 프로토콜 | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| 웹 | 100 | 인터넷 | * | * | 80 | TCP | 허용 |

## 디자인 고려 사항

NSG를 디자인하려면 VM이 어떻게 인프라 서비스와 통신하는지, Azure에서 호스트하는 PaaS 서비스가 어떻게 호스트되는지 이해해야 합니다. SQL 데이터베이스 및 저장소와 같은 대부분의 Azure PaaS 서비스는 공용 연결 인터넷 주소를 통해서만 액세스할 수 있습니다. 부하 분산 프로브 또한 마찬가지입니다.

Azure의 일반적인 시나리오는 이러한 개체가 인터넷 엑세스에 필요한지 여부에 따라 서브넷에 있는 VM 및 PaaS 역할을 분리하는 것입니다. 이같은 시나리오에서는 SQL 데이터베이스 및 저장소처럼 Azure PaaS 서비스에 엑세스해야 하는 VM 또는 역할 인스턴스와 서브넷이 연결될 수도 있습니다.

이 시나리오에서 다음 NSG 규칙을 가정해 보겠습니다.

| 이름 | 우선 순위 | 원본 IP | 원본 포트 | 대상 IP | 대상 포트 | 프로토콜 | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|인터넷 허용 안 함|100| VIRTUAL\_NETWORK|& #42;|인터넷|& #42;|TCP|거부| 

규칙이 가상 네트워크에서의 인터넷 엑세스를 전부 허용하지 않으므로, SQL 데이터베이스처럼 공용 인터넷 끝점이 필요한 Azure PaaS 서비스는 VM이 엑세스할 수 없습니다.

거부 규칙 대신, 아래와 같이 인터넷에서의 가상 네트워크 액세스는 거부하지만 가상 네트워크에서의 인터넷 액세스는 허용하는 규칙을 사용하는 것이 좋습니다.

| 이름 | 우선 순위 | 원본 IP | 원본 포트 | 대상 IP | 대상 포트 | 프로토콜 | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|인터넷으로의 엑세스|100| VIRTUAL\_NETWORK|& #42;|인터넷|& #42;|TCP|허용|
|인터넷에서의 엑세스|110| 인터넷|& #42;|VIRTUAL\_NETWORK|& #42;|TCP|거부| 

>[AZURE.WARNING]Azure는 **게이트웨이** 서브넷이라는 특수 서브넷을 사용하여 다른 VNet 및 온-프레미스 네트워크에 대한 VPN 게이트웨이를 처리합니다. 이 서브넷에 NSG를 연결하면 VPN 게이트웨이가 예상대로 작동이 중지됩니다. 게이트웨이 서브넷에 NSG를 연결하지 마세요.

## 계획 - 네트워크 보안 그룹 워크플로

네트워크 보안 그룹을 사용할 경우 기본 워크플로 단계는 다음과 같습니다.

### 워크플로 - NSG 생성 및 연결

1. NSG(네트워크 보안 그룹)를 만듭니다.

1. 기본 규칙만으로 충분하지 않으면 네트워크 보안 규칙을 추가합니다.

1. NSG를 VM에 연결합니다.

1. VM을 업데이트합니다.

1. 업데이트 후 NSG 규칙이 즉시 적용됩니다.

### 워크플로 - 기존 NSG 업데이트

1. 기존 NSG의 규칙을 추가, 삭제 또는 업데이트합니다.

1. NSG와 연결된 모든 VM이 몇 분 내에 업데이트를 완료합니다. NSG 규칙이 이미 VM과 연결된 경우 VM 업데이트가 필요하지 않습니다.

### 워크플로 - NSG 연결 변경

1. 이미 다른 NSG에 연결된 VM에 새 NSG를 연결합니다.

1. VM을 업데이트합니다.

1. 새 NSG의 규칙이 몇 분 이내에 적용됩니다.

## 네트워크 보안 그룹을 생성, 구성 및 관리하는 방법

현재 NSG는 PowerShell cmdlet 및 REST API를 사용해서만 구성 및 수정할 수 있습니다. 관리 포털을 사용하여 NSG를 구성할 수는 없습니다. 아래 PowerShell cmdlet을 사용하면 NSG를 생성, 구성 및 관리하는 데 도움이 됩니다.

**네트워크 보안 그룹 만들기**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**규칙 추가 또는 업데이트**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**NSG에서 규칙 삭제**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**VM에 NSG 연결**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**VM에 연결된 NSG 보기**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**VM에서 NSG 제거**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**서브넷에 NSG 연결**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**서브넷에 연결된 NSG 보기**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**서브넷에서 NSG 제거**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**NSG 삭제**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**규칙과 함께 NSG 세부 정보 가져오기**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**NSG에 관련된 모든 Azure PowerShell cmdlet 보기**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=August15_HO9-->