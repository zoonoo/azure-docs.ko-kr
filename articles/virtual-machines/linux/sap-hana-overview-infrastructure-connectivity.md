---
title: "Azure(큰 인스턴스)의 SAP HANA에 대한 인프라 및 연결 | Microsoft Docs"
description: "Azure(큰 인스턴스)의 SAP HANA를 사용하도록 필수 연결 인프라를 구성합니다."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 4b01305ea1fc033de64ed2e4fb7e4c9f3d16d394
ms.lasthandoff: 03/01/2017


---
# <a name="sap-hana-large-instances-infrastructure-and-connectivity-on-azure"></a>Azure(큰 인스턴스)의 SAP HANA 인프라 및 연결 

사용자와 Microsoft 엔터프라이즈 계정 팀 간에 Azure(큰 인스턴스)의 SAP HANA 구매를 최종적으로 마무리한 후에 다음과 같은 정보가 필요합니다.

- 고객 이름
- 비즈니스 연락처 정보(전자 메일 주소 및 전화 번호 포함)
- 기술 담당자 정보(전자 메일 주소 및 전화 번호 포함)
- 기술 네트워킹 담당자 정보(전자 메일 주소 및 전화 번호 포함)
- Azure 배포 지역(2016년 9월부터 미국 서부 또는 미국 동부)
- Azure(큰 인스턴스) SKU(구성)에서 SAP HANA 확인
- 다음에 배포되는 모든 Azure 지역의 경우:
  - P2P 연결에 대한 /29 IP 주소 범위
  - CIDR 블록(HANA 큰 인스턴스 NAT 풀에 사용. /24 권장)
- HANA 큰 인스턴스에 연결된 모든 Azure VNet의 경우 Azure 지역과 독립적입니다.
  - 하나 이상의 /28 또는/27 IP 주소 범위(고객 VNet 게이트웨이 서브넷의 경우)
  - 하나 이상의 CIDR 블록(고객 VNet 테넌트 서브넷. /24 권장)
- HANA 큰 인스턴스 시스템 각각에 대한 데이터:
  - 원하는 호스트 이름
  - NAT 풀에서 원하는 IP 주소
- Azure HANA Large Instances의 SAP HANA를 직접 연결하는 Azure 구독의 Azure 구독 번호
- SAP HANA 인스턴스의 SAP HANA SID 이름(필요한 SAP HANA 관련 디스크 볼륨을 만드는 데 필수)

정보를 제공한 후에 Microsoft에서는 Azure(큰 인스턴스)의 SAP HANA를 프로비전합니다.

네트워킹 설치 정보는 다음을 목적으로 사용자에게 제공됩니다.

- HANA 큰 인스턴스에 Azure VNet을 연결하는 ExpressRoute 회로에 Azure VNet 연결
  - Azure Resource Manager의 경우:
     - 권한 부여 키
     - ExpressRoute PeerID
- 설정된 ExpressRoute 회로 및 Azure VNet을 사용하여 HANA 큰 인스턴스에 액세스

## <a name="creating-an-azure-vnet"></a>Azure VNet 만들기

이 Azure VNet은 Azure Resource Manager 배포 모델을 사용하여 만들어야 합니다. 일반적으로 ASM으로 알려진 이전 Azure 배포 모델은 이 솔루션에 지원되지 않습니다.

만들어진 Azure VNet에는 하나 이상의 테넌트 서브넷 및 게이트웨이 서브넷이 있어야 합니다. Microsoft에 지정하고 제출한 대로 할당된 IP 주소 범위여야 합니다.

> [!IMPORTANT] 
> 테넌트 및 게이트웨이 주소 블록은 Azure 구독의 VNet에 할당되어야 합니다. P2P 및 NAT 풀 주소 블록은 Azure 구독 외에도 존재하기 때문에 VNet 및 서브넷 주소 공간과 구분되어야 합니다.

다중 테넌트 서브넷(연속되지 않은 주소 범위도 활용)을 사용할 수 있지만 앞에서 설명한 대로 이러한 주소 범위를 Microsoft에 미리 제출해야 합니다.

이러한 테넌트 서브넷에 원하는 모든 명명 표준을 사용할 수 있습니다. 그러나 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로에 연결되는 **각 VNet에는 항상 하나의 게이트웨이 서브넷만이 있어야 합니다**. 그리고 **이 게이트웨이 서브넷의 이름은 항상 &quot;GatewaySubnet&quot;**으로 지정하여 ExpressRoute 게이트웨이를 제대로 배치해야 합니다.

> [!WARNING] 
> 게이트웨이 서브넷의 이름은 반드시 &quot;GatewaySubnet&quot;이어야 합니다.

