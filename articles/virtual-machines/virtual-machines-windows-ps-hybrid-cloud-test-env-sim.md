<properties 
	pageTitle="시뮬레이션된 하이브리드 클라우드 테스트 환경 | Microsoft Azure" 
	description="Azure 가상 네트워크 두 개와 VNet 간 연결을 사용하여 IT 전문가 또는 개발 테스트용 시뮬레이션된 하이브리드 클라우드 환경을 만들어봅니다." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/08/2016" 
	ms.author="josephd"/>

# 테스트용 시뮬레이션된 하이브리드 클라우드 환경 설정

이 문서에서는 두 개의 Azure 가상 네트워크를 사용하여 Microsoft Azure에서 시뮬레이션된 하이브리드 클라우드 환경을 만드는 과정을 안내합니다. 다음은 결과 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

하이브리드 클라우드 프로덕션 환경을 시뮬레이션하고 다음으로 구성됩니다.

- Azure 가상 네트워크(TestLab 가상 네트워크)에서 호스트되는 시뮬레이션 및 간소화된 온-프레미스 네트워크
- Azure에서 호스트되는 시뮬레이션된 크로스-프레미스 가상 네트워크(TestVNET)
- 두 가상 네트워크의 VNet 간 연결
- TestVNET 가상 네트워크의 보조 도메인 컨트롤러

이 구성은 다음 작업을 수행할 수 있는 기초 및 일반적인 시작 지점을 제공합니다.

- 시뮬레이션된 하이브리드 클라우드 환경에서 응용 프로그램 개발 및 테스트
- 일부는 TestLab 가상 네트워크 내에 있고 일부는 TestVNET 가상 네트워크에 있는 컴퓨터의 테스트 구성을 만들어 하이브리드 클라우드 기반 IT 작업을 시뮬레이션합니다.

이 하이브리드 클라우드 테스트 환경을 설정하는 네 가지 주요 단계가 있습니다.

1.	TestLab 가상 네트워크 구성
2.	크로스-프레미스 가상 네트워크 만들기
3.	VNet 간 VPN 연결 만들기
4.	DC2 구성

