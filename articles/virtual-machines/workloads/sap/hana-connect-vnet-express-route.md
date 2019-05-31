---
title: 가상 네트워크에서 SAP HANA on Azure (큰 인스턴스)를 설정 하는 연결 | Microsoft Docs
description: 연결 가상 네트워크에서 Azure (큰 인스턴스)에서 SAP HANA를 사용 하도록 설정 합니다.
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
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9cd290f9e5e7819f3dffa2dd1efea9cd0028fc2c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239460"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>가상 네트워크를 HANA 대규모 인스턴스에 연결

Azure 가상 네트워크를 만든 후 해당 네트워크를 Azure 대규모 인스턴스의 SAP HANA에 연결합니다. 가상 네트워크에 Azure ExpressRoute 게이트웨이를 만듭니다. 이 게이트웨이 사용 하면 HANA 큰 인스턴스 스탬프의 고객 테 넌 트에 연결 하는 ExpressRoute 회로 가상 네트워크를 연결할 수 있습니다.

> [!NOTE] 
> 이 단계를 완료하려면 최대 30분이 걸릴 수 있습니다. 새 게이트웨이는 지정된 Azure 구독에 만들어진 다음, 지정된 Azure 가상 네트워크에 연결됩니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. ExpressRoute 게이트웨이 없는 경우 게이트웨이 삭제 하 고 ExpressRoute 게이트웨이로 다시 만들어야 합니다. ExpressRoute 게이트웨이가 이미 설정된 경우 이 문서의 다음 섹션 "가상 네트워크 연결"을 참조하세요. 

