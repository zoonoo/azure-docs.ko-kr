---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 가상 네트워크에서 연결 설정 | Microsoft Docs
description: Azure(대규모 인스턴스)의 SAP HANA에 대한 가상 네트워크에서 연결을 설정합니다.
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
ms.openlocfilehash: 5efdda485e4e1f5013948c6636b267f0d388f4d5
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167636"
---
# <a name="connecting-a-vnet-to-hana-large-instance-expressroute"></a>VNet을 HANA 큰 인스턴스 ExpressRoute에 연결

이제 모든 IP 주소 범위를 정의하고 Microsoft에서 데이터를 다시 가져 왔기 때문에 이전에 만든 VNet을 HANA 큰 인스턴스에 연결할 수 있습니다. Azure VNet을 만들면 ExpressRoute 게이트웨이를 VNet에 만들어서 큰 인스턴스 스탬프의 고객 테넌트에 연결되는 ExpressRoute 회로에 VNet을 연결해야 합니다.

> [!NOTE] 
> 새 게이트웨이가 지정된 Azure 구독에서 생성된 다음 지정된 Azure VNet에 연결되기 때문에 이 단계를 완료하는 데 최대 30분 정도가 걸릴 수 있습니다.

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. 그렇지 않으면 게이트웨이를 삭제하고 ExpressRoute 게이트웨이로 다시 만들어야 합니다. ExpressRoute 게이트웨이가 이미 설정된 경우 Azure VNet이 온-프레미스 연결성을 위해 ExpressRoute 회로에 이미 연결되어 있으므로 아래 VNet 연결 섹션을 진행합니다.

- (새로운) [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 VNet에 연결된 ExpressRoute VPN 게이트웨이를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **Virtual Network 게이트웨이**를 추가한 다음 **ExpressRoute**를 게이트웨이 유형으로 선택합니다.
  - PowerShell을 대신 선택하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하고 사용하여 최적의 환경을 제공합니다. 다음 명령은 ExpressRoute 게이트웨이를 만듭니다. _$_ 뒤의 텍스트는 사용자 정보로 업데이트해야 하는 사용자 정의 변수입니다.

```PowerShell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

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

이 예제에서는 HighPerformance 게이트웨이 SKU를 사용했습니다. 옵션은 Azure(큰 인스턴스)의 SAP HANA에 지원되는 유일한 게이트웨이 SKU인 HighPerformance 또는 UltraPerformance입니다.

> [!IMPORTANT]
> Type II classs SKU의 HANA 큰 인스턴스의 경우, UltraPerformance 게이트웨이 SKU 사용은 필수적입니다.

**VNet 연결**

이제 Azure VNet에는 ExpressRoute 게이트웨이가 있으므로 Microsoft에서 제공하는 권한 부여 정보를 사용하여 이 연결을 위해 만든 Azure(큰 인스턴스) ExpressRoute 회로에 대한 SAP HANA에 ExpressRoute 게이트웨이를 연결합니다. 이 단계는 Azure Portal 또는 PowerShell을 사용하여 수행할 수 있습니다. 포털을 사용하는 것이 좋지만 PowerShell 지침은 다음과 같습니다. 

- 각 연결에 대해 다른 AuthGUID를 사용하여 각 VNet 게이트웨이에 대해 다음 명령을 실행합니다. 다음 스크립트에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 VNet 및 해당 게이트웨이에 지정됩니다. 다른 Azure VNet을 추가하려면 HANA 큰 인스턴스를 Azure에 연결하는 ExpressRoute 회로에 대해 다른 AuthID를 가져와야 합니다. 

```PowerShell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
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

게이트웨이를 구독과 연결된 여러 ExpressRoute 회로에 연결하려는 경우 이 단계를 두 번 이상 실행해야 합니다. 예를 들어 VNet을 온-프레미스 네트워크에 연결하는 ExpressRoute 회로에 동일한 VNet 게이트웨이를 연결해야 할 가능성이 높습니다.

**다음 단계**

- [HLI에 대한 추가 네트워크 요구 사항](hana-additional-network-requirements.md)을 참조하세요.