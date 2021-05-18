---
title: 가상 네트워크에서 Azure(대규모 인스턴스)의 SAP HANA에 대한 연결 설정 | Microsoft Docs
description: 가상 네트워크에서 Azure(대규모 인스턴스)의 SAP HANA를 사용하기 위한 연결을 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 540247752be27af268a0485ea9eb68d121a25240
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775308"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>가상 네트워크를 HANA 대규모 인스턴스에 연결

Azure 가상 네트워크를 만든 후 해당 네트워크를 Azure 대규모 인스턴스의 SAP HANA에 연결합니다. 가상 네트워크에 Azure ExpressRoute 게이트웨이를 만듭니다. 이 게이트웨이를 사용하면 HANA 대규모 인스턴스 스탬프에서 고객 테넌트에 연결하는 ExpressRoute 회로에 가상 네트워크를 연결할 수 있습니다.

> [!NOTE] 
> 이 단계를 완료하려면 최대 30분이 걸릴 수 있습니다. 새 게이트웨이는 지정된 Azure 구독에 만들어진 다음, 지정된 Azure 가상 네트워크에 연결됩니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. ExpressRoute 게이트웨이가 아닌 경우 게이트웨이를 삭제하고 ExpressRoute 게이트웨이로 다시 만듭니다. ExpressRoute 게이트웨이가 이미 설정된 경우 이 문서의 다음 섹션 "가상 네트워크 연결"을 참조하세요. 

