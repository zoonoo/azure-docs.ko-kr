<properties
   pageTitle="ARM을 사용하여 Express 경로 회로를 구성하기 위한 단계 | Microsoft Azure"
   description="이 문서에서는 Express 경로 회로를 만들고 프로비전하는 단계를 안내합니다. 또한 상태 확인, 업데이트 또는 삭제 및 프로비전 해제 방법을 보여 줍니다."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/04/2015"
   ms.author="ganesr"/>

# Express 경로 회로 만들기 및 수정

> [AZURE.SELECTOR]
[PowerShell Classic](expressroute-howto-circuit-classic.md)
[PowerShell Resource Manager](expressroute-howto-circuit-arm.md)

이 문서에서는 PowerShell cmdlet 및 ARM 배포 모델을 사용하여 Express 경로 회로를 만드는 단계를 안내합니다. 아래 단계에서는 Express 경로 회로의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 설명합니다.

>[AZURE.IMPORTANT]Azure가 현재 두 가지 배포 모델인 리소스 관리자 및 클래식 모드에서 작동한다는 것을 알아야 합니다. 구성을 시작하기 전에 배포 모델 및 도구를 이해해야 합니다. 배포 모델에 대한 자세한 내용은 [Azure 배포 모델](../azure-classic-rm.md)을 참조하세요.

## 필수 구성 요소

