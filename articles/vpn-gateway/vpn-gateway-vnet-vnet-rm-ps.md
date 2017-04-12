---
title: "다른 VNet에 Azure 가상 네트워크 연결: PowerShell | Microsoft Docs"
description: "이 문서에서는 Azure 리소스 관리자 및 PowerShell을 사용하여 가상 네트워크를 함께 연결하는 과정을 안내합니다."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a4cbc4cd1c48da1120c643892b19692ac583d4c3
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-powershell"></a>PowerShell을 사용하여 VNet 간 연결 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [클래식 - Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [클래식 - 클래식 포털](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
>

이 문서에서는 VPN 게이트웨이를 사용하여 Resource Manager 배포 모델의 VNet 간에 연결을 만드는 단계를 안내합니다. 가상 네트워크는 같은 또는 다른 구독의 같은 지역에 있을 수도 있고 다른 지역에 있을 수도 있습니다.

![v2v 다이어그램](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 간 연결 배포 모델 및 메서드
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

아래 표에서는 현재 사용할 수 있는 배포 모델 및 VNet 간 구성을 위한 메서드를 보여 줍니다. 구성 단계를 포함한 문서를 사용할 수 있는 경우 아래 표에서 관련 링크를 직접 제공합니다. 

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 피어링**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>VNet 간 연결 정보
가상 네트워크를 다른 가상 네트워크에 연결(VNet-VNet)하는 것은 VNet을 온-프레미스 사이트 위치에 연결하는 것과 유사합니다. 두 연결 유형 모두 Azure VPN 게이트웨이를 사용하여 IPsec/IKE를 통한 보안 터널을 제공합니다. 연결하는 VNet은 서로 다른 지역에 있을 수 있습니다. 또는 다른 구독에 있을 수 있습니다. VNet 간 통신을 다중 사이트 구성과 통합할 수도 있습니다. 이렇게 하면 다음 다이어그램에 표시된 것처럼 프레미스 간 연결을 가상 네트워크 간 연결과 결합하는 네트워크 토폴로지를 설정할 수 있습니다.

![연결 정보](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>가상 네트워크에 연결하는 이유
다음과 같은 이유로 가상 네트워크에 연결할 수 있습니다.

* **지역 간 지리적 중복 및 지리적 상태**
  
  * 인터넷 연결 끝점으로 이동하지 않고도 보안 연결을 통해 지역에서 복제 또는 동기화를 직접 설정할 수 있습니다.
  * Azure 트래픽 관리자 및 부하 분산 장치를 사용하여 여러 Azure 지역 간의 지리적 중복을 통해 워크로드의 가용성을 높게 설정할 수 있습니다. 이러한 작업의 한 가지 주요 예는 여러 Azure 지역에 분산된 가용성 그룹을 사용하여 SQL AlwaysOn을 설정하는 것입니다.
* **분리 또는 관리 경계를 가진 지역별 다중 계층 응용 프로그램**
  
  * 같은 지역 내에서 분리 또는 관리 요구 사항 때문에 여러 가상 네트워크가 함께 연결된 다중 계층 응용 프로그램을 설정할 수 있습니다.

### <a name="vnet-to-vnet-considerations"></a>VNet 간 고려 사항
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="which-set-of-steps-should-i-use"></a>어느 단계 집합을 사용해야 합니까?
이 문서에서는 서로 다른 두 집합의 단계를 볼 수 있습니다. 일련의 단계는 [동일한 구독에 상주하는 VNet](#samesub)이고 다른 단계는 [다른 구독에 상주하는 VNet](#difsub)입니다. 둘 사이의 주요 차이점은 동일한 PowerShell 세션 내에서 모든 가상 네트워크 및 게이트웨이 리소스를 생성하고 구성할 수 있는지 여부입니다.

이 문서의 단계는 각 섹션의 시작 부분에 선언된 변수를 사용합니다. 기존 VNet을 이미 사용하는 경우 변수를 수정하여 고유한 환경에 설정을 적용합니다. 

![두 연결 모두](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

## <a name="samesub"></a>같은 구독에 있는 VNet을 연결하는 방법
![v2v 다이어그램](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>시작하기 전에
시작하기 전에 Azure Resource Manager PowerShell cmdlet을 설치해야 합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요.

### <a name="Step1"></a>1단계 - IP 주소 범위 계획
다음 단계에서는 두 개의 가상 네트워크와 해당 게이트웨이 서브넷 및 구성을 만듭니다. 그런 다음 두 VNet 간의 VPN 연결을 만듭니다. 네트워크 구성에 대한 IP 주소 범위를 계획하는 것이 중요합니다. 따라서 VNet 범위 또는 로컬 네트워크 범위가 겹치지 않는지 확인해야 합니다.

예제에서 다음 값을 사용합니다.

**TestVNet1에 대한 값:**

* VNet 이름: TestVNet1
* 리소스 그룹: TestRG1
* 위치: 미국 동부
* TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
* 프런트 엔드: 10.11.0.0/24
* 백 엔드: 10.12.0.0/24
* 게이트웨이 서브넷 = 10.12.255.0/27
* DNS 서버: 8.8.8.8
* 게이트웨이 이름: VNet1GW
* 공용 IP: VNet1GWIP
* VpnType: 경로 기반
* 연결(1 대 4): VNet1 대 VNet4
* 연결(1 대 5): VNet1 대 VNet5
* 연결 유형: VNet 간

**TestVNet4에 대한 값:**

* VNet 이름: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* 프런트 엔드: 10.41.0.0/24
* 백 엔드: 10.42.0.0/24
* 게이트웨이 서브넷: 10.42.255.0/27
* 리소스 그룹: TestRG4
* 위치: 미국 서부
* DNS 서버: 8.8.8.8
* 게이트웨이 이름: VNet4GW
* 공용 IP: VNet4GWIP
* VpnType: 경로 기반
* 연결: VNet4 대 VNet1
* 연결 유형: VNet 간

### <a name="Step2"></a>2단계 - TestVNet1 만들기 및 구성
1. 변수 선언
   
    변수를 선언하여 시작합니다. 이 예제에서는 이 연습에 대한 값을 사용하여 변수를 선언합니다. 대부분의 경우에 값을 고유한 값으로 바꿔야 합니다. 그러나 이 구성 유형에 익숙해지기 위해 단계를 차례로 실행하는 경우 이 변수를 사용할 수 있습니다. 필요한 경우 변수를 수정한 다음 복사하여 PowerShell 콘솔에 붙여 넣습니다.
   
        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"
2. 구독에 연결
   
    PowerShell 모드로 전환하여 Resource Manager cmdlet을 사용합니다. PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.
   
        Login-AzureRmAccount
   
    계정에 대한 구독을 확인합니다.
   
        Get-AzureRmSubscription 
   
    사용할 구독을 지정합니다.
   
        Select-AzureRmSubscription -SubscriptionName $Sub1
3. 새 리소스 그룹 만들기
   
        New-AzureRmResourceGroup -Name $RG1 -Location $Location1
4. TestVNet1에 대한 서브넷 구성 만들기
   
    이 예제에서는 TestVNet1이라는 가상 네트워크와 GatewaySubnet, FrontEnd 및 Backend라는 세 개의 서브넷을 만듭니다. 값을 대체할 때 언제나 게이트웨이 서브넷 이름을 GatewaySubnet라고 명시적으로 지정해야 합니다. 다른 이름을 지정하는 경우 게이트웨이 만들기가 실패합니다. 
   
    다음 예제에서는 앞에서 설정한 변수를 사용합니다. 이 예제에서 게이트웨이 서브넷은 /27을 사용합니다. 게이트웨이 서브넷을 /29만큼 작게 만들 수 있지만 적어도 /28 또는 /27을 선택하여 더 많은 주소를 포함하는 큰 서브넷을 만드는 것이 좋습니다. 이렇게 하면 나중에 필요할 수도 있는 추가 구성에 맞게 충분히 주소를 사용할 수 있습니다. 
   
        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
5. TestVNet1 만들기
   
        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
6. 공용 IP 주소 요청
   
    VNet용으로 만들 게이트웨이에 할당할 공용 IP 주소를 요청합니다. AllocationMethod가 동적인지 확인합니다. 사용할 IP 주소를 지정할 수는 없습니다. IP 주소는 게이트웨이에 동적으로 할당됩니다. 
   
        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic
7. 게이트웨이 구성 만들기
   
    게이트웨이 구성은 사용할 공용 IP 주소 및 서브넷을 정의합니다. 예제를 사용하여 게이트웨이 구성을 만듭니다. 
   
        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1
8. TestVNet1에 대한 게이트웨이 만들기
   
    이 단계에서는 TestVNet1용 가상 네트워크 게이트웨이를 만듭니다. VNet-VNet 구성에는 RouteBased VpnType이 필요합니다. 게이트웨이 만들기는 꽤 시간이 걸릴 수 있습니다(완료되려면 45분 이상).
   
        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>3단계 - TestVNet4 만들기 및 구성
TestVNet1 구성이 끝나면 TestVNet4를 만듭니다. 아래 단계에 따라 필요한 경우 값을 사용자의 고유한 값으로 바꾸십시오. 이 단계는 같은 구독에 있기 때문에 같은 PowerShell 세션 내에서 수행할 수 있습니다.

1. 변수 선언
   
    값을 구성에 사용할 값으로 바꾸어야 합니다.
   
        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"
   
    계속하기 전에 여전히 구독 1에 연결되어 있는지 확인합니다.
2. 새 리소스 그룹 만들기
   
        New-AzureRmResourceGroup -Name $RG4 -Location $Location4
3. TestVNet4에 대한 서브넷 구성 만들기
   
        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
4. TestVNet4 만들기
   
        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
5. 공용 IP 주소 요청
   
        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic
6. 게이트웨이 구성 만들기
   
        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
7. TestVNet4 게이트웨이 만들기
   
        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>4단계 - 게이트웨이 연결
1. 두 개의 가상 네트워크 게이트웨이 모두 가져오기
   
    이 예제에서는 두 게이트웨이가 동일한 구독에 있기 때문에 동일한 PowerShell 세션에서 이 단계를 완료할 수 있습니다.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
2. TestVNet1 대 TestVNet4 연결 만들기
   
    이 단계에서는 TestVNet1에서 TestVNet4까지 연결을 만듭니다. 예제에서 참조된 공유 키를 볼 수 있습니다. 공유 키에 대해 고유한 값을 사용할 수 있습니다. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
3. TestVNet4 대 TestVNet1 연결 만들기
   
    이 단계는 TestVNet4에서 TestVNet1까지 연결을 만드는 점을 제외하면 위의 비슷합니다. 공유된 키가 일치하는지 확인합니다.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   
    몇 분 후 연결이 설정됩니다.
4. 연결을 확인합니다. [연결을 확인하는 방법](#verify)섹션을 참조하세요.

## <a name="difsub"></a>다른 구독에 있는 VNet을 연결하는 방법
![v2v 다이어그램](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

이 시나리오에서는 TestVNet1 및 TestVNet5를 연결합니다. TestVNet1 및 TestVNet5는 다른 구독에 상주합니다. 이 구성 단계에서는 TestVNet1을 TestVNet5에 연결하기 위해 VNet 간 연결을 추가합니다. 

여기서 차이점은 일부 구성 단계를 두 번째 구독 환경에서 별도의 PowerShell 세션으로 수행해야 한다는 것입니다. 특히 두 구독이 다른 조직에 속한 경우입니다. 

지침은 위에 나열된 이전 단계에서 계속합니다. TestVNet1 및 TestVNet1용 VPN Gateway를 만들고 구성하려면 [1단계](#Step1) 및 [2단계](#Step2)를 완료해야 합니다. 1단계와 2단계가 완료되면 5단계를 계속하여 TestVNet5를 만듭니다.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>5단계-추가 IP 주소 범위 확인
새 가상 네트워크 TestVNet5의 IP 주소 공간이 VNet 범위 또는 로컬 네트워크 게이트웨이 범위와 겹치지 않는지 확인해야 합니다. 

이 예제에서 가상 네트워크는 서로 다른 조직에 속할 수 있습니다. 이 연습에서는 TestVNet5에 대해 다음 값을 사용할 수 있습니다.

**TestVNet5에 대한 값:**

* VNet 이름: TestVNet5
* 리소스 그룹: TestRG5
* 위치: 일본 동부
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* 프런트 엔드: 10.51.0.0/24
* 백 엔드: 10.52.0.0/24
* 게이트웨이 서브넷: 10.52.255.0.0/27
* DNS 서버: 8.8.8.8
* 게이트웨이 이름: VNet5GW
* 공용 IP: VNet5GWIP
* VpnType: 경로 기반
* 연결: VNet5 대 VNet1
* 연결 유형: VNet 간

**TestVNet1에 대한 추가 값:**

* 연결: VNet1 대 VNet5

### <a name="step-6---create-and-configure-testvnet5"></a>6단계 - TestVNet5 만들기 및 구성
이 단계는 새 구독의 상황에서 수행해야 합니다. 이 부분은 구독을 소유한 다른 조직의 관리자가 수행할 수 있습니다.

1. 변수 선언
   
    값을 구성에 사용할 값으로 바꾸어야 합니다.
   
        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"
2. 구독 5에 연결
   
    PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 샘플을 사용합니다.
   
        Login-AzureRmAccount
   
    계정에 대한 구독을 확인합니다.
   
        Get-AzureRmSubscription 
   
    사용할 구독을 지정합니다.
   
        Select-AzureRmSubscription -SubscriptionName $Sub5
3. 새 리소스 그룹 만들기
   
        New-AzureRmResourceGroup -Name $RG5 -Location $Location5
4. TestVNet4에 대한 서브넷 구성 만들기
   
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
5. TestVNet5 만들기
   
        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
6. 공용 IP 주소 요청
   
        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic
7. 게이트웨이 구성 만들기
   
        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
8. TestVNet5 게이트웨이 만들기
   
        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>7단계 - 게이트웨이 연결
이 예제에서는 게이트웨이가 다른 구독에 있기 때문에 이 단계를 [구독 1] 및 [구독 5]로 표시된 두 개의 PowerShell 세션으로 분할했습니다.

1. **[구독 1]** 구독 1에 대한 가상 네트워크 게이트웨이 가져오기
   
    로그인하고 구독 1에 연결해야 합니다.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   
    다음 요소의 출력을 복사하고 전자 메일 또는 다른 방법을 통해 구독 5의 관리자에게 보냅니다.
   
        $vnet1gw.Name
        $vnet1gw.Id
   
    이 두 요소는 다음 예제 출력과 유사한 값을 갖게 됩니다.
   
        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
2. **[구독 5]** 구독 5에 대한 가상 네트워크 게이트웨이 가져오기
   
    로그인하고 구독 5에 연결해야 합니다.
   
        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   
    다음 요소의 출력을 복사하고 전자 메일 또는 다른 방법을 통해 구독 1의 관리자에게 보냅니다.
   
        $vnet5gw.Name
        $vnet5gw.Id
   
    이 두 요소는 다음 예제 출력과 유사한 값을 갖게 됩니다.
   
        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
3. **[구독 1]** TestVNet1에서 TestVNet5에 연결 만들기
   
    이 단계에서는 TestVNet1에서 TestVNet5까지 연결을 만듭니다. 여기서 차이점은 $vnet5gw가 다른 구독에 있기 때문에 직접 가져올 수 없다는 것입니다. 위의 단계에서 구독 1에서 전달한 값을 사용하여 새 PowerShell 개체를 만들어야 합니다. 아래 예제를 사용하세요. 이름, ID 및 공유 키를 사용자의 고유한 값으로 바꿉니다. 중요한 점은 두 연결에서 모두 공유 키가 일치해야 한다는 것입니다. 연결 만들기는 완료하는 데 꽤 오래 걸릴 수 있습니다.
   
    구독 1에 연결해야 합니다. 
   
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
4. **[구독 5]** TestVNet5에서 TestVNet1에 연결 만들기
   
    이 단계는 TestVNet5에서 TestVNet1까지 연결을 만드는 점을 제외하면 위의 비슷합니다. 구독 1에서 가져온 값을 기반으로 PowerShell 개체를 만드는 동일한 과정이 여기에도 적용됩니다. 이 단계에서는 공유된 키가 일치해야 합니다.
   
    구독 5에 연결해야 합니다.
   
        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>연결을 확인하는 방법
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>다음 단계

* 연결이 완료되면 가상 네트워크에 가상 컴퓨터를 추가할 수 있습니다. 자세한 내용은 [가상 컴퓨터](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) 설명서를 참조하세요.
* BGP에 대한 내용은 [BGP 개요](vpn-gateway-bgp-overview.md) 및 [BGP를 구성하는 방법](vpn-gateway-bgp-resource-manager-ps.md)을 참조하세요. 


