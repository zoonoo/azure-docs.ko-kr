<properties 
	pageTitle="LOB(기간 업무) 응용 프로그램 1단계 | Microsoft Azure" 
	description="Azure의 LOB(기간 업무) 응용 프로그램 1단계에서 가상 네트워크 및 기타 Azure 인프라 요소를 만듭니다." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# LOB(기간 업무) 응용 프로그램 워크로드 1단계: Azure 구성
 
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 리소스 관리자 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.
 
이 인트라넷 전용 Azure 인프라 서비스의 고가용성 LOB(기간 업무) 응용 프로그램을 배포하는 단계에서는 Azure 네트워킹 및 저장소 인프라를 구축합니다. [2단계](virtual-machines-workload-high-availability-LOB-application-phase2.md)로 진행하기 전에 이 단계를 완료해야 합니다. 모든 단계는 [Azure에서 고가용성 LOB(기간 업무) 응용 프로그램 배포](virtual-machines-workload-high-availability-LOB-application-overview.md)를 참조하세요.

다음과 같은 기본 네트워크 구성 요소를 Azure에 프로비전해야 합니다.

- Azure 가상 컴퓨터 호스팅을 위한 하나의 서브넷이 포함된 크로스-프레미스 가상 네트워크
- VHD 디스크 이미지 및 추가 데이터 디스크를 저장하기 위한 Azure 저장소 계정 2개
- 가용성 집합 3개

## 시작하기 전에

Azure 구성 요소 구성을 시작하기 전에 다음 표에 필요한 정보를 입력합니다. Azure 구성 절차를 쉽게 진행하려면 이 섹션을 인쇄한 다음 필요한 정보를 기록하거나 이 섹션을 문서에 복사한 후에 정보를 입력합니다.

가상 네트워크(VNet)의 설정은 표 V에 입력합니다.

