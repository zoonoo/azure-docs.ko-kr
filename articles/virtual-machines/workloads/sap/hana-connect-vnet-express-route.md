---
title: 가상 네트워크에서 Azure(대규모 인스턴스)의 SAP HANA에 대한 연결 설정 | Microsoft Docs
description: 가상 네트워크에서 Azure(대규모 인스턴스)의 SAP HANA를 사용하기 위한 연결을 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a64a60603cd9898386a975313afc676e3b253326
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353600"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>가상 네트워크를 HANA 대규모 인스턴스에 연결

Azure 가상 네트워크를 만든 후 해당 네트워크를 Azure 대규모 인스턴스의 SAP HANA에 연결합니다. 가상 네트워크에 Azure ExpressRoute 게이트웨이를 만듭니다. 이 게이트웨이를 사용하면 가상 네트워크를 ExpressRoute 회로에 연결하여 대규모 인스턴스 스탬프의 고객 테넌트에 연결할 수 있습니다.

> [!NOTE] 
> 이 단계를 완료하려면 최대 30분이 걸릴 수 있습니다. 새 게이트웨이는 지정된 Azure 구독에 만들어진 다음, 지정된 Azure 가상 네트워크에 연결됩니다.

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. 그렇지 않은 경우 게이트웨이를 삭제하고 ExpressRoute 게이트웨이로 다시 만들어야 합니다. ExpressRoute 게이트웨이가 이미 설정된 경우 이 문서의 다음 섹션 "가상 네트워크 연결"을 참조하세요. 

- [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 가상 네트워크에 연결된 ExpressRoute VPN 게이트웨이를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **가상 네트워크 게이트웨이**를 추가한 다음, 게이트웨이 유형으로 **ExpressRoute**를 선택합니다.
  - PowerShell을 사용하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 사용합니다. 다음 명령은 ExpressRoute 게이트웨이를 만듭니다. _$_ 뒤의 텍스트는 사용자 정보로 업데이트해야 하는 사용자 정의 변수입니다.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

이 예제에서는 HighPerformance 게이트웨이 SKU를 사용했습니다. 옵션은 Azure(대규모 인스턴스)의 SAP HANA에 대해 지원되는 유일한 게이트웨이 SKU인 HighPerformance 또는 UltraPerformance입니다.

> [!IMPORTANT]
> Type II 클래스 SKU의 HANA 대규모 인스턴스의 경우, UltraPerformance 게이트웨이 SKU를 사용해야 합니다.

## <a name="link-virtual-networks"></a>가상 네트워크 연결

이제 Azure 가상 네트워크에 ExpressRoute 게이트웨이가 만들어졌습니다. Microsoft가 제공한 권한 부여 정보를 사용하여 ExpressRoute 게이트웨이를 Azure(대규모 인스턴스) ExpressRoute 회로의 SAP HANA에 연결합니다. Azure Portal 또는 PowerShell을 사용하여 연결할 수 있습니다. Azure Portal을 사용하는 것이 좋지만 PowerShell을 사용하려고 한다면 지침은 다음과 같습니다. 

각 연결에 대해 서로 다른 AuthGUID를 사용하여 각 가상 네트워크 게이트웨이에 대해 다음 명령을 실행합니다. 다음 스크립트에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 가상 네트워크 및 해당 게이트웨이에 지정됩니다. 다른 Azure 가상 네트워크를 추가하려면 HANA 대규모 인스턴스를 Azure에 연결하는 ExpressRoute 회로에 대해 다른 AuthID를 가져와야 합니다. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

게이트웨이를 구독과 연결된 두 개 이상의 ExpressRoute 회로에 연결하려면 이 단계를 두 번 이상 실행해야 할 수 있습니다. 예를 들어 가상 네트워크를 온-프레미스 네트워크에 연결하는 ExpressRoute 회로에 동일한 가상 네트워크 게이트웨이를 연결해야 할 가능성이 높습니다.

## <a name="next-steps"></a>다음 단계

- [HLI에 대한 추가 네트워크 요구 사항](hana-additional-network-requirements.md)