---
title: 'Azure ExpressRoute: ExpressRoute Direct 구성'
description: Azure PowerShell을 사용하여 Microsoft 글로벌 네트워크에 직접 연결하도록 Azure ExpressRoute Direct를 구성하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 2c49fe65cf97207a495f6c0cc78585489a0db6ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228251"
---
# <a name="how-to-configure-expressroute-direct"></a>ExpressRoute Direct 구성 방법

ExpressRoute Direct는 전 세계에 전략적으로 분산된 피어링 위치를 통해 Microsoft의 글로벌 네트워크에 직접 연결하는 기능을 제공합니다. 자세한 내용은 [ExpressRoute Direct 정보](expressroute-erdirect-about.md)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

ExpressRoute Direct를 사용하려면 먼저 구독을 등록해야 합니다. ExpressRoute Direct를 사용하려면 먼저 구독을 등록해야 합니다. 등록하려면 Azure PowerShell을 통해 다음을 수행하세요.
1.  Azure에 로그인하고 등록할 구독을 선택합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. 다음 명령을 사용하여 공개 미리 보기에 대한 구독을 등록합니다.
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

등록한 후에는 **Microsoft.Network** 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자에서 작동하도록 구성됩니다.

## <a name="create-the-resource"></a><a name="resources"></a>리소스 만들기

1. Azure에 로그인하고 구독을 선택합니다. ExpressRoute Direct 리소스와 ExpressRoute 회로가 동일한 구독에 있어야 합니다.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. expressrouteportslocation 및 expressrouteport API에 액세스하려면 Microsoft.Network에 대한 구독을 다시 등록합니다.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. ExpressRoute Direct가 지원되는 모든 위치를 나열합니다.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **예제 출력**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. 위에 나열된 위치에 사용 가능한 대역폭이 있는지 확인

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **예제 출력**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. 위에서 선택한 위치를 기준으로 ExpressRoute Direct 리소스 만들기

   ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 캡슐화를 둘 다 지원합니다. QinQ를 선택한 경우 ExpressRoute Direct 리소스 전체에서 고유하게 식별되는 S-Tag가 각 ExpressRoute 회로에 동적으로 할당됩니다. 회로의 각 C-Tag는 회로에서 고유해야 하지만 ExpressRoute Direct 전체에서 고유할 필요는 없습니다.  

   Dot1Q 캡슐화가 선택된 경우 전체 ExpressRoute Direct 리소스에서 C-Tag(VLAN)의 고유성을 관리해야 합니다.  

   > [!IMPORTANT]
   > ExpressRoute Direct에는 한 가지 유형으로만 캡슐화할 수 있습니다. ExpressRoute Direct를 만든 후에는 캡슐화를 변경할 수 없습니다.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > 캡슐화 속성을 Dot1Q로 설정할 수도 있습니다. 
   >

   **예제 출력:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>LOA(승인 문서) 생성

최근 생성된 ExpressRoute Direct 리소스를 참조하고, LOA를 쓸 고객 이름을 입력하고, 필요에 따라 문서를 저장할 파일 위치를 정의합니다. 파일 경로를 참조하지 않으면 문서가 현재 디렉터리로 다운로드됩니다.

  ```powershell 
   New-AzExpressRoutePortLOA -ExpressRoutePort $ERDirect -CustomerName TestCustomerName -Destination "C:\Users\SampleUser\Downloads" 
   ```
 **예제 출력**

   ```powershell
   Written Letter of Authorization To: C:\Users\SampleUser\Downloads\LOA.pdf
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>링크의 관리 상태 변경
   
이 프로세스를 사용하여 계층 1 테스트를 수행하고 각 교차 연결이 1차 및 2차 포트에 대한 각 라우터에 제대로 패치되도록 합니다.
1. ExpressRoute Direct 세부 정보를 가져옵니다.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. 링크를 사용으로 설정합니다. 이 단계를 반복하여 각 링크를 사용으로 설정합니다.

   링크[0]은 1차 포트이고 링크[1]은 2차 포트입니다.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **예제 출력:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   `AdminState = "Disabled"`로 동일한 절차를 사용하여 포트 작동을 중단합니다.

## <a name="create-a-circuit"></a><a name="circuit"></a>회로 생성

기본적으로 ExpressRoute Direct 리소스가 있는 구독에서 10개의 회로를 만들 수 있습니다. 이 제한은 지원을 통해 늘릴 수 있습니다. 사용자는 프로비전된 대역폭과 사용된 대역폭을 둘 다 추적할 책임이 있습니다. 프로비전된 대역폭은 ExpressRoute Direct 리소스에 있는 모든 회로의 대역폭 합계이고, 사용된 대역폭은 기본 물리적 인터페이스의 물리적 사용량입니다.

위에 설명된 시나리오만 지원하기 위해 ExpressRoute Direct에서 활용할 수 있는 추가 회로 대역폭이 있습니다. 대역폭은 40Gbps 및 100Gbps입니다.

**SkuTier** 는 로컬, 표준 또는 프리미엄일 수 있습니다.

**SkuFamily** 는 MeteredData만 될 수 있습니다. ExpressRoute Direct에서는 무제한이 지원되지 않습니다.

ExpressRoute Direct 리소스에서 회로를 만듭니다.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  기타 대역폭에는 5.0, 10.0 및 40.0이 있습니다.

  **예제 출력:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```
## <a name="delete-the-resource"></a>리소스 삭제
ExpressRoute Direct 리소스를 삭제하기 전에 먼저 ExpressRoute Direct 포트 쌍에 생성된 모든 ExpressRoute 회로를 삭제해야 합니다.
다음 명령을 실행하여 ExpressRoute Direct 리소스를 삭제할 수 있습니다.
 ```powershell
   Remove-azexpressrouteport -Name $Name -Resourcegroupname -$ResourceGroupName
   ```
## <a name="next-steps"></a>다음 단계

ExpressRoute Direct에 대한 자세한 내용은 [개요](expressroute-erdirect-about.md)를 참조하세요.
