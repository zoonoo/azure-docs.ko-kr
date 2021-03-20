---
title: 'Azure Express 경로: 회로 피어 링 다시 설정'
description: Azure PowerShell를 사용 하 여 Azure Express 경로 회로에 대해 피어 링을 사용 하도록 설정 하 고 해제 하는 방법을 알아봅니다.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97559576"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute 회로 피어링 다시 설정

이 문서에서는 PowerShell을 사용 하 여 Express 경로 회로의 피어 링을 사용 하거나 사용 하지 않도록 설정 하는 방법을 설명 합니다. 피어 링은 만들 때 기본적으로 사용 하도록 설정 됩니다. 피어 링을 사용 하지 않도록 설정 하면 Express 경로 회로의 기본 연결 및 보조 연결 모두에서 BGP 세션이 종료 됩니다. 이 피어 링은 Microsoft에 연결 되지 않습니다. 피어 링을 사용 하도록 설정 하면 Express 경로 회로의 기본 연결 및 보조 연결 모두에서 BGP 세션이 설정 됩니다. 이 피어 링에 대 한 Microsoft 연결이 복원 됩니다. Express 경로 회로에서 독립적으로 Microsoft 피어 링 및 Azure 개인 피어 링에 대 한 피어 링을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

Express 경로 피어 링을 다시 설정 하는 데 도움이 되는 두 가지 시나리오가 있습니다.
* 재해 복구 디자인 및 구현을 테스트 하려는 경우 예를 들어 두 개의 ExpressRoute 회로가 있습니다. 한 회로에서 피어 링을 사용 하지 않도록 설정 하 고 네트워크 트래픽을 다른 회로에 장애 조치할 수 있습니다.
* Azure 개인 피어 링 또는 Express 경로 회로의 Microsoft 피어 링에서 BFD (양방향 전달 검색)를 사용 하도록 설정 합니다. 2018 년 8 월 1 일 이후에는 Express 경로 회로를 만들고 2020 년 1 월 10 일 이후 Microsoft 피어 링에 대해 BFD가 기본적으로 사용 하도록 설정 됩니다. 표시 된 날짜 이전에 회로가 생성 된 경우 BFD를 사용 하려면 피어 링을 다시 설정 해야 합니다. 

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>피어링 다시 설정

1. PowerShell을 로컬로 실행 하는 경우 상승 된 권한으로 PowerShell 콘솔을 열고 계정에 연결 합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

   ```azurepowershell
   Connect-AzAccount
   ```
2. Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. 사용할 구독을 지정합니다.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. ExpressRoute 회로를 검색하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. 사용하거나 사용하지 않도록 설정하려는 피어링을 식별합니다. *피어링* 은 배열입니다. 다음 예제에서 피어링[0]은 Azure 프라이빗 피어링이고 피어링[1]은 Microsoft 피어 링입니다.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. 피어링의 상태를 변경하려면 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   피어링은 설정한 상태를 유지합니다. 

## <a name="next-steps"></a>다음 단계
Express 경로 문제를 해결 하는 데 도움이 필요한 경우 다음 문서를 참조 하세요.
* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [네트워크 성능 문제 해결](expressroute-troubleshooting-network-performance.md)