Azure Portal, PowerShell, Azure 템플릿 또는 Azure CLI를 사용하여 VNet을 만들 수 있습니다([Azure Portal을 사용하여 가상 네트워크 만들기](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 참조).

## <a name="creating-a-gateway-subnet"></a>게이트웨이 서브넷 만들기

Azure VNet을 만들면 ExpressRoute 게이트웨이를 VNet에 만들어서 큰 인스턴스 스탬프의 고객 테넌트에 연결되는 ExpressRoute 회로에 VNet을 연결해야 합니다.

> [!NOTE] 
> 새 게이트웨이가 지정된 Azure 구독에서 생성된 다음 지정된 Azure VNet에 연결되기 때문에 이 단계를 완료하는 데 최대 30분 정도가 걸릴 수 있습니다.

게이트웨이가 이미 있는 경우 ExpressRoute 게이트웨이인지 여부를 확인합니다. 그렇지 않으면 게이트웨이를 삭제하고 ExpressRoute 게이트웨이로 다시 만들어야 합니다. ExpressRoute 게이트웨이가 이미 설정된 경우 Azure VNet이 온-프레미스 연결성을 위해 ExpressRoute 회로에 이미 연결되어 있으므로 아래 VNet 연결 섹션을 진행합니다.

- (새로운) [Azure Portal](https://portal.azure.com/) 또는 PowerShell을 사용하여 VNet에 연결된 ExpressRoute VPN Gateway를 만듭니다.
  - Azure Portal을 사용하는 경우 새 **가상 네트워크 게이트웨이**를 추가한 다음 **ExpressRoute**를 게이트웨이 유형으로 선택합니다.
  - PowerShell을 대신 선택하는 경우 먼저 최신 [Azure PowerShell SDK](https://azure.microsoft.com/downloads/)를 다운로드하고 사용하여 최적의 환경을 제공합니다. 다음 명령은 ExpressRoute 게이트웨이를 만듭니다. _$_ 뒤의 텍스트는 사용자 정보로 업데이트해야 하는 사용자 정의 변수입니다.

```
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
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

이 예제에서는 HighPerformance 게이트웨이 SKU를 사용했습니다. 옵션은 Azure(큰 인스턴스)의 SAP HANA에 지원되는 유일한 게이트웨이 SKU인 HighPerformance 또는 UltraPerformance입니다.

## <a name="linking-vnets"></a>VNet 연결

이제 Azure VNet에는 ExpressRoute 게이트웨이가 있으므로 Microsoft에서 제공하는 권한 부여 정보를 사용하여 이 연결을 위해 만든 Azure(큰 인스턴스) ExpressRoute 회로에 대한 SAP HANA에 ExpressRoute 게이트웨이를 연결합니다. 이 작업은 PowerShell을 사용해야만 수행할 수 있습니다(현재 Azure Portal을 통해 지원되지 않음).

- 각 연결에 대해 다른 AuthGUID를 사용하여 각 VNet 게이트웨이에 대해 다음을 수행합니다. 아래에 표시된 처음 두 항목은 Microsoft에서 제공하는 정보에서 가져옵니다. 또한 AuthGUID는 모든 VNet 및 해당 게이트웨이에 지정됩니다.

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
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

게이트웨이를 구독과 연결된 다른 여러 ExpressRoute 회로에 연결하려는 경우 이 단계를 두 번 이상 실행해야 합니다.

## <a name="adding-more-ip-addresses-or-subnets"></a>더 많은 IP 주소 또는 서브넷 추가

IP 주소 또는 서브넷을 추가하는 경우 Azure Portal, PowerShell 또는 CLI 중 하나를 사용합니다.

Azure의 SAP HANA Service Management를 사용하여 추가 IP 주소 공간 범위를 아직 선언하지 않은 경우 추가하도록 하는 Azure 지원 요청을 엽니다. 확인을 받은 후에 다음 단계를 수행합니다.

Azure Portal에서 추가 서브넷을 만들려면 [Azure Portal을 사용하여 가상 네트워크 만들기](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하고 PowerShell에서 추가 서브넷을 만들려면 [PowerShell을 사용하여 가상 네트워크 만들기](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="adding-vnets"></a>VNet 추가

하나 이상의 Azure VNet을 처음 연결한 후에 Azure(큰 인스턴스)의 SAP HANA에 액세스하는 추가 항목을 추가할 수 있습니다. 먼저 Azure 지원 요청을 제출합니다. 해당 요청에서 특정 Azure 배포를 식별하는 정보 및 추가 Azure VNet의 테넌트 서브넷 및 게이트웨이 서브넷에 대한 IP 주소 공간 범위를 모두 포함합니다. Azure의 SAP HANA Service Management에서는 추가 VNet 및 ExpressRoute를 연결해야 하는 데 필요한 정보를 제공합니다.

새 Azure VNet을 추가하는 단계:

1. 온보딩 프로세스의 첫 번째 단계를 완성하고 위의 _Azure VNet 만들기_ 섹션을 참조하세요.
2. 온보딩 프로세스의 두 번째 단계를 완성하고 위의 _게이트웨이 서브넷 만들기_ 섹션을 참조하세요.
3. 새 VNet에 대한 정보를 사용하여 Azure 지원 요청을 열고 추가 VNet을 HANA 큰 인스턴스 ExpressRoute 회로에 연결하는 새 권한 부여 키를 요청합니다.
4. 인증이 완료되었다고 알림을 받으면 Microsoft에서 제공되는 권한 부여 정보를 사용하여 온보딩 프로세스에서 세 번째 단계를 완료합니다. 위의 _VNet 연결_ 섹션을 참조하세요.

## <a name="increasing-expressroute-circuit-bandwidth"></a>ExpressRoute 회로 대역폭 증가

Azure의 SAP HANA Service Management에 문의하세요. Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로 대역폭을 증가시켜야 하는 경우 Azure 지원 요청을 만듭니다. (최대 10Gbps의 단일 회로 대역폭으로 증가하도록 요청할 수 있습니다.) 그러면 작업이 완료된 후에 Azure에서 이보다 속도를 빠르게 하기 위해 필요한 추가 작업 없음이라는 알림이 표시됩니다.

## <a name="adding-an-additional-expressroute-circuit"></a>추가 ExpressRoute 회로 추가

Azure의 SAP HANA Service Management를 참조하여 추가 ExpressRoute 회로가 필요하다는 조언을 받은 경우 새 ExpressRoute 회로를 만들도록 Azure 지원에 요청하고 연결하는 인증 정보를 가져옵니다. VNet에 사용될 주소 공간은 Azure의 SAP HANA Service Management가 권한 부여를 제공하기 위해 요청하기 전에 정의되어야 합니다.

새 회로가 만들어지고 Azure의 SAP HANA Service Management 구성이 완료되면 진행해야 하는 정보를 포함한 알림을 받게 됩니다. 새 VNet을 만들고 이 추가 회로에 연결하기 위해 위에 제공된 단계를 수행합니다. 이미 다른 ExpressRoute 회로에 연결된 경우 Azure VNet을 이 추가 회로에 연결할 수 없습니다.

## <a name="deleting-a-subnet"></a>서브넷 삭제

VNet 서브넷을 제거하기 위해 Azure Portal, PowerShell 또는 CLI 중 하나를 사용할 수 있습니다. 주소 공간을 제거하는 경우 Azure(큰 인스턴스)의 SAP HANA Service Management는 Azure의 SAP HANA가 통신할 수 있는 범위에서 해당 항목을 제거하기 위해 주소 공간 변경 내용에 대한 알림을 받아야 합니다.

아직 서브넷을 제거하는 방법에 대한 특정 전용 Azure.com 지침은 없지만 서브넷을 제거하는 프로세스는 서브넷을 추가하는 프로세스와 반대입니다. 서브넷을 만드는 방법에 대한 자세한 내용은 Azure Portal 문서인 [Azure Portal을 사용하여 가상 네트워크 만들기](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

## <a name="deleting-a-vnet"></a>VNet 삭제

VNet을 삭제하는 경우 Azure Portal, PowerShell 또는 CLI를 사용합니다. Azure(큰 인스턴스)의 SAP HANA Service Management는 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로에 대한 기존 권한 부여를 제거하고 HANA 큰 인스턴스와의 통신에 대한 IP 주소 범위(테넌트 및 게이트웨이 범위 모두)를 제거합니다.

VNet을 제거하면 제거될 IP 주소 공간 범위를 제공하는 Azure 지원 요청이 열립니다.

아직 VNet을 제거하는 방법에 대한 특정 전용 Azure.com 지침은 없지만 VNet을 제거하는 프로세스는 위에 설명된 VNet을 추가하는 프로세스와 반대입니다. VNet을 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [PowerShell을 사용하여 가상 네트워크 만들기](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서를 참조하세요.

모든 항목이 제거되었는지 확인하려면 다음 항목을 삭제합니다.

- **Azure Resource Manager의 경우:** ExpressRoute 연결, VNet 게이트웨이, VNet 게이트웨이 공용 IP 및 VNet
- **클래식 VM의 경우:** VNet 게이트웨이 및 VNet

## <a name="deleting-an-expressroute-circuit"></a>ExpressRoute 회로 삭제

추가 Azure(큰 인스턴스)의 SAP HANA ExpressRoute 회로를 제거하려면 Azure의 SAP HANA Service Management를 통해 Azure 지원 요청을 열고 회로를 삭제하도록 요청합니다. Azure 구독 내에서 필요에 따라 VNet을 삭제하거나 유지할 수 있습니다. 그러나 HANA 큰 인스턴스 ExpressRoute 회로와 연결된 VNet 게이트웨이 간에 연결을 삭제하거나(Azure Resource Manager의 경우) 연결을 해제해야(클래식의 경우) 합니다.

VNet도 제거하려면 위의 섹션에서 VNet 삭제에 대한 지침을 따릅니다.



