---
title: Azure ExpressRoute Global Reach 구성 | Microsoft Docs
description: 이 문서는 온-프레미스 네트워크 간의 사설 네트워크를 설정하고 Global Reach를 사용하도록 설정하기 위해 ExpressRoute 회로를 함께 연결하는 데 유용합니다.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4006626f9768289e75ccd61a1ef0bad5389f0a7a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071042"
---
# <a name="configure-expressroute-global-reach-preview"></a>ExpressRoute Global Reach 구성(미리 보기)
이 문서는 PowerShell을 사용하여 ExpressRoute Global Reach를 구성하는 데 도움이 됩니다. 자세한 내용은 [ExpressRoute Global Reach](expressroute-global-reach.md)를 참조하세요.
 
## <a name="before-you-begin"></a>시작하기 전에
> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 


구성을 시작하기 전에 다음 요구 사항을 확인해야 합니다.

* Azure PowerShell 최신 버전을 설치합니다. [Azure PowerShell 설치 및 구성](/powershell/azure/install-azurerm-ps)을 참조하세요.
* ExpressRoute 회로 프로비전 [워크플로](expressroute-workflows.md)를 이해합니다.
* ExpressRoute 회로가 프로비전된 상태인지 확인합니다.
* Azure 개인 피어링이 ExpressRoute 회로에 구성되어 있는지 확인합니다.  

### <a name="log-into-your-azure-account"></a>Azure 계정에 로그인
구성을 시작하기 전에 Azure 계정에 로그인해야 합니다. 

상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 사용자 Azure 계정의 로그인 자격 증명에 대한 명령 프롬프트가 표시됩니다.  

```powershell
Connect-AzureRmAccount
```

Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

```powershell
Get-AzureRmSubscription
```

사용할 구독을 지정합니다.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>구성에 대한 ExpressRoute 회로 식별
두 ExpressRoute 회로가 지원되는 국가에 위치하고 서로 다른 피어링 위치에서 만들어진 경우에 한하여 ExpressRoute Global Reach를 사용하도록 설정할 수 있습니다. 사용자 구독이 두 회로 모두를 소유하는 경우 아래 섹션에서 구성을 실행할 회로를 하나 선택할 수 있습니다. 두 회로가 서로 다른 Azure 구독에 있는 경우 한 Azure 구독에서 권한을 부여하고, 다른 Azure 구독에서 구성 명령을 실행할 때 권한 부여 키를 전달해야 합니다.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>온-프레미스 네트워크 간 연결 사용

다음 명령을 사용하여 회로 1 및 회로 2를 가져옵니다. 두 회로는 같은 구독에 있습니다.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

회로 1에서 다음 명령을 실행하고 회로 2의 개인 피어링 ID를 전달합니다.

> [!NOTE]
> 개인 피어링의 ID는 */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*과 같습니다.
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix*는 /29 IPv4 서브넷이어야 합니다. 예를 들어, 다음과 같습니다. “10.0.0.0/29”입니다. 두 ExpressRoute 회로 간의 연결을 설정하는 데 이 서브넷의 IP 주소가 사용됩니다. Azure VNet 또는 온-프레미스 네트워크에서는 이 서브넷의 주소를 사용하지 말아야 합니다.
> 



회로 1에서 구성을 저장합니다.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

위의 작업이 완료되면 두 ExpressRoute 회로를 통해 양쪽에 있는 온-프레미스 네트워크 간에 연결이 설정됩니다.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>서로 다른 Azure 구독의 ExpressRoute 회로

두 회로가 동일한 Azure 구독에 있지 않으면 권한 부여가 필요합니다. 다음 구성에서는 회로 2의 구독에서 권한 부여가 생성되고 권한 부여 키가 회로 1에 전달됩니다.

권한 부여 키를 생성합니다. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
회로 2의 개인 피어링 ID와 함께 권한 부여 키를 기록해 둡니다.

회로 1에서 다음 명령을 실행합니다. 회로 2의 개인 피어링 ID 및 권한 부여 키를 전달합니다. 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

회로 1에서 구성을 저장합니다.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

위의 작업이 완료되면 두 ExpressRoute 회로를 통해 양쪽에 있는 온-프레미스 네트워크 간에 연결이 설정됩니다.

## <a name="get-and-verify-the-configuration"></a>구성을 가져와서 확인

구성이 수행된 회로에서 구성을 확인하려면 다음 명령을 사용합니다. 예를 들어, 위의 예에서는 회로 1입니다.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

PowerShell에서 *$ckt1*만 실행하는 경우 *CircuitConnectionStatus*가 출력됩니다. 연결이 설정되면 “연결됨”, 설정되지 않으면 “연결 끊김”이 표시됩니다. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>온-프레미스 네트워크 간 연결을 사용 안 함

이를 사용하지 않으려면 구성이 수행된 회로에 대해 이 명령을 실행하세요. 예를 들어, 위의 예제에서는 회로 1입니다.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

가져오기 작업을 실행하여 상태를 확인할 수 있습니다. 

위의 작업이 완료되면 사용자의 ExpressRoute 회로를 통해 온-프레미스 네트워크 간에 연결이 해제됩니다. 


## <a name="next-steps"></a>다음 단계
1. [ExpressRoute Global Reach에 대해 자세히 알아봅니다.](expressroute-global-reach.md)
2. [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure 가상 네트워크에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)


