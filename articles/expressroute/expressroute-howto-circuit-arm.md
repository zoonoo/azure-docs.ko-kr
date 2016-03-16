<properties
   pageTitle="리소스 관리자 및 PowerShell을 사용하여 Express 경로 회로 만들기 및 수정 | Microsoft Azure"
   description="이 문서에서는 Express 경로 회로를 만들고 프로비전하는 방법을 설명합니다. 또한 회로를 확인, 업데이트 또는 삭제 및 프로비전 해제하는 방법도 보여 줍니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/04/2015"
   ms.author="cherylmc"/>

# 리소스 관리자 및 PowerShell을 사용하여 Express 경로 회로 만들기 및 수정

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

이 문서에서는 Windows PowerShell cmdlet 및 Azure Resource Manager 배포 모델을 사용하여 Azure Express 경로 회로를 만드는 방법을 설명합니다. 아래 단계에서는 회로의 상태 확인, 업데이트 또는 삭제 및 프로비전 해제를 수행하는 방법도 보여 줍니다.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## 필수 구성 요소

Express 경로 회로를 만들려면 다음 작업을 수행해야 합니다.

- 최신 버전(버전 1.0 이상)의 Azure PowerShell 모듈 받기 Azure PowerShell 모듈을 사용하도록 컴퓨터를 구성하는 방법에 대한 단계별 지침은 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md) 페이지의 지침을 따르세요.
- 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md) 페이지와 [워크플로](expressroute-workflows.md) 페이지 검토

## Express 경로 회로 만들기 및 프로비전

**1단계. Express 경로에 대한 PowerShell 모듈을 가져옵니다.**

Express 경로 cmdlet을 사용하려면 [PowerShell 갤러리](http://www.powershellgallery.com/)에서 최신 Powershell 설치 관리자를 설치하고 PowerShell 세션으로 리소스 관리자 모듈을 가져와야 합니다. 관리자 권한으로 PowerShell을 실행합니다.

```
Install-Module AzureRM

Install-AzureRM
```

알려진 의미 체계 버전 범위의 모든 AzureRM 모듈을 가져옵니다.

```
Import-AzureRM
```

또한 알려진 의미 체계 버전 범위의 선택 모듈을 가져올 수 있습니다.

```
Import-Module AzureRM.Network
```

계정에 로그인합니다.

```
Login-AzureRmAccount
```

Express 경로 회로를 만들려는 구독을 선택합니다.

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**2단계. 지원되는 공급자, 위치 및 대역폭 목록을 가져옵니다.**

Express 경로 회로를 만들기 전에 연결 공급자, 지원되는 위치 및 대역폭 옵션 목록이 필요합니다. PowerShell cmdlet *Get-AzureRmExpressRouteServiceProvider*는 나중에 사용할 이 정보를 반환합니다.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

연결 공급자가 여기에 나열되었는지 확인합니다. 다음 정보는 나중에 회로를 만들 때 필요하므로 적어 둡니다.

- 이름
- PeeringLocations
- BandwidthsOffered

이제 Express 경로 회로를 만들 준비가 되었습니다.

**3단계. Express 경로 회로를 만듭니다.**

아직 리소스 그룹이 없는 경우 Express 경로 회로를 만들기 전에 먼저 리소스 그룹을 만들어야 합니다. 다음 명령을 실행하여 수행할 수 있습니다.

```
New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"
```

아래 예제에서는 Equinix 실리콘밸리를 통해 200Mbps Express 경로 회로를 만드는 방법을 보여 줍니다. 다른 공급자와 다른 설정을 사용하는 경우, 요청을 수행할 때 해당 정보를 대체합니다. 다음은 새 서비스 키에 대한 예제 요청입니다.

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

올바른 SKU 계층과 SKU 제품군을 지정하는지 확인합니다.

- SKU 계층은 Express 경로 표준 또는 Express 경로 Premium 추가 기능이 사용되는지 여부를 결정합니다. *표준*을 지정하여 표준 SKU를 가져오거나 Premium 추가 기능에 대해 *프리미엄*을 지정할 수 있습니다.
- SKU 제품군은 청구서 유형을 결정합니다. 데이터 요금제의 경우 *metereddata*를 선택하고 무제한 데이터 요금제의 경우 *unlimiteddata*를 선택할 수 있습니다. **참고:** 회로를 만든 후에는 청구서 유형을 변경할 수 없습니다.

응답에 서비스 키가 포함됩니다. 다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**4단계. 모든 Express 경로 회로를 나열합니다.**

만든 모든 Express 경로 회로 목록을 가져오려면 *Get-AzureRmExpressRouteCircuit* 명령을 실행합니다.

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

응답은 다음 예제와 유사합니다.

```
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
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                      "ServiceProviderName": "Equinix",
                                      "PeeringLocation": "Silicon Valley",
                                      "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

언제든지 *Get-AzureRmExpressRouteCircuit* cmdlet을 사용하여 이 정보를 검색할 수 있습니다. 매개 변수 없이 호출을 수행하면 모든 회로가 표시됩니다. 서비스 키는 *ServiceKey* 필드에 나열됩니다.

```
Get-AzureRmExpressRouteCircuit
```

응답은 다음 예제와 유사합니다.

```
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
```

다음을 실행하여 모든 매개 변수에 대한 자세한 설명을 볼 수 있습니다.

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**5단계. 프로비전을 위해 연결 공급자에 서비스 키를 보냅니다.**

새 Express 경로 회로를 만들면 회로는 다음 상태가 됩니다.

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState*는 서비스 공급자 측의 현재 프로비전 상태에 대한 정보를 제공하며, Status는 Microsoft 측의 상태를 제공합니다. Express 경로 회로를 사용하려면 다음 상태여야 합니다.

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

연결 공급자가 사용자에 대해 활성화를 처리 중이면 회로가 다음 상태로 변경됩니다.

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**6단계. 주기적으로 회로 키의 상태 및 상태를 확인합니다.**

회로 키의 상태를 확인하면 공급자가 회로를 사용하도록 설정한 시점을 알 수 있습니다. 회로가 구성된 후에는 *ServiceProviderProvisioningState*가 아래 예에서처럼 *프로비전됨*으로 표시됩니다.

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

응답은 다음 예제와 유사합니다.

```
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Name = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

이 작업이 성공하려면 모든 가상 네트워크를 Express 경로에서 연결 해제해야 합니다. 이 작업에 실패한 경우 회로에 연결된 가상 네트워크가 있는지 확인하세요.

Express 경로 회로 서비스 공급자 프로비전 상태를 사용할 경우 상태가 사용함 상태에서 *사용 안 함*으로 바뀝니다. 서비스 공급자 측에서 회로를 프로비전 해제하도록 서비스 공급자와 협조해야 합니다. Microsoft에서는 서비스 공급자가 회로의 프로비전을 해제한 다음 통지를 보낼 때까지 리소스를 계속 예약하며 요금을 청구합니다.

위의 cmdlet을 실행하기 전에 서비스 공급자가 회로의 프로비전을 해제한 경우(서비스 공급자 프로비전 상태가 *프로비전 안 됨*으로 설정) Microsoft는 회로의 프로비전을 해제하고 청구를 중지합니다.

## 다음 단계

회로를 만든 후 다음을 수행합니다.

- [Express 경로 회로의 라우팅 만들기 및 수정](expressroute-howto-routing-arm.md)
- [가상 네트워크를 Express 경로 회로에 연결](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0302_2016-->