---
title: Azure Availability Zones에서 영역 중복 가상 네트워크 게이트웨이 만들기 - 미리 보기 | Microsoft Docs
description: 가용성 영역에서 VPN Gateway 및 ExpressRoute 게이트웨이 배포 - 미리 보기.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/25/2018
ms.author: cherylmc
ms.openlocfilehash: a08c0f772965ddb2b40ac1ced1f26ade4cba3197
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017035"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Azure Availability Zones에서 영역 중복 가상 네트워크 게이트웨이 만들기 - 미리 보기

[Azure Availability Zones](../availability-zones/az-overview.md)에서 VPN 및 ExpressRoute 게이트웨이를 배포할 수 있습니다. 그러면 가상 네트워크 게이트웨이에 복원력, 확장성 및 고가용성이 제공됩니다. Azure Availability Zones에서 게이트웨이를 배포하면 Azure에서 영역 수준 오류로의 온-프레미스 네트워크 연결성을 보호하면서 물리적 및 논리적으로 영역 내 게이트웨이를 구분합니다.

영역 및 영역 중복 게이트웨이는 일반 가상 네트워크 게이트웨이 전반에서 기본적으로 성능이 향상됩니다. 또한, 영역 중복 또는 영역 가상 네트워크 게이트웨이를 만들기는 다른 게이트웨이를 만드는 것에 비해 더 빠릅니다. 생성 시간은 45분 미만으로, ExpressRoute 게이트웨이에 약 15분, VPN 게이트웨이에 약 19분이 걸립니다.

### <a name="zrgw"></a>영역 중복 게이트웨이

가용성 영역에 가상 네트워크 게이트웨이를 자동으로 배포하는 데 영역 중복 가상 네트워크 게이트웨이를 사용할 수 있습니다. 영역 중복 게이트웨이를 통해 Azure에서 중요하고 확장 가능한 서비스에 액세스하도록 GA에서 99.99% 가동 시간 SLA를 활용할 수 있습니다.

<br>
<br>

