---
title: Azure ExpressRoute Global Reach 적용 시나리오 | Microsoft Docs
description: 이 페이지에서는 Global Reach 적용 시나리오를 제공합니다.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: rambala
ms.openlocfilehash: 5174baae40d7f14720affe90f702c024541a5745
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54333087"
---
# <a name="expressroute-global-reach-application-scenario"></a>ExpressRoute Global Reach 적용 시나리오

ExpressRoute Global Reach에 대한 자세한 내용은 [ExpressRoute Global Reach][Global Reach]를 참조하세요. 이 문서에서는 적용 시나리오를 따라가면서 ExpressRoute Global Reach 솔루션을 다른 몇 가지 솔루션과 비교하고, 예제 시나리오에 맞게 Global Reach를 구성하고, 연결을 확인합니다. 

##<a name="application-scenario"></a>적용 시나리오

Fabrikam Inc.는 미국 동부에 있는 대규모의 실제 Azure 배포 기업입니다. Fabrikam은 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다. Contoso Ltd.는 미국 서부의 Azure 배포 기업입니다. Contoso는 ExpressRoute를 통해 해당 온-프레미스와 Azure 배포 간에 백 엔드 연결을 유지합니다.  

Fabrikam Inc.는 Contoso Ltd.를 합병했습니다. 합병한 후에 Fabrikam은 네트워크 상호 연결하려고 합니다. 다음 그림은 이 시나리오를 보여 줍니다.

 [![1]][1]

위 그림 중간 부분의 점선 화살표는 필요한 네트워크 상호 연결을 나타냅니다. 다음 표에서는 합병 이전의 Contoso Ltd. ExpressRoute의 기본 개인 피어링을 나타내는 경로 테이블을 보여 줍니다.

[![2]][2]

다음 표에서는 합병 이전의 Fabrikam Inc. ExpressRoute의 기본 개인 피어링을 나타내는 경로 테이블을 보여 줍니다.

[![3]][3]

## <a name="traditional-solutions"></a>기존 솔루션

### <a name="option-1-interconnect-on-premises-networks"></a>옵션 1: 온-프레미스 네트워크 상호 연결

다음 그림은 이 옵션을 보여 줍니다. 표시된 것처럼 사이트 간 VPN 또는 기타 광대역 연결 옵션을 사용하여 두 온-프레미스 네트워크를 상호 연결한 후 두 엔터티 간의 모든 라우팅을 관리할 수 있습니다. 

[![4]][4]

이 옵션은 다음과 같은 단점이 있습니다.

- 최적이 아닌 경로를 통한 배포에서는 Azure 지역 간 통신을 강제로 적용하게 됩니다.
- 지역 간 통신에서 Microsoft 백본 네트워크의 고가용성의 혜택을 거부하게 됩니다.
- 지역 간 통신에 대해 전체 라우팅 책임을 사용자가 지게 됩니다.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>옵션 2: ExpressRoute 교차 연결 및 온-프레미스 네트워크 상호 연결

다음 그림은 이 옵션을 보여 줍니다.

[![5]][5]

위 그림과 같이 교차 지역 VNet에 대해 ExpressRoute 회로 간 연결을 추가로 설정할 수 있습니다. ExpressRoute 회로에 대한 VNet 간 교차 지역 연결은 다음 통신을 사용하도록 설정합니다.

- 각각 Fabrikam/Contoso 온-프레미스 네트워크와 통신하기 위한 미국 서부/미국 동부 VNet
- 미국 동부 VNet과 통신하기 위한 미국 서부 VNet

온-프레미스 네트워크가 서로 통신하려면 두 온-프레미스 네트워크 간의 상호 연결이 여전히 필요합니다.

이 옵션을 사용하면 개인 배포를 위한 지역 간 Azure 통신이 Microsoft 백본을 통해 진행되고, 온-프레미스 네트워크 간 통신이 외부 네트워크를 통해 수행될 수 있습니다. 그러나 이 솔루션의 주요 단점은 여러 교차 지역 연결을 설정해야 하므로 유지 관리 및 문제 해결이 복잡해진다는 것입니다. 또한 이 옵션에서는 두 온-프레미스 네트워크 간의 통신을 위해 고가용성 Microsoft 글로벌 백본을 활용할 수 없습니다.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>옵션 3: VNet 피어링 및 온-프레미스 네트워크 상호 연결

다음 그림은 이 옵션을 보여 줍니다. 이 옵션은 지역 간 VNet 통신을 위해 VNet 피어링을 사용합니다. 그림과 같이 이 옵션은 교차 지역 VNet과 온-프레미스 간의 통신 주소를 지정하지 않으므로 불완전합니다(중간에 두 개의 점선 화살표로 표시). 온-프레미스에 대한 교차 지역 통신에는 온-프레미스 간 연결(옵션 1) 또는 ExpressRoute 교차 연결(옵션 2)을 사용합니다.

[![6]][6]

이 옵션은 지역 간 VNet 통신에 대한 최적의 라우팅을 제공합니다. 그러나 Fabrikam 또는 Contoso에 허브-스포크 VNet 모델과 같은 좀 더 복잡한 Azure 배포가 있으면 이 옵션으로 충분하지 않게 됩니다. 또한 앞의 두 옵션처럼 이 옵션에서는 두 온-프레미스 네트워크 간의 통신을 위해 고가용성 Microsoft 글로벌 백본을 활용할 수 없습니다.

## <a name="global-reach"></a>Global Reach

다음 그림과 같이 ExpressRoute Global Reach는 ExpressRoute 회로의 개인 피어링을 연결합니다.

[![7]][7]

