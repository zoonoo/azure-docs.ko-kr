<properties
	pageTitle="SharePoint Server 2013 팜 1단계 | Microsoft Azure"
	description="Azure의 SharePoint Server 2013 팜 1단계에서 가상 네트워크 및 기타 Azure 인프라 요소를 만듭니다."
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
	ms.date="12/11/2015"
	ms.author="josephd"/>

# SharePoint 인트라넷 팜 워크로드 1단계: Azure 구성

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

Azure 인프라 서비스의 SQL Server AlwaysOn 가용성 그룹을 사용하여 인트라넷 전용 SharePoint 2013 팜을 배포하는 이 단계에서는 Azure 서비스 관리에서 Azure 네트워킹 및 저장소 인프라를 구축합니다. [2단계](virtual-machines-workload-intranet-sharepoint-phase2.md)로 진행하기 전에 이 단계를 완료해야 합니다. 전체 단계를 보려면 [Azure에서 SQL Server AlwaysOn 가용성 그룹을 사용하여 SharePoint 배포](virtual-machines-workload-intranet-sharepoint-overview.md)를 참조하세요.

다음과 같은 기본 네트워크 구성 요소를 Azure에 프로비전해야 합니다.

- Azure 가상 컴퓨터 호스팅을 위한 하나의 서브넷이 포함된 크로스-프레미스 가상 네트워크
- VHD 디스크 이미지 및 추가 데이터 디스크를 저장할 Azure 저장소 계정 하나
- 가용성 집합 4개

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
6\. | 첫 번째 최종 DNS 서버 | 가상 네트워크 서브넷의 주소 공간에 사용 가능한 네 번째 IP 주소입니다(표 S 참조). 이러한 주소는 IT 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | 두 번째 최종 DNS 서버 | 가상 네트워크 서브넷의 주소 공간에 사용 가능한 다섯 번째 IP 주소입니다(표 S 참조). 이러한 주소는 IT 부서에서 확인할 수 있습니다. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | IPsec 공유 키 | 사이트 간 VPN 연결의 양 측을 인증하는 데 사용되는 임의의 32자 영숫자 문자열입니다. 이 키 값은 IT 또는 보안 부서에서 확인할 수 있습니다. 또는 [IPsec 미리 공유한 키에 대한 임의 문자열 만들기](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx)를 참조하세요.| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

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

> [AZURE.NOTE]이 미리 정의된 아키텍처에서는 간편한 작업을 위해 단일 서브넷을 사용합니다. 서브넷 격리를 에뮬레이트하기 위해 트래픽 필터 집합을 오버레이하려는 경우에는 Azure [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md)을 사용할 수 있습니다.

가상 네트워크에서 도메인 컨트롤러를 처음 설정할 때 사용하려는 두 온-프레미스 DNS 서버의 정보를 표 D에 입력합니다. 아래에는 빈 칸이 두 개 있지만 더 추가할 수 있습니다. 추가 항목은 IT 부서에서 확인할 수 있습니다.

항목 | DNS 서버 IP 주소
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**표 D: 온-프레미스 DNS 서버**

사이트 간 VPN 연결을 통해 크로스-프레미스 네트워크에서 조직 네트워크로 패킷을 라우팅하려면 조직 온-프레미스 네트워크에서 연결 가능한 모든 위치에 대해 CIDR 표기법으로 작성된 주소 공간 목록을 포함하는 로컬 네트워크를 사용하여 가상 네트워크를 구성해야 합니다. 로컬 네트워크를 정의하는 주소 공간 목록은 고유해야 하며, 다른 가상 네트워크나 다른 로컬 네트워크에 사용되는 주소 공간과 겹쳐서는 안 됩니다.

로컬 네트워크 주소 공간 집합의 정보는 표 L에 입력합니다. 아래에는 빈 칸이 3개 있지만 일반적으로는 항목을 더 추가해야 합니다. 이 주소 공간 목록은 IT 부서에서 확인할 수 있습니다.

