<properties 
   pageTitle="Nsp를 사용하여 Express 경로 구성"
   description="이 자습서에서는 NSPs를 통한 Express 경로 설정 과정을 안내합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/29/2015"
   ms.author="cherylmc"/>

#  네트워크 서비스 공급자를 통해 Express 경로 연결 구성

네트워크 서비스 공급자를 통해 Express 경로 연결을 구성하려면 올바른 순서대로 여러 단계를 완료해야 합니다. 이러한 지침은 다음을 수행하는데 도움이 됩니다.

- Express 경로 회로 만들기 및 관리
- 가상 네트워크를 ExpressRoute 회로에 연결

##  필수 구성 요소


구성을 시작하기 전에 다음 전제 조건을 충족하는지 확인합니다.

- Azure 구독
- 최신 버전의 Windows PowerShell
- 다음 가상 네트워크 요구 사항:
	- Azure의 가상 네트워크에 사용할 IP 주소 접두사의 집합.
	- IP 접두사 온-프레미스 집합(공용 IP 주소를 포함할 수 있음).
	- 가상 네트워크 게이트웨이는 /28 서브넷으로 생성되어야 합니다.
	- 가상 네트워크 외부에 있는 IP 접두사의 추가 집합(/28)입니다. 라우팅을 구성하는데 사용됩니다. 네트워크 서비스 공급자에게 제공해야 합니다.
	- 네트워크에 대한 AS 숫자. 네트워크 서비스 공급자에게 제공해야 합니다. 개인 AS 번호를 사용할 수 있습니다. 작업을 수행하려는 경우 65000 이상이어야 합니다. AS 번호에 대한 자세한 내용은 AS(자치 시스템) 번호를 참조하세요. 

- 네트워크 서비스 공급자에서:
	- Express 경로에서 지원되는 VPN 서비스가 있어야 합니다. 기존 VPN 서비스를 한정하는 경우 네트워크 서비스 공급자에 문의하세요.

##  배포 워크플로

![네트워크 서비스 공급자 워크플로](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  PowerShell을 사용하여 설정 구성
Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어하고 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)의 PowerShell 설명서를 참조하세요.



1. **ExpressRoute에 대한 PowerShell 모듈을 가져옵니다.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **공급자, 위치 및 지원되는 대역폭의 목록을 가져옵니다.**

	회로를 만들기 전에 각 위치에 대한 서비스 공급자, 지원되는 위치 및 대역폭 옵션 목록이 필요합니다. 다음 PowerShell cmdlet는 이후 단계에서 사용할 이 정보를 반환합니다.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

	반환되는 정보는 아래 예와 유사합니다.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider
	
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
		

3. **서비스 키에 대한 요청을 확인하고 네트워크 서비스 공급자에 전달합니다.**

	PowerShell cmdlet를 사용하여 이 요청을 수행합니다. 이 예에서는 AT&T Netbond를 서비스 공급자로 사용하며 Silicon Valley에서 50Mbps Express 경로 회로를 지정합니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다.

	다음은 새 서비스 키에 대한 예제 요청입니다.

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	응답은 아래 예제와 비슷합니다.

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	언제든지 Get-AzureCircuit cmdlet을 사용 하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 ServiceKey 필드에 표시됩니다.

		PS C:\> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	이 단계를 완료한 후에 Azure 저장소 및 기타 서비스에 연결을 해야 합니다.



4. **가상 네트워크와 게이트웨이를 구성합니다.**

	[ExpressRoute에 대한 가상 네트워크 및 게이트웨이 구성](../expressroute/expressroute-configuring-vnet-gateway.md)을.참조하세요. 게이트웨이 서브넷이 ExpressRoute 연결을 사용하려면 /28이어야 합니다.

5. **네트워크를 회로에 연결합니다.**

	확인한 후에만 다음 단계 진행
 
	- ServiceProviderState: 프로비전됨
	- 상태: Enabled

	게이트웨이가 만들어진 Azure 가상 네트워크가 하나 이상 있는지 확인합니다. 게이트웨이가 실행되어야 합니다.

		PS C:\> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=Oct15_HO2-->