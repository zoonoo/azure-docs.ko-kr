<properties
   pageTitle="Express 경로 회로를 구성하기 위한 단계 | Microsoft Azure"
   description="이 문서에서는 Express 경로 회로를 만들고 프로비전하는 단계를 안내합니다. 또한 상태 확인, 업데이트 또는 삭제 및 프로비전 해제 방법을 보여줍니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="cherylmc"/>

# Express 경로 회로 만들기 및 수정

이 문서에서는 PowerShell cmdlet 및 일반 배포 모델을 사용하여 Express 경로 회로를 만드는 단계를 안내합니다. 아래 단계에서는 Express 경로 회로의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 설명합니다.

>[AZURE.IMPORTANT]Azure가 현재 두 가지 배포 모델인 리소스 관리자 및 클래식 모드에서 작동한다는 것을 알아야 합니다. 구성을 시작하기 전에 배포 모델 및 도구를 이해해야 합니다. 배포 모델에 대한 자세한 내용은 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요.

## 필수 구성 요소

- Azure PowerShell 모듈의 최신 버전이 필요합니다. [Azure 다운로드 페이지](http://azure.microsoft.com/downloads)의 PowerShell 섹션에서 최신 PowerShell 모듈을 다운로드할 수 있습니다. 컴퓨터를 구성하여 Azure PowerShell 모듈을 사용하는 방법에 대한 단계별 지침은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 페이지의 지침을 수행합니다. 
- 구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 페이지와 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.

## Express 경로 회로 만들기 및 프로비전

1. **Express 경로에 대한 PowerShell 모듈을 가져옵니다.**

 	Express 경로 cmdlet을 사용하려면 Azure와 Express 경로 모듈을 PowerShell 세션으로 가져와야 합니다. PowerShell 세션으로 Azure 및 Express 경로 모듈을 가져오려면 다음 명령을 실행합니다.

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **공급자, 위치 및 지원되는 대역폭의 목록을 가져옵니다.**

	Express 경로 회로를 만들기 전에 연결 공급자, 지원되는 위치 및 대역폭 옵션 목록이 필요합니다. PowerShell cmdlet *Get-AzureDedicatedCircuitServiceProvider*는 나중에 사용할 이 정보를 반환합니다.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		Aryaka Networks      Silicon Valley,Washington      200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		                     DC,Singapore                                                                                                                                                                                                                                
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		AT&T Netbond         Washington DC,Silicon          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Dallas                                                                                                                                                                                                                               
		British Telecom      London,Amsterdam,Washington    10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     DC,Tokyo,Silicon Valley                                                                                                                                                                                                                     
		Colt Ethernet        Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Colt IPVPN           Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Comcast              Washington DC,Silicon Valley   200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong Kong,Singapore,                                                                                                                                                                                                              
		                     Sydney,Tokyo,Sao Paulo,Los                                                                                                                                                                                                                  
		                     Angeles,Melbourne                                                                                                                                                                                                                           
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		InterCloud           Washington                     200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     DC,London,Singapore,Amsterdam                                                                                                                                                                                                               
		Internet Solutions   London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		– Cloud Connect                                                                                                                                                                                                                                                  
		Interxion            Amsterdam                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Level 3              London,Chicago,Dallas,Seattle, 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Chicago,Dallas,Seattle, 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Silicon Valley,Washington DC                                                                                                                                                                                                                
		IPVPN                                                                                                                                                                                                                                                            
		Megaport             Melbourne,Sydney               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NEXTDC               Melbourne                      200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		NTT Communications   Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Orange               Amsterdam,London,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC,Hong Kong                                                                                                                                                                                                              
		PCCW Global Limited  Hong Kong                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		Tata Communications  Hong Kong,Singapore,London,Ams 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     terdam,Chennai,Mumbai                                                                                                                                                                                                                       
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney,Melbourne               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              London,Hong Kong,Silicon       10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		                     Valley,Washington DC                                                                                                                                                                                                                        
		Vodafone             London                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Zayo Group           Washington DC                  200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
    	

3. **Express 경로 회로를 만듭니다.**

	아래 예제에서는 Equinix 실리콘밸리를 통해 200Mbps Express 경로 회로를 만드는 방법을 보여줍니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다.

	다음은 새 서비스 키에 대한 예제 요청입니다.

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "MyTestCircuit"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard

	또는 프리미엄 추가 기능을 통해 Express 경로 회로를 만들려면 다음 예를 사용합니다. 프리미엄 추가 기능에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 참조하세요.

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium
	
	
	응답에 서비스 키가 포함됩니다. 다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

		get-help new-azurededicatedcircuit -detailed 

4. **모든 Express 경로 목록.**

	*Get-azurededicatedcircuit* 명령을 실행하여 자신이 만든 모든 Express 경로 회로 목록을 구할 수 있습니다.

		#Getting service key
		Get-AzureDedicatedCircuit

	응답은 아래 예제와 비슷합니다.

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	언제든지 *Get-AzureDedicatedCircuit* cmdlet을 사용하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 *ServiceKey* 필드에 나열됩니다.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Sku                              : Standard
		Status                           : Enabled

	다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

		get-help get-azurededicatedcircuit -detailed 

5. **프로비전을 위해 연결 공급자에게 서비스 키를 보냅니다.**

	새 Express 경로 회로를 만들면 회로는 다음 상태가 됩니다.
	
		ServiceProviderProvisioningState : NotProvisioned
		
		Status                           : Enabled

	*ServiceProviderProvisioningState*는 서비스 공급자 측의 현재 프로비전 상태에 대한 정보를 제공하며, Status는 Microsoft 측의 상태를 제공합니다. Express 경로 회로를 사용하려면 다음 상태여야 합니다.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled

	연결 공급자가 사용자에 대해 활성화를 처리 중이면 다음 상태가 됩니다.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **주기적으로 회로 키의 상태 및 상태를 확인합니다.**

	이를 통해 공급자가 회로 활성화를 마치면 알 수 있습니다. 회로가 구성된 후에는 *ServiceProviderProvisioningState*가 아래 예에서처럼 *프로비전됨*으로 표시됩니다.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

6. **라우팅 구성을 만듭니다.**
	
	단계별 지침은 [Express 경로 회로 라우팅 구성(회로 피어링 만들기 및 수정)](expressroute-howto-routing-classic.md) 페이지를 참조하세요.

7. **VNet을 Express 경로 회로에 연결합니다.**

	이제 VNet을 Express 경로 회로에 연결합니다. 단계별 지침은 [Express 경로 회로를 VNet에 연결](expressroute-howto-linkvnet-classic.md)을 참조하세요. Express 경로에 대한 가상 네트워크를 만들어야 할 경우 지침은 [Express 경로에 대한 가상 네트워크 만들기](expressroute-howto-createvnet-classic.md)를 참조하세요.

##  Express 경로 회로의 상태를 확인하는 방법

언제든지 *Get-AzureCircuit* cmdlet을 사용하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

		Bandwidth                        : 1000
		CircuitName                      : MyAsiaCircuit
		Location                         : Singapore
		ServiceKey                       : #################################
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

서비스 키를 매개 변수 형태로 호출에 전달하면 특정 Express 경로 회로에 대한 정보를 가져올 수 있습니다.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

		Bandwidth                        : 200
		CircuitName                      : MyTestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

		get-help get-azurededicatedcircuit -detailed 

##  Express 경로 회로 수정

연결에 미치는 영향 없이 Express 경로 회로의 특정 속성을 수정할 수 있습니다.

다음을 수행할 수 있습니다.

- 중단 시간 없이 Express 경로 회로에 대한 Express 경로 프리미엄 추가 기능 활성화/비활성화
- 중단 시간 없이 Express 경로 회로의 대역폭 증가시킵니다.

제한 및 제한 사항에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 참조하세요.

### Express 경로 프리미엄 추가 기능을 활성화하는 방법

다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 활성화할 수 있습니다.
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

이제 Express 경로 프리미엄 추가 기능을 사용할 수 있게 됩니다. 명령이 성공적으로 실행되는 즉시 프리미엄 추가 기능에 대한 청구가 시작됩니다.

### Express 경로 프리미엄 추가 기능을 비활성화하는 방법

다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

그러면 회로에 대해 프리미엄 추가 기능을 사용할 수 없습니다.

>[AZURE.IMPORTANT]표준 회로에 허용된 것보다 많은 리소스를 사용할 경우 이 작업이 실패합니다.
>
>- 프리미엄을 표준으로 다운그레이드하기 전에 회로에 연결된 가상 네트워크 수가 10개 미만인지 확인해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고 프리미엄 요금이 청구됩니다.
- 다른 지리적 위치의 모든 가상 네트워크를 연결 해제해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고 프리미엄 요금이 청구됩니다.
- 사설 피어링을 위해서는 경로 테이블의 경로가 4000개 미만이어야 합니다. 경로 테이블 크기가 4000개 경로 이상이면 BGP 세션이 폐기되고 게시된 프리픽스 수가 4000개 미만이 될 때까지 다시 활성화되지 않습니다.


### Express 경로 회로 대역폭을 업데이트하는 방법

공급자에 대해 지원되는 대역폭 옵션은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 확인하세요. 기존 회로의 크기보다 큰 모든 크기를 선택할 수 있습니다. 필요한 크기를 선택한 후에는 다음 명령을 사용하여 회로 크기를 조정할 수 있습니다.

		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

회로의 크기는 Microsoft 쪽에서 조정됩니다. 변경에 부합하게 구성을 업데이트하려면 해당 공급자에게 연락해야 합니다. 이 지점에서 업데이트된 대역폭 옵션에 대한 요금을 청구하기 시작합니다.

>[AZURE.IMPORTANT]그러나 중단 없이 Express 경로 회로의 대역폭을 줄일 수는 없습니다. 대역폭 다운그레이드에서는 Express 경로 회로의 프로비전을 해제하고 새 Express 경로 회로를 다시 프로비전해야 합니다.

##  Express 경로 회로 삭제 및 프로비전 해제

다음 명령을 실행하여 Express 경로 회로를 삭제할 수 있습니다.

		PS C:\> Remove-AzureDedicatedCircuit -ServiceKey "*********************************"	

이 작업이 성공하려면 모든 가상 네트워크를 Express 경로에서 연결 해제해야 합니다. 이 작업이 실패할 경우 회로에 연결된 가상 네트워크가 있는지 확인하십시오.

Express 경로 회로 서비스 공급자 프로비전 상태를 사용할 경우 상태가 사용함 상태에서 *사용 안 함*으로 바뀝니다. 서비스 공급자 측에서 회로를 프로비전 해제하도록 서비스 공급자와 협조해야 합니다. 이제 리소스를 예약하며, 서비스 공급자가 회로 프로비저닝을 해제한 다음 통지를 보내기 전까지 청구가 계속됩니다.

위의 cmdlet을 실행하기 전에 서비스 공급자가 회로의 프로비전을 해제한 경우(서비스 공급자 프로비전 상태가 *프로비전 안 됨*으로 설정) 회로에 프로비전을 해제하고 청구를 중지합니다.

## 다음 단계

- [라우팅 구성](expressroute-howto-routing-classic.md)
- [VNet을 Express 경로 회로에 연결](expresssroute-howto-linkvnet-classic.md) 

<!---HONumber=Oct15_HO2-->