- Azure PowerShell 모듈의 최신 버전이 필요합니다. [Azure 다운로드 페이지](http://azure.microsoft.com/downloads)의 PowerShell 섹션에서 최신 PowerShell 모듈을 다운로드할 수 있습니다. 컴퓨터를 구성하여 Azure PowerShell 모듈을 사용하는 방법에 대한 단계별 지침을 따르려면 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 페이지의 지침을 수행하세요. 
- 구성을 시작하기 전에 [필수 구성 요소](expressroute-prerequisites.md) 페이지와 [워크플로](expressroute-workflows.md) 페이지를 검토했는지 확인합니다.

## Express 경로 회로 만들기 및 프로비전

1. **Express 경로에 대한 PowerShell 모듈을 가져옵니다.**

 	Express 경로 cmdlet을 사용하려면 [PowerShell 갤러리](http://www.powershellgallery.com/)에서 최신 Powershell 설치 관리자를 설치하고 PowerShell 세션으로 Azure 리소스 관리자 모듈을 가져와야 합니다. 관리자 권한으로 PowerShell을 실행해야 합니다.

	    Install-Module AzureRM

		Install-AzureRM

	알려진 모든 의미 체계 버전 범위 내에서 AzureRM.* 모듈을 가져옵니다.

		Import-AzureRM

	알려진 의미 체계 버전 범위 내에서 선택 모듈만 가져올 수 있습니다.
		
		Import-Module AzureRM.Network 

	계정에 로그인합니다.

		Login-AzureRmAccount

	Express 경로 회로를 만들려는 구독을 선택합니다.
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
			


2. **공급자, 위치 및 지원되는 대역폭의 목록을 가져옵니다.**

	Express 경로 회로를 만들기 전에 연결 공급자, 지원되는 위치 및 대역폭 옵션 목록이 필요합니다. PowerShell cmdlet *Get-AzureRmExpressRouteServiceProvider*는 나중에 사용할 이 정보를 반환합니다.

		PS C:\> Get-AzureRmExpressRouteServiceProvider

	연결 공급자가 여기에 나열되었는지 확인합니다. 회로를 만들 때 필요하므로 다음을 기록해둡니다.
	
	- 이름
	- PeeringLocations
	- BandwidthsOffered

	이제 Express 경로 회로를 만들 준비가 되었습니다.

		
3. **Express 경로 회로를 만듭니다.**

	Express 경로 회로를 만들기 전에 리소스 그룹이 없는 경우 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 할 수 있습니다.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	아래 예제에서는 Equinix 실리콘밸리를 통해 200Mbps Express 경로 회로를 만드는 방법을 보여줍니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다.

	다음은 새 서비스 키에 대한 예제 요청입니다.

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	올바른 SKU 계층과 SKU 제품군을 지정하는지 확인합니다.
 
	 - SKU 계층은 Express 경로 표준 또는 Express 경로 Premium 추가 기능이 사용되는지 여부를 결정합니다. *표준*을 지정하여 표준 SKU를 가져오거나 Premium 추가 기능에 대해 *프리미엄*을 지정할 수 있습니다.
	 - SKU 제품군은 청구서 유형을 결정합니다. 데이터 요금제에 대해 *metereddata*를 무제한 데이터 요금제에 대해 *unlimiteddata"를 선택할 수 있습니다. **참고:** 회로가 만들어지면 청구서 유형을 변경할 수 없습니다.

	응답에 서비스 키가 포함됩니다. 다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

		get-help New-AzureRmExpressRouteCircuit -detailed 

4. **모든 Express 경로 목록.**

	*Get-AzureRmExpressRouteCircuit* 명령을 실행하여 자신이 만든 모든 Express 경로 회로 목록을 구할 수 있습니다.

		#Getting service key
		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	응답은 아래 예제와 비슷합니다.

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []


	언제든지 *Get-AzureRmExpressRouteCircuit* cmdlet을 사용하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 *ServiceKey* 필드에 나열됩니다.

		Get-AzureRmExpressRouteCircuit

	응답은 아래 예제와 비슷합니다.

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : NotProvisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []



	다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

		get-help Get-AzureRmExpressRouteCircuit -detailed 

5. **프로비전을 위해 연결 공급자에게 서비스 키를 보냅니다.**

	새 Express 경로 회로를 만들면 회로는 다음 상태가 됩니다.
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	*ServiceProviderProvisioningState*는 서비스 공급자 측의 현재 프로비전 상태에 대한 정보를 제공하며, Status는 Microsoft 측의 상태를 제공합니다. Express 경로 회로를 사용하려면 다음 상태여야 합니다.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	연결 공급자가 사용자에 대해 활성화를 처리 중이면 다음 상태가 됩니다.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



5. **주기적으로 회로 키의 상태 및 상태를 확인합니다.**

	이를 통해 공급자가 회로 활성화를 마치면 알 수 있습니다. 회로가 구성된 후에는 *ServiceProviderProvisioningState*가 아래 예에서처럼 *프로비전됨*으로 표시됩니다.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	응답은 아래 예제와 비슷합니다.

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []

6. **라우팅 구성을 만듭니다.**
	
	단계별 지침은 [Express 경로 회로 라우팅 구성(회로 피어링 만들기 및 수정)](expressroute-howto-routing-arm.md) 페이지를 참조하세요.

7. **VNet을 Express 경로 회로에 연결합니다.**

	이제 VNet을 Express 경로 회로에 연결합니다. 단계별 지침은 [Express 경로 회로를 VNet에 연결](expressroute-howto-linkvnet-arm.md)을 참조하세요. Express 경로에 대한 가상 네트워크를 만들어야 할 경우 지침은 [Express 경로에 대한 가상 네트워크 만들기](expressroute-howto-createvnet-classic.md)를 참조하세요.

##  Express 경로 회로의 상태를 확인하는 방법

언제든지 *Get-AzureRmExpressRouteCircuit* cmdlet을 사용하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다.

		Get-AzureRmExpressRouteCircuit

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []

리소스 그룹 이름 및 회로 이름을 매개 변수 형태로 호출에 전달하면 특정 Express 경로 회로에 대한 정보를 가져올 수 있습니다.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	The response will be something similar to the example below:

		Name                             : ExpressRouteARMCircuit
		ResourceGroupName                : ExpressRouteResourceGroup
		Location                         : westus
		Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
		Etag                             : W/"################################"
		ProvisioningState                : Succeeded
		Sku                              : {
		                                     "Name": "Standard_MeteredData",
		                                     "Tier": "Standard",
		                                     "Family": "MeteredData"
		                                   }
		CircuitProvisioningState         : Enabled
		ServiceProviderProvisioningState : Provisioned
		ServiceProviderNotes             : 
		ServiceProviderProperties        : {
		                                     "ServiceProviderName": "Equinix",
		                                     "PeeringLocation": "Silicon Valley",
		                                     "BandwidthInMbps": 200
		                                   }
		ServiceKey                       : **************************************
		Peerings                         : []

다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

		get-help get-azurededicatedcircuit -detailed 

##  Express 경로 회로 수정

연결에 미치는 영향 없이 Express 경로 회로의 특정 속성을 수정할 수 있습니다.

다음을 수행할 수 있습니다.

- 중단 시간 없이 Express 경로 회로에 대한 Express 경로 프리미엄 추가 기능 활성화/비활성화
- 중단 시간 없이 Express 경로 회로의 대역폭 증가시킵니다.

제한 및 제한 사항에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 참조하세요.

### Express 경로 프리미엄 추가 기능을 활성화하는 방법

다음 PowerShell 코드 조각을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 활성화할 수 있습니다.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Name = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
이제 Express 경로 프리미엄 추가 기능을 사용할 수 있게 됩니다. 명령이 성공적으로 실행되는 즉시 프리미엄 추가 기능에 대한 청구가 시작됩니다.

### Express 경로 프리미엄 추가 기능을 비활성화하는 방법

다음 PowerShell cmdlet을 사용하여 기존 회로에 대해 Express 경로 프리미엄 추가 기능을 사용하지 않도록 설정할 수 있습니다.
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


그러면 회로에 대해 프리미엄 추가 기능을 사용할 수 없습니다.

>[AZURE.IMPORTANT]표준 회로에 허용된 것보다 많은 리소스를 사용할 경우 이 작업이 실패합니다.
>
>- 프리미엄을 표준으로 다운그레이드하기 전에 회로에 연결된 가상 네트워크 수가 10개 미만인지 확인해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고 프리미엄 요금이 청구됩니다.
- 다른 지리적 위치의 모든 가상 네트워크를 연결 해제해야 합니다. 그렇지 않으면 업데이트 요청이 실패하고 프리미엄 요금이 청구됩니다.
- 사설 피어링을 위해서는 경로 테이블의 경로가 4000개 미만이어야 합니다. 경로 테이블 크기가 4000개 경로 이상이면 BGP 세션이 폐기되고 게시된 프리픽스 수가 4000개 미만이 될 때까지 다시 활성화되지 않습니다.


### Express 경로 회로 대역폭을 업데이트하는 방법

공급자에 대해 지원되는 대역폭 옵션은 [Express 경로 FAQ](expressroute-faqs.md) 페이지를 확인하세요. 기존 회로의 크기보다 큰 모든 크기를 선택할 수 있습니다. 필요한 크기를 선택한 후에는 다음 명령을 사용하여 회로 크기를 조정할 수 있습니다.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

회로의 크기는 Microsoft 쪽에서 조정됩니다. 변경에 부합하게 구성을 업데이트하려면 해당 공급자에게 연락해야 합니다. 이 지점에서 업데이트된 대역폭 옵션에 대한 요금을 청구하기 시작합니다.

>[AZURE.IMPORTANT]그러나 중단 없이 Express 경로 회로의 대역폭을 줄일 수는 없습니다. 대역폭 다운그레이드에서는 Express 경로 회로의 프로비전을 해제하고 새 Express 경로 회로를 다시 프로비전해야 합니다.

##  Express 경로 회로 삭제 및 프로비전 해제

다음 명령을 실행하여 Express 경로 회로를 삭제할 수 있습니다.

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

이 작업이 성공하려면 모든 가상 네트워크를 Express 경로에서 연결 해제해야 합니다. 이 작업이 실패할 경우 회로에 연결된 가상 네트워크가 있는지 확인하십시오.

Express 경로 회로 서비스 공급자 프로비전 상태를 사용할 경우 상태가 사용함 상태에서 *사용 안 함*으로 바뀝니다. 서비스 공급자 측에서 회로를 프로비전 해제하도록 서비스 공급자와 협조해야 합니다. 이제 리소스를 예약하며, 서비스 공급자가 회로 프로비저닝을 해제한 다음 통지를 보내기 전까지 청구가 계속됩니다.

위의 cmdlet을 실행하기 전에 서비스 공급자가 회로의 프로비전을 해제한 경우(서비스 공급자 프로비전 상태가 *프로비전 안 됨*으로 설정) 회로에 프로비전을 해제하고 청구를 중지합니다.

## 다음 단계

- [라우팅 구성](expressroute-howto-routing-arm.md)
- [VNet을 Express 경로 회로에 연결](expressroute-howto-linkvnet-arm.md) 

<!---HONumber=Nov15_HO2-->