- [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 가상 네트워크에 연결된 ExpressRoute VPN 게이트웨이를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **가상 네트워크 게이트웨이**를 추가한 다음, 게이트웨이 유형으로 **ExpressRoute**를 선택합니다.
  - PowerShell을 사용하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하여 사용합니다. 
 
다음 명령은 ExpressRoute 게이트웨이를 만듭니다. 뒤의 텍스트를 _$_ 자신의 특정 정보로 업데이트 되어야 하는 사용자 정의 변수입니다.

```powershell
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

이 예제에서는 HighPerformance 게이트웨이 SKU를 사용했습니다. 옵션은 Azure(대규모 인스턴스)의 SAP HANA에 대해 지원되는 유일한 게이트웨이 SKU인 HighPerformance 또는 UltraPerformance입니다.

> [!IMPORTANT]
> Type II 클래스 SKU의 HANA 대규모 인스턴스의 경우, UltraPerformance 게이트웨이 SKU를 사용해야 합니다.

## <a name="link-virtual-networks"></a>가상 네트워크 연결

이제 Azure 가상 네트워크에 ExpressRoute 게이트웨이가 만들어졌습니다. Microsoft에서 제공 하는 권한 부여 정보를 사용 하 여 SAP HANA 큰 인스턴스 ExpressRoute 회로에 ExpressRoute 게이트웨이 연결 합니다. Azure Portal 또는 PowerShell을 사용하여 연결할 수 있습니다. PowerShell 지침에는 다음과 같습니다. 

각 연결에 대해 다른 AuthGUID를 사용 하 여 ExpressRoute 게이트웨이 각각에 대해 다음 명령을 실행 합니다. 다음 스크립트에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 가상 네트워크 및 해당 게이트웨이에 지정됩니다. 다른 Azure 가상 네트워크를 추가 하려는 경우 Microsoft에서 Azure로 HANA 큰 인스턴스에 연결 하는 ExpressRoute 회로 대해 다른 AuthID를 가져올 해야 합니다. 

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
> 새로 만들기-AzVirtualNetworkGatewayConnection 명령의 마지막 매개 변수 **ExpressRouteGatewayBypass** ExpressRoute 빠른 경로 사용 하도록 설정 하는 새 매개 변수입니다. HANA 큰 인스턴스 단위와 Azure Vm 간에 네트워크 대기 시간을 줄여 주는 기능입니다. 2019 년 5 월에에서 기능 추가 되었습니다. 자세한 내용은 문서를 확인 [SAP HANA (큰 인스턴스) 네트워크 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)합니다. 최신 버전의 PowerShell cmdlet 명령을 실행 하기 전에 실행 되 고 있는지 확인 하십시오.

게이트웨이를 구독과 연결된 두 개 이상의 ExpressRoute 회로에 연결하려면 이 단계를 두 번 이상 실행해야 할 수 있습니다. 예를 들어 가상 네트워크를 온-프레미스 네트워크에 연결하는 ExpressRoute 회로에 동일한 가상 네트워크 게이트웨이를 연결해야 할 가능성이 높습니다.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>기존 HANA 큰 인스턴스 ExpressRoute 회로에 ExpressRoute 빠른 경로 적용합니다.
설명서를 지금까지 HANA 큰 인스턴스 배포를 Azure 가상 네트워크 중 하나는 Azure ExpressRoute 게이트웨이 사용 하 여 생성 된 새 ExpressRoute 회로 연결 하는 방법을 설명 합니다. 하지만 많은 고객이 이미 이미 해당 ExpressRoute 회로 설정 하 고 있는 가상 네트워크를 HANA 큰 인스턴스에 이미 연결 합니다. 새 ExpressRoute 빠른 경로 네트워크 대기 시간 절감은,이 기능을 사용 하 여 변경 내용을 적용 하는 것이 좋습니다. 새 ExpreesRoute 회로 연결 하 고 기존 ExpressRoute 회로 변경 하도록 명령 동일 합니다. 이를 사용 하 여 기존 회로 변경 하는 PowerShell 명령이 시퀀스를 실행 해야 하는 결과적으로 

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

ExpressRoute 빠른 경로 기능을 사용 하려면 위에 표시 된 대로 마지막 매개 변수를 추가 하는 중요


## <a name="expressroute-global-reach"></a>ExpressRoute에 대 한 글로벌 정보
두 가지 시나리오 중 하나 또는 모두에 대 한 전역 연결을 사용 하려는:

 - 추가 프록시 또는 방화벽 없이 HANA 시스템 복제
 - 시스템 복사본 또는 시스템 새로 고침을 수행 하려면 두 개의 다른 지역의 HANA 큰 인스턴스 단위 간의 복사 백업

고려할 해야 있습니다.

- / 29 주소 공간 범위를 제공 해야 주소 공간입니다. Azure 또는 온-프레미스의 다른 곳 사용는 주소 범위가 겹치지 않을 수 있습니다 다른 주소 공간 범위를 지금까지 HANA 큰 인스턴스를 Azure에 연결을 사용 하 고 겹치지 않을 수 있는 IP 주소 범위를 사용 하 여 중 하나를 사용 하 여 있습니다.
- HANA 큰 인스턴스에 온-프레미스 경로 보급 하도록 사용할 수 있는 Asn (자치 시스템 번호)에 대 한 제한이 있습니다. 온-프레미스 65000 – 65020 범위의 개인 Asn 사용 하 여 모든 경로 또는 65515 보급 하지 해야 합니다. 
- 온-프레미스에 직접 액세스를 HANA 큰 인스턴스에 연결 하는 시나리오의 경우 Azure에 연결 하는 회로 대 한 요금을 계산 해야 합니다. 가격에 대 한 가격을 확인 [도달 하는 추가 기능을 전역](https://azure.microsoft.com/pricing/details/expressroute/)합니다.

배포에 적용 하는 시나리오 중 하나 또는 모두를 가져오려면 지원 있는 메시지를 열고 Azure에 설명 된 대로 [HANA 큰 인스턴스에 대 한 지원 요청을 여세요.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances)

필요한 데이터 및 라우팅 및 사용자의 요청을 실행 하려면 Microsoft에 대 한 사용 해야 하는 키워드에는 다음과 같습니다.

- 서비스: SAP HANA 큰 인스턴스
- 문제 유형: 구성 및 설정
- 문제가 하위 유형: 문제가 위에 없음
- 주체 '수정' 내 네트워크-글로벌 환경 추가
- 세부 정보: ' HANA 큰 인스턴스 테 넌 트의 글로벌 환경를 HANA 큰 인스턴스를 추가 하거나 ' 온-프레미스에 HANA 큰 인스턴스 테 넌 트에 글로벌 환경 추가 합니다.
- HANA 큰 인스턴스 테 넌 트 사례에 HANA 큰 인스턴스에 대 한 추가 정보: 정의 해야 합니다 **두 개의 Azure 지역** 연결할 두 테 넌 트의 위치 **AND** 제출 해야 하는   **/29 IP 주소 범위**
- 온-프레미스를 HANA 큰 인스턴스 테 넌 트 사례에 대 한 추가 정보: 정의 해야 합니다 **Azure 지역** 에 직접 연결 하려는 HANA 큰 인스턴스 테 넌 트가 배포 된 합니다. 또한 제공 해야 합니다 **Auth GUID** 및 **회로 피어 ID** 온-프레미스와 Azure 간에 ExpressRoute 회로 설정할 때를 수신 하면 합니다. 이름을 지정 해야 하는 또한 하 **ASN**합니다. 마지막 결과물은 **/29 IP 주소 범위** ExpressRoute 글로벌 환경에 대 한 합니다.

> [!NOTE]
> 두 경우 모두 처리 하도록 하려는 경우 제공 하면 두 개의 다른/29 IP 주소 범위가 겹치지 않는 다른 ip 주소 범위 지금까지 사용 합니다. 




## <a name="next-steps"></a>다음 단계

- [HLI에 대한 추가 네트워크 요구 사항](hana-additional-network-requirements.md)