항목 | 로컬 네트워크 주소 공간
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**표 L: 로컬 네트워크의 주소 접두사**

> [AZURE.NOTE]다음 명령 집합은 Azure PowerShell 1.0 이상을 사용합니다. 자세한 내용은 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)을 참조하세요.

먼저 Azure PowerShell 프롬프트를 시작하고 계정에 로그인합니다.

	Login-AzureRMAccount

다음 명령을 사용하여 구독 이름을 가져옵니다.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Azure 구독을 설정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

다음으로, 인트라넷 SharePoint 팜에 대한 새 리소스 그룹을 만듭니다. 고유한 리소스 그룹 이름을 확인하려면 다음 명령을 사용하여 기존 리소스 그룹을 나열합니다.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

이러한 명령을 사용하여 새 리소스 그룹을 만듭니다.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

리소스 관리자 기반 가상 컴퓨터를 사용하려면 리소스 관리자 기반 저장소 계정이 필요합니다.

항목 | 저장소 계정 이름 | 목적 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | 워크로드의 모든 가상 컴퓨터에서 사용되는 표준 저장소 계정입니다. 

**ST 테이블: 저장소 계정**

2, 3, 4단계에서 가상 컴퓨터를 만들 때 이 이름이 필요합니다.

소문자와 숫자만 포함하는 각 저장소 계정에 대해서 전역적으로 고유한 이름을 선택해야 합니다. 이 명령을 사용하여 기존 저장소 계정을 나열할 수 있습니다.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

저장소 계정을 만들려면 이러한 명령을 실행합니다.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

다음으로, 인트라넷 SharePoint 팜을 호스트할 Azure 가상 네트워크를 만듭니다.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$spSubnetName="<Table S – Item 2 – Subnet name column>"
	$spSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$spSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $spSubnetName -AddressPrefix $spSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$spSubnet -DNSServer $dnsServers

다음으로 이러한 명령을 사용하여 사이트 간 VPN 연결을 위한 게이트웨이를 만듭니다.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="SPPublicIPAddress"
	$vnetGatewayIpConfigName="SPPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="SPAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="SPLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="SPS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

다음으로, Azure VPN 게이트웨이 연결을 위해 온-프레미스 VPN 장치를 구성합니다. 자세한 내용은 [VPN 장치 구성](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device)을 참조하세요.

온-프레미스 VPN 장치를 구성하려면 다음이 필요합니다.

- 가상 네트워크용 Azure VPN 게이트웨이의 공용 IPv4 주소(**Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName** 명령의 표시에 있음)
- 사이트 간 VPN 연결용 IPsec 미리 공유한 키(표 V - 항목 8 - 값 열)

그런 다음 온-프레미스 네트워크에서 가상 네트워크의 주소 공간에 연결할 수 있는지 확인합니다. 이를 위해 일반적으로는 가상 네트워크 주소 공간에 해당하는 경로를 VPN 장치에 추가한 다음 조직 네트워크의 나머지 라우팅 인프라에 해당 경로를 보급합니다. 이 작업을 수행하는 방법은 IT 부서에서 확인할 수 있습니다.

다음으로 가용성 집합 4개의 이름을 정의합니다. 표 A에 해당 정보를 입력합니다.

항목 | 목적 | 가용성 집합 이름 
--- | --- | --- 
1\. | 도메인 컨트롤러 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | 앱 서버 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | 웹 서버 | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**표 A: 가용성 집합 이름**

2, 3, 4단계에서 가상 컴퓨터를 만들 때 이러한 이름이 필요합니다.

이러한 Azure PowerShell 명령을 사용하여 새 가용성 집합을 만듭니다.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 4 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

이 단계를 올바르게 완료하면 해당 구성이 생성됩니다.

![](./media/virtual-machines-workload-intranet-sharepoint-phase1/workload-spsqlao_01.png)

## 다음 단계

- [2단계](virtual-machines-workload-intranet-sharepoint-phase2.md)를 사용하여 이 워크로드의 구성을 계속합니다.

<!---HONumber=AcomDC_1217_2015-->