- [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 가상 네트워크에 연결된 ExpressRoute VPN 게이트웨이를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **가상 네트워크 게이트웨이** 를 추가한 다음, 게이트웨이 유형으로 **ExpressRoute** 를 선택합니다.
  - PowerShell을 사용하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 사용합니다. 
 
다음 명령은 ExpressRoute 게이트웨이를 만듭니다. _$_ 뒤의 텍스트는 사용자 정보로 업데이트해야 하는 사용자 정의 변수입니다.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "UltraPerformance" # Supported values for HANA large instances are: UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Azure(대규모 인스턴스)의 SAP HANA에 대해 지원되는 유일한 게이트웨이 SKU는 **UltraPerformance** 입니다.

## <a name="link-virtual-networks"></a>가상 네트워크 연결

이제 Azure 가상 네트워크에 ExpressRoute 게이트웨이가 만들어졌습니다. Microsoft에서 제공하는 권한 부여 정보를 사용하여 ExpressRoute 게이트웨이를 SAP HANA 대규모 인스턴스 ExpressRoute 회로에 연결합니다. Azure Portal 또는 PowerShell을 사용하여 연결할 수 있습니다. PowerShell 지침은 다음과 같습니다. 

각 연결에 대해 서로 다른 AuthGUID를 사용하여 각 ExpressRoute 게이트웨이에 대해 다음 명령을 실행합니다. 다음 스크립트에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 가상 네트워크 및 해당 게이트웨이에 지정됩니다. 다른 Azure 가상 네트워크를 추가하려는 경우 HANA 대규모 인스턴스를 Microsoft의 Azure에 연결하는 ExpressRoute 회로에 대한 다른 AuthID를 가져와야 합니다. 

```powershell
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
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> New-AzVirtualNetworkGatewayConnection 명령의 마지막 매개 변수인 **ExpressRouteGatewayBypass** 는 ExpressRoute 빠른 경로를 사용하도록 설정하는 새 매개 변수입니다. HANA 대규모 인스턴스 단위와 Azure VM 간의 네트워크 대기 시간을 줄이는 기능입니다. 이 기능은 2019년 5월에 추가되었습니다. 자세한 내용은 [SAP HANA(대규모 인스턴스) 네트워크 아키텍처](./hana-network-architecture.md) 문서를 참조하세요. 명령을 실행하기 전에 최신 버전의 PowerShell cmdlet을 실행하고 있는지 확인합니다.

게이트웨이를 구독과 연결된 두 개 이상의 ExpressRoute 회로에 연결하려면 이 단계를 두 번 이상 실행해야 할 수 있습니다. 예를 들어 가상 네트워크를 온-프레미스 네트워크에 연결하는 ExpressRoute 회로에 동일한 가상 네트워크 게이트웨이를 연결해야 할 가능성이 높습니다.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>기존 HANA 대규모 인스턴스 ExpressRoute 회로에 대한 ExpressRoute 빠른 경로 적용
지금까지 살펴본 설명서에서는 HANA 대규모 인스턴스 배포를 사용하여 만든 새 ExpressRoute를 Azure 가상 네트워크 중 하나의 Azure ExpressRoute 게이트웨이에 연결하는 방법을 설명했습니다. 그러나 많은 고객이 이미 ExpressRoute 회로를 이미 설정했으며 가상 네트워크를 HANA 대규모 인스턴스에 이미 연결했습니다. 새로운 ExpressRoute 빠른 경로는 네트워크 대기 시간을 줄이므로 변경 내용을 적용하여 이 기능을 사용하는 것이 좋습니다. 새 ExpreesRoute 회로를 연결하고 기존 ExpressRoute 회로를 변경하는 명령은 동일합니다. 따라서 기존 회로를 사용하도록 변경하려면 이 일련의 PowerShell 명령을 실행해야 합니다. 

```powershell
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
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

ExpressRoute 빠른 경로 기능을 사용하려면 위에 표시된 대로 마지막 매개 변수를 추가하는 것이 중요합니다.


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
두 시나리오 중 하나 또는 둘 다에 대해 Global Reach를 사용하도록 설정합니다.

 - 추가 프록시 또는 방화벽이 없는 HANA 시스템 복제
 - 시스템 복사 또는 시스템 새로 고침을 수행하기 위해 서로 다른 두 지역의 HANA 대규모 인스턴스 단위 간 백업 복사

다음 사항을 고려해야 합니다.

- /29 주소 공간의 주소 공간 범위를 제공해야 합니다. 이 주소 범위는 지금까지 HANA 대규모 인스턴스를 Azure에 연결하는 데 사용한 다른 주소 공간 범위와 겹치지 않을 수 있으며 Azure 또는 온-프레미스의 다른 곳에서 사용한 IP 주소 범위와 겹치지 않을 수 있습니다.
- 온-프레미스 경로를 HANA 대규모 인스턴스에 알리는 데 사용할 수 있는 ASN(자율 시스템 번호)에는 제한이 있습니다. 온-프레미스는 65000 ~ 65020 또는 65515 범위의 비공개 ASN이 있는 경로를 알리지 않아야 합니다. 
- HANA 대규모 인스턴스에 온-프레미스 직접 액세스를 연결하는 시나리오의 경우 Azure에 연결하는 회로에 대한 요금을 계산해야 합니다. 가격에 대해서는 [Global Reach 추가 기능](https://azure.microsoft.com/pricing/details/expressroute/)에 대한 가격을 확인하세요.

배포에 적용되는 시나리오 중 하나 또는 둘 다를 가져오려면 [HANA 대규모 인스턴스에 대한 지원 요청 열기](./hana-li-portal.md#open-a-support-request-for-hana-large-instances)에 설명된 대로 Azure에서 지원 메시지를 엽니다.

Microsoft가 요청에 따라 라우팅하고 실행할 수 있도록 하는 데 필요한 데이터와 키워드는 다음과 같습니다.

- 서비스: SAP HANA 대규모 인스턴스
- 문제 유형: 구성 및 설정
- 문제 하위 유형: 내 문제가 위에 나열되지 않음
- 제목 '내 네트워크 수정 - Global Reach 범위 추가'
- 세부 정보: 'HANA 대규모 인스턴스에 Global Reach 추가 또는'HANA 대규모 인스턴스 테넌트에 온-프레미스에 대한 Global Reach 추가
- HANA 대규모 인스턴스 테넌트 사례로 HANA 대규모 인스턴스에 대한 추가 세부 정보: 연결할 두 테넌트가 있는 **두 Azure 지역** 을 정의하고 **/29 IP 주소 범위** 를 제출해야 합니다.
- HANA 대규모 인스턴스 테넌트 사례로 온-프레미스에 대한 추가 세부 정보: 직접 연결하려는 HANA 대규모 인스턴스 테넌트가 배포된 **Azure 지역** 을 정의해야 합니다. 또한 온-프레미스와 Azure 간에 ExpressRoute 회로를 설정할 때 받은 **Auth GUID** 및 **서킷 피어 ID** 를 제공해야 합니다. 또한 **ASN** 의 이름을 지정해야 합니다. 마지막 결과물은 ExpressRoute Global Reach에 대한 **/29 IP 주소 범위** 입니다.

> [!NOTE]
> 두 경우를 모두 처리하려면 지금까지 사용된 다른 IP 주소 범위와 겹치지 않는 두 개의 다른 /29 IP 주소 범위를 제공해야 합니다. 




## <a name="next-steps"></a>다음 단계

- [HLI에 대한 추가 네트워크 요구 사항](hana-additional-network-requirements.md)
