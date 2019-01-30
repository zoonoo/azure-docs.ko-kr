---
title: 'Global Reach 구성 - ExpressRoute: Azure | Microsoft Docs'
description: 이 문서는 온-프레미스 네트워크 간의 사설 네트워크를 설정하고 Global Reach를 사용하도록 설정하기 위해 ExpressRoute 회로를 함께 연결하는 데 유용합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: ab1098ca65ad92cffdbe1dfb24fd43fcc8f10eae
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54431681"
---
# <a name="configure-expressroute-global-reach-preview"></a>ExpressRoute Global Reach 구성(미리 보기)
이 문서는 PowerShell을 사용하여 ExpressRoute Global Reach를 구성하는 데 도움이 됩니다. 자세한 내용은 [ExpressRoute Global Reach](expressroute-global-reach.md)를 참조하세요.
 
## <a name="before-you-begin"></a>시작하기 전에
> [!IMPORTANT]
> 이 공개 미리 보기는 서비스 수준 약정 없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능이 지원되지 않거나, 기능이 제한되거나, 일부 Azure 위치에 제공되지 않을 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 


구성을 시작하기 전에 다음을 확인합니다.

* 최신 버전의 Azure PowerShell을 설치했습니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/azurerm/install-azurerm-ps)을 참조하세요.
* ExpressRoute 회로 프로비저닝 [워크플로](expressroute-workflows.md)를 이해했습니다.
* ExpressRoute 회로가 프로비전된 상태입니다.
* Azure 개인 피어링이 ExpressRoute 회로에 구성되어 있습니다.  

### <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인
구성을 시작하려면 Azure 계정에 로그인합니다. 

상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 이 명령은 Azure 계정에 대한 로그인 자격 증명을 입력하라는 메시지를 표시합니다.  

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
두 ExpressRoute 회로가 지원되는 국가에 위치하고 서로 다른 피어링 위치에서 만들어진 경우에 한하여 ExpressRoute Global Reach를 사용하도록 설정할 수 있습니다. 사용자 구독이 두 회로 모두를 소유하는 경우 다음 섹션에서 구성을 실행할 회로를 하나 선택할 수 있습니다. 

두 회로가 다른 Azure 구독에 있는 경우 하나의 Azure 구독에서 권한을 부여해야 합니다. 그런 후 다른 Azure 구독에서 구성 명령을 실행할 때 권한 부여 키를 제공합니다.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>온-프레미스 네트워크 간 연결 사용

다음 명령을 사용하여 회로 1 및 회로 2를 가져옵니다. 두 회로는 같은 구독에 있습니다.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

회로 1에서 다음 명령을 실행하고 회로 2의 개인 피어링 ID를 전달합니다. 이 명령을 실행할 때 다음에 유의합니다.

* 개인 피어링 ID는 다음 예제와 비슷합니다. 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix*는 /29 IPv4 서브넷이어야 합니다(예: "10.0.0.0/29"). 두 ExpressRoute 회로 간의 연결을 설정하는 데 이 서브넷의 IP 주소가 사용됩니다. Azure Virtual Network 또는 온-프레미스 네트워크에서 이 서브넷의 주소를 사용하면 안 됩니다.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

회로 1에서 다음과 같이 구성을 저장합니다.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

이전 작업이 완료되면 두 ExpressRoute 회로를 통해 양쪽에 있는 온-프레미스 네트워크 간에 연결이 설정됩니다.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>서로 다른 Azure 구독의 ExpressRoute 회로

두 회로가 동일한 Azure 구독에 있지 않으면 권한 부여가 필요합니다. 다음 구성에서는 회로 2의 구독에서 권한 부여가 생성되고 권한 부여 키가 회로 1에 전달됩니다.

권한 부여 키를 생성합니다. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
회로 2의 개인 피어링 ID와 권한 부여 키를 기록해 둡니다.

회로 1에서 다음 명령을 실행합니다. 회로 2의 개인 피어링 ID와 권한 부여 키를 제공합니다.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

회로 1에서 구성을 저장합니다.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

이전 작업이 완료되면 두 ExpressRoute 회로를 통해 양쪽에 있는 온-프레미스 네트워크 간에 연결이 설정됩니다.

## <a name="get-and-verify-the-configuration"></a>구성을 가져와서 확인

구성이 수행된 회로(예를 들어, 이전 예제의 회로 1)에서 구성을 확인하려면 다음 명령을 사용합니다.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

PowerShell에서 *$ckt1*만 실행하는 경우 *CircuitConnectionStatus*가 출력됩니다. 연결이 설정되면 "연결됨", 설정되지 않으면 "연결 끊김"이 표시됩니다. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>온-프레미스 네트워크 간 연결을 사용 안 함

연결을 사용하지 않도록 설정하려면 구성이 수행된 회로(예를 들어, 이전 예제의 회로 1)에 대해 이 명령을 실행하세요.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

가져오기 작업을 실행하여 상태를 확인할 수 있습니다. 

이전 작업이 완료되면 사용자의 ExpressRoute 회로를 통해 온-프레미스 네트워크 간에 연결이 해제됩니다. 


## <a name="next-steps"></a>다음 단계
1. [ExpressRoute Global Reach에 대해 자세히 알아봅니다.](expressroute-global-reach.md)
2. [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure Virtual Network에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)