항목 | 구성 요소 | 설명 | 값 
--- | --- | --- | --- 
1\. | VNet 이름 | Azure 가상 네트워크에 할당할 이름입니다(예: SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | VNet 위치 | 가상 네트워크를 포함할 Azure 데이터 센터입니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | 로컬 네트워크 이름 | 조직 네트워크에 할당할 이름입니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | VPN 장치 IP 주소 | 인터넷에서 사용할 VPN 장치 인터페이스의 공용 IPv4 주소입니다. 이 주소는 IT 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | VNet 주소 공간 | 단일 개인 주소 접두사로 정의되는 가상 네트워크의 주소 공간입니다. 이 주소 공간은 IT 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | 가상 네트워크에 대한 첫 번째 DNS 서버 | 가상 네트워크 서브넷의 주소 공간에 사용 가능한 네 번째 IP 주소(표 S 참조). 이 주소는 IT 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | 가상 네트워크에 대한 두 번째 DNS 서버 | 가상 네트워크의 두 번째 서브넷 주소 공간에 사용 가능한 다섯 번째 IP 주소(표 S 참조). 이 주소는 IT 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | IPsec 공유 키 | 사이트 간 VPM 연결의 양 측을 인증하는 데 사용되는 임의의 128자 영숫자 문자열입니다. 이 키 값은 IT 또는 보안 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_


**표 V: 크로스-프레미스 가상 네트워크 구성**

이 솔루션의 서브넷 관련 정보는 표 S에 입력합니다.

- 첫 번째 서브넷에 대해 Azure 게이트웨이 서브넷용 29비트 주소 공간을 결정합니다(/29 접두사 길이 포함).
- 두 번째 서브넷에 대해 식별 이름, 가상 네트워크 주소 공간 기반 단일 IP 주소 공간, 용도에 대한 설명을 지정합니다. 

가상 네트워크 주소 공간의 이 주소 공간은 IT 부서에서 확인할 수 있습니다. 두 주소 공간은 모두 네트워크 접두사 형식이라고도 하는 CIDR(Classless Interdomain Routing) 형식이어야 합니다. 예를 들면 10.24.64.0/20과 같습니다.

항목 | 서브넷 이름 | 서브넷 주소 공간 | 목적 
--- | --- | --- | --- 
1\. | 게이트웨이 서브넷 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Azure 게이트웨이 가상 컴퓨터에서 사용되는 서브넷입니다.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**표 S: 가상 네트워크의 서브넷**

> [AZURE.NOTE]이 미리 정의된 아키텍처에서는 간편한 작업을 위해 단일 서브넷을 사용합니다. 서브넷 격리를 에뮬레이트하기 위해 트래픽 필터 집합을 오버레이하려는 경우에는 Azure [네트워크 보안 그룹](virtual-networks-nsg.md)을 사용할 수 있습니다.

가상 네트워크에서 도메인 컨트롤러를 처음 설정할 때 사용하려는 두 온-프레미스 DNS 서버의 정보를 표 D에 입력합니다. 각 DNS 서버에는 이름과 단일 IP 주소를 지정합니다. 이 이름은 DNS 서버의 호스트 이름 또는 컴퓨터 이름과 일치하지 않아도 됩니다. 아래에는 빈 칸이 두 개 있지만 항목을 더 추가할 수 있습니다. 추가 항목은 IT 부서에서 확인할 수 있습니다.

항목 | DNS 서버 이름 | DNS 서버 IP 주소 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**표 D: 온-프레미스 DNS 서버**

사이트 간 VPN 연결을 통해 Azure 가상 네트워크에서 조직 네트워크로 패킷을 라우팅하려면 조직 온-프레미스 네트워크에서 연결 가능한 모든 위치에 대해 CIDR 표기법으로 작성된 주소 공간 목록을 포함하는 로컬 네트워크를 사용하여 가상 네트워크를 구성해야 합니다. 로컬 네트워크를 정의하는 주소 공간 목록은 고유해야 하며, 다른 가상 네트워크나 다른 로컬 네트워크에 사용되는 주소 공간과 겹쳐서는 안 됩니다.

로컬 네트워크 주소 공간 집합의 정보는 표 L에 입력합니다. 아래에는 빈 칸이 3개 있지만 일반적으로는 항목을 더 추가해야 합니다. 이 주소 공간 목록은 IT 부서에서 확인할 수 있습니다.

항목 | 로컬 네트워크 주소 공간 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**표 L: 로컬 네트워크의 주소 접두사**

다음으로 Azure PowerShell 버전 0.9.5 이상을 설치해야 합니다. Azure PowerShell의 버전을 확인하려면 이 명령을 실행합니다.

	Get-Module azure | format-table version

최신 버전의 Azure PowerShell을 설치해야 하는 경우 **제어판 - 프로그램 및 기능**을 사용하여 현재 버전을 제거합니다. 그런 다음 [Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)의 지침을 사용하여 로컬 컴퓨터에 Azure PowerShell을 설치합니다. Azure PowerShell 프롬프트를 엽니다.

먼저 다음 명령을 사용하여 올바른 Azure 구독을 선택합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

**Get-AzureSubscription** 명령 출력의 **SubscriptionName** 속성에서 구독 이름을 가져올 수 있습니다.

이제 이 명령을 사용하여 Azure PowerShell을 리소스 관리자 모드로 전환합니다.

	Switch-AzureMode AzureResourceManager 

다음으로 LOB(기간 업무) 응용 프로그램에 대한 새 리소스 그룹을 만듭니다.

고유한 리소스 그룹 이름을 확인하려면 다음 명령을 사용하여 기존 리소스 그룹을 나열합니다.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

리소스 관리자 기반 가상 컴퓨터를 만들 수 있는 Azure 위치를 나열하려면 다음 명령을 사용합니다.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

이러한 명령을 사용하여 새 리소스 그룹을 만듭니다.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 관리자 기반 저장소 계정이 필요합니다.

항목 | 저장소 계정 이름 | 목적 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | SQL server 가상 컴퓨터에서 사용되는 프리미엄 저장소 계정입니다.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | 워크로드의 모든 다른 가상 컴퓨터에서 사용되는 표준 저장소 계정입니다. 

**ST 테이블: 저장소 계정**

2, 3, 4단계에서 가상 컴퓨터를 만들 때 이러한 이름이 필요합니다.

소문자와 숫자만 포함하는 각 저장소 계정에 대해서 전역적으로 고유한 이름을 선택해야 합니다. 이 명령을 사용하여 기존 저장소 계정을 나열할 수 있습니다.

	Get-AzureStorageAccount | Sort Name | Select Name

선택한 저장소 계정 이름이 전역적으로 고유한지 여부를 테스트하려면 PowerShell의 Azure 서비스 관리 모드에서 **Test-AzureName** 명령을 실행해야 합니다. 다음 명령을 사용합니다.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Test-AzureName 명령에서 **False**를 표시하는 경우 제안된 이름이 고유한 것입니다. 두 저장소 계정에 대해 고유한 이름을 확인한 경우 ST 테이블을 업데이트하고 다음 명령을 사용하여 Azure PowerShell을 다시 리소스 관리자 모드로 전환합니다.

	Switch-AzureMode AzureResourceManager 

첫 번째 저장소 계정을 만들려면 다음이 명령을 실행합니다.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

두 번째 저장소 계정을 만들려면 다음이 명령을 실행합니다.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

다음으로 LOB(기간 업무) 응용 프로그램을 호스팅할 Azure 가상 네트워크를 만듭니다.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

다음으로 이러한 명령을 사용하여 사이트 간 VPN 연결을 위한 게이트웨이를 만듭니다.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

다음으로, Azure VPN 게이트웨이 연결을 위해 온-프레미스 VPN 장치를 구성합니다. 자세한 내용은 [VPN 장치 구성](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device)을 참조하세요.

온-프레미스 VPN 장치를 구성하려면 다음이 필요합니다.

- 가상 네트워크용 Azure VPN 게이트웨이의 공용 IPv4 주소(**Get-AzurePublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName** 명령의 표시에 있음)
- 사이트 간 VPN 연결용 IPsec 사전 공유 키(테이블 V - 항목 8 - 값 열)

그런 다음 온-프레미스 네트워크에서 가상 네트워크의 주소 공간에 연결할 수 있는지 확인합니다. 이를 위해 일반적으로는 가상 네트워크 주소 공간에 해당하는 경로를 VPN 장치에 추가한 다음 조직 네트워크의 나머지 라우팅 인프라에 해당 경로를 보급합니다. 이 작업을 수행하는 방법은 IT 부서에서 확인할 수 있습니다.

다음으로 가용성 집합 3개의 이름을 정의합니다. 표 A에 해당 정보를 입력합니다.

항목 | 목적 | 가용성 집합 이름 
--- | --- | --- 
1\. | 도메인 컨트롤러 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | 웹 서버 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**표 A: 가용성 집합 이름**

2, 3, 4단계에서 가상 컴퓨터를 만들 때 이러한 이름이 필요합니다.

이러한 Azure PowerShell 명령을 사용하여 새 가용성 집합을 만듭니다.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

이 단계를 올바르게 완료하면 해당 구성이 생성됩니다.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## 다음 단계

이 워크로드를 계속 구성하려면 [2단계: 도메인 컨트롤러 구성](virtual-machines-workload-high-availability-LOB-application-phase2.md)으로 진행하세요.

## 추가 리소스

[Azure에서 고가용성 LOB(기간 업무) 응용 프로그램 배포](virtual-machines-workload-high-availability-LOB-application-overview.md)

[LOB(기간 업무) 응용 프로그램 아키텍처 청사진](http://msdn.microsoft.com/dn630664)

[테스트를 위한 하이브리드 클라우드에서 웹 기반 LOB 응용 프로그램 설정](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Azure 인프라 서비스 구현 지침](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure 인프라 서비스 워크로드: SharePoint Server 2013 팜](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Sept15_HO3-->