두 ExpressRoute 회로 간에 Global Reach를 구성하면 두 온-프레미스 네트워크와 두 지역의 Azure 배포 간에 개인 통신이 설정됩니다. 따라서 Global Reach는 Microsoft 백본 네트워크를 통해 원하는 모든 통신(이 문서의 첫 번째 그림에 파선으로 표시)을 충족합니다.

### <a name="configure-global-reach"></a>Global Reach 구성

ExpressRoute Global Reach를 구성하는 방법을 알아보려면 [Global Reach 구성][Configure Global Reach]을 참조하세요. 

Fabrikam Inc. 및 Contoso Ltd.는 Azure에서 별도 회사로 온보딩되므로 두 회사의 ExpressRoute 회로는 두 개의 다른 Azure 구독에 있습니다. 구독 전체에서 Global Reach를 만들려면 Contoso Ltd.에 속하는 ExpressRoute 회로에서 권한 부여를 만들고 Fabrikam Inc. ExpressRoute 회로에 전달해야 합니다.


Contoso의 ExpressRoute 회로에 대해 권한 부여를 만들려면 먼저 Contoso의 Azure 계정에 로그인하고 해당 구독을 선택합니다(여러 구독이 있는 경우). 이러한 단계를 위한 PowerShell 명령은 다음과 같습니다.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
ExpressRoute 회로 권한 부여를 만드는 단계는 다음과 같습니다.

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

'Set-AzureRmExpressRouteCircuit'은 ExpressRouteCircuit을 나열합니다. 목록 끝쪽으로 표시되는 개인 피어링 ID 및 권한 부여 키를 적어둡니다. 아래의 예제 PowerShell 출력 조각을 참조하세요.

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

피어링 ID 및 권한 부여 키를 사용하여 Fabrikam의 ExpressRoute 회로 아래에 Global Reach를 만들 수 있습니다. Fabrikam의 Azure 계정에 로그인합니다. 구독이 둘 이상인 경우 해당 구독을 선택합니다.

Global Reach는 두 MSEE 쌍 간에 중복되는 지점 간 연결 세트를 만듭니다. 두 지점 간 연결에 대해 /29를 주소 접두사를 지정해야 합니다(실행 예제로 192.168.11.64/29 사용). 다음 명령을 사용하여 Global Reach 연결을 만듭니다.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

위 명령을 실행하면 중복 Global Reach 연결을 만드는 데 몇 분 정도 걸립니다.

### <a name="verify-global-reach"></a>Global Reach 확인

다음 표에서는 Global Reach를 구성한 이후의 Contoso Ltd. ExpressRoute의 기본 개인 피어링을 나타내는 경로 테이블을 보여 줍니다.

[![8]][8]

다음 표에서는 Global Reach를 구성한 이후의 Fabrikam Inc. ExpressRoute의 기본 개인 피어링을 나타내는 경로 테이블을 보여 줍니다.

[![9]][9]

위의 표에서는 필요한 모든 대상 'NETWORK' 접두사와 해당 'NEXT HOT'이 나열되는 것을 볼 수 있습니다.

위에서는 Azure Portal에서 ExpressRoute 회로의 '개인 피어링' 아래에서 액세스할 수 있는 '경로 테이블 가져오기' 블레이드가 표시됩니다. 다음 PowerShell 명령을 사용하여 ExpressRoute 경로 테이블을 나열할 수도 있습니다.

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

보조 MSEE의 경로 테이블을 보려면 위 명령에서 'primary'를 키워드 ‘secondary’로 바꿉니다.

다른 네트워크의 다른 대상을 ping하여 데이터 평면 연결도 확인해 보겠습니다. 다음 세 ping은 Fabrikam 온-프레미스 네트워크에서 Contoso 온-프레미스 네트워크, Contoso Azure VNet 및 Fabrikam Azure VNet에 대한 데이터 평면 연결을 확인합니다.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


다음 두 ping은 Contoso 온-프레미스 네트워크에서 Contoso Azure VNet 및 Fabrikam Azure VNet에 대한 데이터 평면 연결을 확인합니다.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

다음 pingdms Fabrikam Azure VNet에서 Contoso Azure VNet으로의 데이터 평면 연결을 확인합니다.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>대기 시간이 중요한 트래픽에 대한 최적화

Global Reach는 Microsoft Edge 디바이스를 통해 트래픽을 라우팅합니다. 이 문서에서 고려되는 특정 시나리오의 경우, 두 VNet 간에 VNet 피어링을 사용하도록 설정하여 좀 더 최적화된 라우팅을 얻을 수 있습니다. 마찬가지로, 사이트 간에 좀 더 직접적인 WAN 연결을 제공할 수 있는 서비스 공급자를 통해 두 온-프레미스 네트워크 간에 좀 더 최적화된 라우팅을 얻을 수 있습니다. 이러한 시나리오에서는 연결의 장애 복구(failback) 옵션으로 Global Reach 라우팅을 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

Global Reach는 국가별로 롤아웃됩니다. Global Reach를 원하는 국가에서 사용할 수 있는지 확인하려면 [ExpressRoute Global Reach][Global Reach]를 참조하세요.

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "적용 시나리오"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "합병 이전의 Contoso ExpressRoute 경로 테이블"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "합병 이전의 Fabrikam ExpressRoute 경로 테이블"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "온-프레미스 네트워크 상호 연결"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "ExpressRoute 교차 연결"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "VNet 피어링"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "Global Reach"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "Global Reach를 사용한 Contoso ExpressRoute 경로 테이블"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "Global Reach를 사용한 Fabrikam ExpressRoute 경로 테이블"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