![영역 중복 게이트웨이 그래픽](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>영역 게이트웨이

특정 영역에는 게이트웨이를 배포하려면 영역 게이트웨이를 사용합니다. 영역 게이트웨이를 배포하는 경우 게이트웨이의 두 인스턴스가 동일한 가용성 영역에 배포됩니다.

<br>
<br>

![영역 게이트웨이 그래픽](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>게이트웨이 SKU

영역 중복 및 영역 게이트웨이는 새 게이트웨이 SKU를 사용해야 합니다. [미리 보기에서 직접 등록](#enroll)하면 모든 Azure AZ 영역에 새 가상 네트워크 게이트웨이 SKU가 표시됩니다. 이러한 SKU는 영역 중복 및 영역 게이트웨이에 따라 다른 점을 제외하고 ExpressRoute 및 VPN Gateway에 대한 해당 SKU와 비슷합니다.

새 게이트웨이 SKU는 다음과 같습니다.

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>공용 IP SKU

영역 중복 게이트웨이 및 영역 게이트웨이는 모두 Azure 공용 IP 리소스 *표준* SKU에 의존합니다. Azure 공용 IP 리소스의 구성은 배포하는 게이트웨이가 영역 중복인지 아니면 영역인지 여부를 결정합니다. *기본* SKU를 통해 공용 IP 리소스를 만드는 경우 게이트웨이에는 영역 중복성이 없으며 게이트웨이 리소스는 영역별이 됩니다.

### <a name="pipzrg"></a>영역 중복 게이트웨이

영역을 지정하지 않고 **표준** 공용 IP SKU를 사용하여 공용 IP 주소를 만드는 경우 동작은 게이트웨이가 VPN 게이트웨이인지 아니면 ExpressRoute 게이트웨이인지에 따라 다릅니다. 

* VPN 게이트웨이의 경우 두 게이트웨이 인스턴스가 영역 중복성을 제공하도록 이러한 세 영역 중 2곳에 배포됩니다. 
* ExpressRoute 게이트웨이의 경우 인스턴스가 세 개 이상일 수 있으므로 게이트웨이를 모든 세 영역 모두에서 확장할 수 있습니다.

### <a name="pipzg"></a>영역 게이트웨이

**표준** 공용 IP SKU를 사용하여 공용 IP 주소를 만들고 영역(1, 2 또는 3)을 지정하는 경우 모든 게이트웨이 인스턴스가 동일한 영역에 배포됩니다.

### <a name="piprg"></a>지역 게이트웨이

**기본** 공용 IP SKU를 사용하여 공용 IP 주소를 만드는 경우 게이트웨이가 지역 게이트웨이로 배포되며 게이트웨이에 기본 제공된 영역 중복성이 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

자신의 컴퓨터 또는 Azure Cloud Shell에 로컬로 설치된 PowerShell을 사용할 수 있습니다. PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 기능을 사용하려면 PowerShell 모듈의 최신 버전이 필요합니다.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>PowerShell을 로컬로 사용하려면 다음을 수행합니다.

Cloud Shell을 사용하는 대신 컴퓨터에서 로컬로 PowerShell을 사용하는 경우 PowerShell 모듈 6.1.1 이상을 설치해야 합니다. 설치한 PowerShell의 버전을 확인하려면 다음 명령을 사용합니다.

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. 미리 보기에 등록

영역 중복 또는 영역 게이트웨이를 구성하려면 미리 보기에서 구독을 직접 등록해야 합니다. 구독이 프로비전되면 모든 Azure AZ 영역에서 새 게이트웨이 SKU를 확인할 수 있습니다. 

Azure 계정에 로그인하고 이 미리 보기에 대한 허용 목록에 추가하려는 구독을 사용하고 있는지 확인합니다. 다음 예제를 사용하여 등록합니다.

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

다음 명령을 사용하여 ‘AllowVMSSVirtualNetworkGateway’ 기능이 구독으로 등록되어 있는지 확인합니다.

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

결과는 다음 예제와 유사합니다.

![프로비전됨](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. 변수 선언

예제 단계에 사용된 값은 다음과 같습니다. 또한 일부 예제에서는 단계 내에서 선언된 변수를 사용합니다. 사용자 환경에서 이러한 단계를 사용하는 경우 이러한 값을 사용자의 정보로 바꾸어야 합니다. 위치를 지정할 경우 지정한 영역이 지원되는지 확인합니다. 자세한 내용은 [FAQ](#faq)을 참조하세요.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>3. 가상 네트워크 만들기

리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

가상 네트워크를 만듭니다.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>4. 게이트웨이 서브넷 추가

게이트웨이 서브넷은 가상 네트워크 게이트웨이 서비스가 사용하는 예약된 IP 주소를 포함합니다. 다음 예제를 사용하여 게이트웨이 서브넷을 추가하고 설정합니다.

게이트웨이 서브넷을 추가합니다.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

가상 네트워크에 대한 게이트웨이 서브넷 구성을 설정합니다.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>5. 공용 IP 주소 요청
 
이 단계에서는 만들려는 게이트웨이에 적용되는 지침을 선택합니다. 게이트웨이 배포를 위한 영역 선택은 공용 IP 주소에 대해 지정된 영역에 따라 달라집니다.

### <a name="ipzoneredundant"></a>영역 중복 게이트웨이의 경우

**표준** PublicIpaddress SKU가 포함된 공용 IP 주소를 요청하고 모든 영역을 지정하지 않습니다. 이 경우 만들어진 표준 공용 IP 주소는 영역 중복 공용 IP가 됩니다.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard
```

### <a name="ipzonalgw"></a>영역 게이트웨이의 경우

**표준** PublicIpaddress SKU가 포함된 공용 IP 주소를 요청합니다. 영역(1, 2 또는 3)을 지정합니다. 이 영역에 모든 게이트웨이 인스턴스가 배포됩니다.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>지역 게이트웨이의 경우

**기본** PublicIpaddress SKU가 포함된 공용 IP 주소를 요청합니다. 이 경우 게이트웨이가 지역 게이트웨이로 배포되며 게이트웨이에 기본 제공된 영역 중복성이 없습니다. 게이트웨이 인스턴스는 각각 모든 영역에서 만들어집니다.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. IP 구성 만들기

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. 게이트웨이 만들기

가상 네트워크 게이트웨이를 만듭니다.

>[!NOTE]
>이때 게이트웨이 SKU를 지정할 수 없습니다. SKU는 자동으로 기본값이 ExpressRoute에 대해 ErGw1AZ, VPN Gateway에 대해 VpnGw1AZ가 설정됩니다.
>

### <a name="for-expressroute"></a>ExpressRoute의 경우

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>VPN Gateway의 경우

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>피드백을 제공하는 방법

여러분의 의견에 감사드립니다. 영역 중복성 및 영역 VPN 및 Express Route 게이트웨이에 대한 문제를 보고하거나 의견을 제공하려면 aznetworkgateways@microsoft.com에 이메일을 보내주세요. 제목 줄에서 “[]”에 회사 이름을 적어주세요. 문제를 보고하려는 경우 구독 ID도 보내주세요.

## <a name="faq"></a>FAQ

### <a name="how-do-i-sign-up-for-the-preview"></a>미리 보기에 등록하려면 어떻게 해야 하나요?

이 문서의 PowerShell 명령을 사용하여 [직접 등록](#enroll)할 수 있습니다.

### <a name="what-will-change-when-i-enroll"></a>내가 등록하면 무엇이 변경되나요?

사용자 관점에서 미리 보기 중에 영역 중복성이 포함된 게이트웨이를 배포할 수 있습니다. 즉, Azure Availability Zones 간에 게이트웨이의 모든 인스턴스가 배포되고, 각 가용성 영역은 다른 장애 및 업데이트 도메인입니다. 이렇게 하면 사용자 게이트웨이가 영역 오류에 대해 더 안정적이고, 사용 가능하며, 복원력이 증가합니다.

### <a name="what-regions-are-available-for-the-preview"></a>미리 보기는 어떤 지역에서 사용할 수 있나요?

영역 중복 및 영역 게이트웨이는 프로덕션/Azure 공용 지역에서 사용할 수 있습니다.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>이 미리 보기에 참여하면 요금이 청구되나요?

미리 보기 중에는 게이트웨이에 대한 요금이 청구되지 않습니다. 단, 배포를 사용하는 연결된 SLA는 없습니다. 우리는 여러분의 의견을 기다립니다.

> [!NOTE]
> ExpressRoute 게이트웨이의 경우 게이트웨이 비용은 청구되지 않습니다. 단, 회로 자체(게이트웨이 아님)에는 요금이 청구됩니다.

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>체험해 볼 수 있는 지역은 어디인가요?

공개 미리 보기는 미국 중남부 및 프랑스 중부(가용성 영역이 있는 Azure 지역은 일반적으로 사용 가능)에서 제공됩니다. 앞으로 영역 중복 게이트웨이를 다른 Azure 공용 지역에서 사용할 수 있도록 제공할 예정입니다.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>내 기존 가상 네트워크 게이트웨이를 영역 중복 또는 영역 게이트웨이로 변경할 수 있나요?

영역 중복 또는 영역 게이트웨이로의 기존 가상 네트워크 게이트웨이 마이그레이션은 현재 지원되지 않습니다. 단, 기존 게이트웨이를 삭제하고 영역 중복 또는 영역 게이트웨이를 다시 만들 수는 있습니다.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>동일한 가상 네트워크에서 VPN과 Express Route 게이트웨이를 모두 배포할 수 있나요?

동일한 가상 네트워크에서 VPN과 Express Route 게이트웨이의 공존은 공개 미리 보기에서 지원됩니다. 단, 다음 요구 사항과 제한 사항을 고려해야 합니다.

* 게이트웨이 서브넷에 대한 /27 IP 주소 범위를 예약합니다.
* 영역 중복/영역 Express Route 게이트웨이는 영역 중복/영역 VPN 게이트웨이를 통해서만 공존할 수 있습니다.
* 영역 중복/영역 VPN 게이트웨이를 배포하기 전에 영역 중복/영역 Express Route 게이트웨이를 배포합니다.
* 영역 중복/영역 Express Route 게이트웨이는 최대 4개 회로에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

여러분의 의견에 감사드립니다. 영역 중복성 및 영역 VPN 및 Express Route 게이트웨이에 대한 문제를 보고하거나 의견을 제공하려면 aznetworkgateways@microsoft.com에 이메일을 보내주세요. 제목 줄에서 “[]”에 회사 이름을 적어주세요. 문제를 보고하려는 경우 구독 ID도 보내주세요.