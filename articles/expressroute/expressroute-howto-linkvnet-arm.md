---
title: "ExpressRoute 회로에 가상 네트워크 연결: PowerShell: Azure | Microsoft Docs"
description: "이 문서는 리소스 관리자 배포 모델 및 PowerShell을 사용하여 VNet(가상 네트워크)을 Express 경로 회로에 연결하는 방법에 대한 개요를 제공합니다."
services: expressroute
documentationcenter: na
author: ganesr
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: ganesr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 32b12bcb7410fcc74450422767e9d92fef38ebdc
ms.lasthandoff: 03/25/2017


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Virtual Network를 ExpressRoute 회로에 연결
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [클래식 - PowerShell](expressroute-howto-linkvnet-classic.md)
> * [비디오 - Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
> 

이 문서를 참조하면 Resource Manager 배포 모델 및 PowerShell을 사용하여 VNet(가상 네트워크)을 Azure ExpressRoute 회로에 연결할 수 있습니다. 가상 네트워크는 같은 구독에 있을 수도 있고 다른 구독의 일부일 수도 있습니다. 또한 이 문서는 가상 네트워크 링크를 업데이트하는 방법을 보여줍니다. 

**Azure 배포 모델 정보**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>필수 구성 요소
* Azure PowerShell 모듈의 최신 버전(버전 1.0 이상)이 필요합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs) 을 참조하세요.
* 구성을 시작하기 전에 [필수 조건](expressroute-prerequisites.md), [라우팅 요구 사항](expressroute-routing.md) 및 [워크플로](expressroute-workflows.md)를 검토해야 합니다.
* 활성화된 Express 경로 회로가 있어야 합니다. 
  * 지침을 수행하여 [Express 경로 회로를 만들고](expressroute-howto-circuit-arm.md) 연결 공급자를 통해 회로를 사용하도록 설정합니다. 
  * 회로에 구성된 Azure 개인 피어링이 있는지 확인합니다. 라우팅 지침에 대한 문서는 [라우팅 구성](expressroute-howto-routing-arm.md) 을 참조하세요. 
  * Azure 개인 피어링이 구성되어 있고 네트워크와 Microsoft 간의 BGP 피어링이 종단 간 연결을 사용하도록 작동 중이어야 합니다.
  * 가상 네트워크 및 가상 네트워크 게이트웨이를 만들어서 완전히 프로비전해야 합니다. [VPN Gateway](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)를 만들려면 지침을 따르고 `-GatewayType ExpressRoute`를 사용해야 합니다.

최대 10개의 가상 네트워크를 표준 Express 경로 회로에 연결할 수 있습니다. 표준 Express 경로 회로를 사용하는 경우 모든 가상 네트워크는 동일한 지역에 있어야 합니다. 

Express 경로 프리미엄 추가 기능을 사용하도록 설정하면 Express 경로 회로의 지역 외부에서 가상 네트워크를 연결하거나 Express 경로 회로에 많은 수의 가상 네트워크를 연결할 수 있습니다. 프리미엄 추가 기능에 대한 자세한 내용은 [FAQ](expressroute-faqs.md) 에서 확인하세요.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>동일한 구독에 있는 가상 네트워크를 회로에 연결
다음 cmdlet를 사용하여 Express 경로 회로에 가상 네트워크 게이트웨이를 연결할 수 있습니다. cmdlet을 실행하기 전에 가상 네트워크 게이트웨이가 연결을 위해 생성되고 준비되었는지 확인합니다.

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>다른 구독에 있는 가상 네트워크를 회로에 연결
여러 구독에서 Express 경로 회로를 공유할 수 있습니다. 아래 그림에는 여러 구독에서 Express 경로 회로에 대한 작업을 공유하는 방법의 간단한 계통도가 나와 있습니다.

큰 구름 안에 있는 각각의 작은 구름은 한 조직 내의 여러 부서에 속하는 구독을 나타내는 데 사용됩니다. 조직 내의 각 부서는 자체 구독을 사용하여 서비스를 배포하되, 단일 Express 경로 회로를 공유하여 온-프레미스 네트워크로 다시 연결할 수 있습니다. 단일 부서(이 예제에서는 IT)가 Express 경로 회로를 소유할 수 있습니다. 조직 내의 기타 구독도 Express 경로 회로를 사용할 수 있습니다.

> [!NOTE]
> 전용 회로에 대한 연결 및 대역폭 요금은 Express 경로 회로 소유자에게 적용됩니다. 모든 가상 네트워크는 동일한 대역폭을 공유합니다.
> 
> 

![구독 간 연결](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>관리
*회로 소유자* 는 Express 경로 회로 리소스의 인증된 고급 사용자입니다. 회로 소유자는 *회로 사용자*가 사용할 수 있는 권한 부여를 만들 수 있습니다. *회로 사용자* 는 Express 경로 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. *회로 사용자* 는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

*회로 소유자* 는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다.

### <a name="circuit-owner-operations"></a>회로 소유자 작업

**권한 부여 만들기**

회로 소유자가 권한 부여를 만듭니다. 그러면 회로 사용자가 Express 경로 회로에 가상 네트워크 게이트웨이를 연결하는 데 사용할 수 있는 권한 부여 키가 만들어집니다. 권한 부여는 하나의 연결에만 유효합니다.

다음 cmdlet 조각은 권한 부여를 만드는 방법을 보여 줍니다.

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

        $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"


이에 대한 응답에는 권한 부여 키와 상태가 포함됩니다.

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**권한 부여 검토**

회로 소유자는 다음 cmdlet을 실행하여 특정 회로에 발급한 모든 권한 부여를 검토할 수 있습니다.

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


**권한 부여 추가**

회로 소유자는 다음 cmdlet를 사용하여 권한 부여를 추가할 수 있습니다.

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

    $circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
    $authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit


**권한 부여 삭제**

회로 소유자는 다음 cmdlet을 실행하여 권한 부여를 취소/삭제할 수 있습니다.

    Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit    

**회로 사용자 작업**

회로 사용자는 회로 소유자로부터 권한 부여 키를 받아야 하며 피어 ID가 필요합니다. 권한 부여 키는 GUID입니다.

피어 ID는 다음 명령에서 확인할 수 있습니다.

    Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"

**연결 권한 부여 사용**

회로 사용자는 다음 cmdlet을 실행하여 링크 권한 부여를 사용할 수 있습니다.

    $id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
    $gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
    $connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"

**연결 권한 부여 해제**

Express 경로 회로와 가상 네트워크의 연결을 삭제하여 권한 부여를 해제할 수 있습니다.

## <a name="modify-a-virtual-network-connection"></a>가상 네트워크 연결 수정
가상 네트워크 연결의 특정 속성을 업데이트할 수 있습니다. 

### <a name="update-the-connection-weight"></a>연결 무게 업데이트
가상 네트워크를 여러 ExpressRoute 회로에 연결할 수 있습니다. 둘 이상의 ExpressRoute 회로에서 동일한 접두사를 수신할 수도 있습니다. 이 접두사를 대상으로 하는 트래픽을 전송할 연결을 선택하기 위해 연결의 *RoutingWeight*를 변경할 수 있습니다. 트래픽은 제일 높은 *RoutingWeight*를 사용한 연결로 전송됩니다.

    $connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
    $connection.RoutingWeight = 100
    Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection

*RoutingWeight*의 범위는 0에서 32000입니다. 기본값은 0입니다. 

## <a name="next-steps"></a>다음 단계
Express 경로에 대한 자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.


