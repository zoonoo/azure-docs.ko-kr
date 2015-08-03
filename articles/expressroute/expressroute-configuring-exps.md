<properties
   pageTitle="Exchange 공급자를 통해 Express 경로 구성"
   description="이 자습서에서는 exchange 공급자(EXPs)를 통한 ExpressRoute 설정 과정을 안내 합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Exchange 공급자를 통해 Express 경로 연결 구성

Exchange 공급자를 통해 ExpressRoute 연결을 구성하려면 올바른 순서대로 여러 단계를 완료해야 합니다. 이러한 지침은 다음을 수행하는데 도움이 됩니다.

- ExpressRoute 회로 만들기 및 관리
- ExpressRoute 회로에 대한 BGP 피어링 구성
- 가상 네트워크를 ExpressRoute 회로에 연결

##  필수 구성 요소


구성을 시작하기 전에 다음 전제 조건을 충족하는지 확인합니다.

- Azure 구독
- 최신 버전의 Azure PowerShell
- 다음 가상 네트워크 요구 사항:
	- Azure의 가상 네트워크에 사용할 IP 주소 접두사의 집합
	- IP 접두사 온-프레미스 집합(공용 IP 주소를 포함할 수 있음)
	- 가상 네트워크 게이트웨이는 /28 서브넷으로 생성되어야 합니다.
	- 가상 네트워크 외부에 있는 IP 접두사의 추가 집합(/28)입니다. BGP 피어링을 구성하는 데 사용됩니다.
	- 네트워크에 대한 AS 숫자. AS 번호에 대한 자세한 내용은 [AS(자치 시스템) 번호](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)를 참조하세요.
	- 인증된 BGP 세션이 필요한 경우 MD5 해시
	- 트래픽을 보낼 수 있는 VLAN ID입니다. 각 회로 대한 2개의 VLAN ID가 필요합니다. 가상 네트워크에 대해 하나, 공용 IP 주소에서 호스팅된 서비스에 대해 하나가 필요합니다.
	- 사용자 네트워크에 대한 [AS(자치 시스템) 번호](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)입니다.
	- 두개의 1Gbps / 10Gbps는 Exchange 공급자의 이더넷 교환에 교차 연결합니다.
	- 라우팅용으로 BGP를 지원할 수 있는 라우터 쌍

##  배포 워크플로


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  PowerShell을 사용하여 설정 구성

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어하고 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)의 PowerShell 설명서를 참조하세요.

1. **ExpressRoute에 대한 PowerShell 모듈을 가져옵니다.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **공급자, 위치 및 지원되는 대역폭의 목록을 가져옵니다.**

	회로를 만들기 전에 각 위치에 대한 서비스 공급자, 지원되는 위치 및 대역폭 옵션 목록이 필요합니다. 다음 PowerShell cmdlet는 이후 단계에서 사용할 이 정보를 반환합니다.

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**


		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths
		----                 -------------------------      --------------------------
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		                     as,New York,Seattle,Silicon
		                     Valley,Washington
		                     DC,London,Hong
		                     Kong,Singapore,Sydney,Tokyo
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		Exchange
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		IPVPN
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		International
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000

3. **전용 회로 만들기**

	아래 예제에서는 Equinix 실리콘밸리를 통해 200Mbps ExpressRoute 회로를 만드는 방법을 보여줍니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다.

	다음은 새 서비스 키에 대한 예제 요청입니다.

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location

		#Getting service key
		Get-AzureDedicatedCircuit

	응답은 아래 예제와 비슷합니다.

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	언제든지 Get-AzureCircuit cmdlet을 사용 하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 ServiceKey 필드에 표시됩니다.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Exchange 공급자에게 서비스 키를 보냅니다.**

	Exchange 공급자는 서비스 키를 사용하여 연결 종료를 설정합니다. 연결 공급자에게 연결을 설정하기 위한 추가 정보를 제공해야 합니다.

5. **주기적으로 회로 키의 상태 및 상태를 확인합니다.**

	공급자가 회로를 사용하도록 설정하는 시기를 알 수 있습니다. 회로를 사용하도록 설정하면아래 예에서와 같이 *ServiceProviderProvisioningState*가 *Provisioned*로 표시됩니다.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

6. **가상 네트워크에 대한 라우팅을 구성합니다.**

	BGP 세션을 사용하여 라우팅을 교환하고 고가용성이 있는지도 확인합니다. 아래 예제를 사용하여 회로에 대해 BGP 세션을 만듭니다. 세션을 만들 때 사용자 고유의 값으로 대체합니다.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	서비스 키 제공으로 Get-AzureBGPPeering을 사용하여 회로에 대한 라우팅 정보를 얻을 수 있습니다. Set-AzureBGPPeering을 사용하여 BGP 설정을 업데이트할 수도 있습니다. 이 명령을 실행하는 경우 BGP 세션 상태가 되지 않습니다. BGP 세션을 표시하려면 하나 이상의 VNet에 회로가 연결되어야 합니다.

	아래 응답은 다음 단계에 필요한 정보를 제공합니다. 피어 ASN을 사용하여 라우터의 VRF에 BGP를 구성합니다.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **공용 IP 주소에 호스팅된 서비스에 대한 라우팅을 구성합니다.**

	BGP 세션을 사용하여 라우팅을 교환하고 고가용성이 있는지도 확인합니다. 아래 예제를 사용하여 회로에 대해 BGP 세션을 만듭니다. 세션을 만들 때 사용자 고유의 값으로 대체합니다.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public

	서비스 키 제공으로 Get-AzureBGPPeering을 사용하여 회로에 대한 라우팅 정보를 얻을 수 있습니다. Set-AzureBGPPeering을 사용하여 BGP 설정을 업데이트할 수도 있습니다. 이 명령을 실행하는 경우 BGP 세션 상태가 되지 않습니다. BGP 세션을 표시하려면 하나 이상의 VNet에 회로가 연결되어야 합니다.

	아래 응답은 다음 단계에 필요한 정보를 제공합니다. 피어 ASN을 사용하여 라우터의 VRF에 BGP를 구성합니다.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101

8. **가상 네트워크와 게이트웨이를 구성합니다.**

	[ExpressRoute에 대한 가상 네트워크 및 게이트웨이 구성](https://msdn.microsoft.com/library/azure/dn643737.aspx)을.참조하세요. 게이트웨이 서브넷이 ExpressRoute 연결을 사용하려면 /28이어야 합니다.

9. **네트워크를 회로에 연결합니다.** 다음과 같은 상황 및 상태로 회로가 이동하는 지 확인한 후에만 다음 지침을 계속 수행합니다.
	- ServiceProviderProvisioningState: Provisioned
	- 상태: Enabled

			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
 

<!---HONumber=July15_HO4-->