이 구성에는 Azure 구독이 필요합니다. MSDN 또는 Visual Studio 구독이 있는 경우에는 [Visual Studio 구독자를 위한 월간 Azure 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 참조하세요.

>[AZURE.NOTE] Azure의 가상 컴퓨터 및 가상 네트워크 게이트웨이는 실행 중인 동안 지속적인 비용이 부과됩니다. 이 비용은 MSDN 또는 유료 구독에 대해 청구됩니다. Azure VPN 게이트웨이는 두 개의 Azure 가상 컴퓨터 집합으로 구현됩니다. 비용을 최소화하려면 테스트 환경을 만들고 가능한 한 신속하게 필요한 테스트 및 데모를 수행합니다.

## 1단계: TestLab 가상 네트워크 구성

[기본 구성 테스트 환경](https://technet.microsoft.com/library/mt771177.aspx) 항목에 설명된 지침을 사용하여 TestLab이라는 Azure 가상 네트워크에서 DC1, APP1 및 CLIENT1 컴퓨터를 구성합니다.

다음으로 Azure PowerShell 프롬프트를 시작합니다.

> [AZURE.NOTE] 다음 명령 집합은 Azure PowerShell 1.0 이상을 사용합니다.

계정에 로그인합니다.

	Login-AzureRMAccount

다음 명령을 사용하여 구독 이름을 가져옵니다.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Azure 구독을 설정합니다. 1단계에서 기본 구성을 빌드하는 데 사용했던 동일한 구독을 사용합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

그런 다음 Azure 게이트웨이를 호스트하는 데 사용할 기본 구성의 TestLab 가상 네트워크에 게이트웨이 서브넷을 추가합니다.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

그런 다음 TestLab 가상 네트워크의 게이트웨이에 할당할 공용 IP 주소를 요청합니다.

	$gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

그런 다음 게이트웨이를 만듭니다.

	$vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
	New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

새 게이트웨이를 만드는 데 20분 이상 걸릴 수 있습니다.

로컬 컴퓨터의 Azure 포털에서 CORP\\User1 자격 증명을 사용하여 DC1에 연결합니다. 컴퓨터 및 사용자가 해당 로컬 도메인 컨트롤러를 사용하여 인증할 수 있도록 CORP 도메인을 구성하려면 DC1에서 관리자 수준 Windows PowerShell 명령 프롬프트의 다음 명령을 실행합니다.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## 2단계: TestVNET 가상 네트워크 만들기

먼저 TestVNET 가상 네트워크를 만들고 네트워크 보안 그룹으로 보호합니다.

	$rgName="<name of the resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
	$testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
	$gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

그런 다음 TestVNET 가상 네트워크의 게이트웨이에 할당할 공용 IP 주소를 요청하고 게이트웨이를 만듭니다.

	$gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
	New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##3단계: VNet 간 연결 만들기

먼저 네트워크 또는 보안 관리자에서 임의로 암호화된 강력한 32자의 미리 공유한 키를 얻습니다. 또는 [IPsec 미리 공유한 키에 대한 임의 문자열 만들기](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx)에서 정보를 사용하여 미리 공유한 키를 얻습니다.

그리고 나서 다음 명령을 사용하여 VNet 간 VPN 연결을 만듭니다. 이를 완료하려면 다소 시간이 걸릴 수 있습니다.

	$sharedKey="<pre-shared key value>"
	$gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
	$gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
	New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
	New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

잠시 후에 연결이 설정됩니다.

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## 4단계: DC2 구성

먼저 DC2에 가상 컴퓨터를 만듭니다. 로컬 컴퓨터의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<the storage account name for the base configuration>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

다음으로 Azure 포털에서 새 DC2 가상 컴퓨터에 연결합니다.

그런 다음 기본 연결 테스트에 대한 트래픽을 허용하도록 Windows 방화벽 규칙을 구성합니다. DC2의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

Ping 명령을 실행한 경우 IP 주소 10.0.0.4에서 성공적인 회신 4개가 수신되어야 합니다. 이는 VNet 간 연결의 트래픽에 대한 테스트입니다.

다음으로 DC2에서 추가 데이터 디스크를 드라이브 문자가 F:인 새 볼륨으로 추가합니다.

1.	Server Manager의 왼쪽 창에서 **파일 및 저장소 서비스**를 클릭한 다음 **디스크**를 클릭합니다.
2.	내용 창의 **디스크** 그룹에서 **디스크 2**(**파티션**이 **알 수 없음**으로 설정됨)를 클릭합니다.
3.	**작업**을 클릭한 후 **새 볼륨**을 클릭합니다.
4.	새 볼륨 마법사의 시작하기 전에 페이지에서 **다음**을 클릭합니다.
5.	서버 및 디스크 선택 페이지에서 **디스크 2**를 클릭하고 **다음**을 클릭합니다. 메시지가 표시되면 **확인**을 클릭합니다.
6.	볼륨 크기를 선택합니다 페이지에서 **다음**을 클릭합니다.
7.	드라이브 문자 또는 폴더에 할당 페이지에서 **다음**을 클릭합니다.
8.	파일 시스템 설정 선택 페이지에서 **다음**을 클릭합니다.
9.	선택 확인 페이지에서 **만들기**를 클릭합니다.
10.	완료되면 **닫기**를 클릭합니다.

그런 다음 DC2를 corp.contoso.com 도메인의 복제본 도메인 컨트롤러로 구성합니다. DC2의 관리자 수준 Windows PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

CORP\\User1 암호와 DSRM(디렉터리 서비스 복원 모드) 암호를 둘 다 제공하고 DC2를 다시 시작하라는 메시지가 표시됩니다.

TestVNET 가상 네트워크에는 고유한 DNS 서버(DC2)가 있으므로 이 DNS 서버를 사용하도록 TestVNET 가상 네트워크를 구성해야 합니다.

1.	Azure 포털의 왼쪽 창에서 가상 네트워크 아이콘을 클릭한 다음 **TestVNET**을 클릭합니다.
2.	**설정** 탭에서 **DNS 서버**를 클릭합니다.
3.	**주 DNS 서버**에서 10.0.0.4를 **192.168.0.4**로 바꿔 입력합니다.
4.	**Save**를 클릭합니다.

다음은 현재 구성입니다.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
이제 시뮬레이션된 하이브리드 클라우드 환경을 테스트할 준비가 완료되었습니다.

## 다음 단계

- 이 환경에서 [웹 기반 LOB(기간 업무) 응용 프로그램](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md)을 설정합니다.

<!---HONumber=AcomDC_0810_2016-->