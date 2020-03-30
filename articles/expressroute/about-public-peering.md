---
title: Azure ExpressRoute 공용 피어링 생성 및 관리
description: Azure 공용 피어링에 대해 자세히 알아보기 및 관리
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: cherylmc
ms.openlocfilehash: 8c1afac834fb9abb2cbf82f16f046a1624b251f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481136"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute 공용 피어링 생성 및 관리

> [!div class="op_single_selector"]
> * [기사 - 공개 피어링](about-public-peering.md)
> * [비디오 - 공용 피어링](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [기사 - 마이크로 소프트 피어링](expressroute-circuit-peerings.md#microsoftpeering)
>

이 문서에서는 ExpressRoute 회로에 대한 공용 피어링 라우팅 구성을 만들고 관리하는 데 도움이 됩니다. 피어링을 상태, 업데이트 또는 삭제 및 프로비저닝 해제할 수도 있습니다. 이 문서는 공용 피어링이 더 이상 사용되지 전에 만들어진 리소스 관리자 회로에 적용됩니다. 기존 회로(공용 피어링이 더 이상 사용되지 않도록 하기 전에 만든)가 있는 경우 [Azure PowerShell,](#powershell) [Azure CLI](#cli)및 [Azure 포털을](#portal)사용하여 공용 피어링을 관리/구성할 수 있습니다.

>[!NOTE]
>공용 피어링은 더 이상 사용되지 않습니다. 새 ExpressRoute 회로에서는 공용 피어링을 만들 수 없습니다. 새 ExpressRoute 회로가 있는 경우 대신 Azure 서비스에 대해 [Microsoft 피어링을](expressroute-circuit-peerings.md#microsoftpeering) 사용합니다.
>

## <a name="connectivity"></a>연결

연결은 항상 사용자의 WAN에서 Microsoft Azure 서비스로 시작됩니다. Microsoft Azure 서비스가 라우팅 도메인을 통해 네트워크로의 연결을 시작할 수 없습니다. ExpressRoute 회로가 Azure 공용 피어링에 대해 활성화된 경우 회로를 통해 [Azure에서 사용되는 공용 IP 범위에](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) 액세스할 수 있습니다.

공용 피어링이 활성화되면 대부분의 Azure 서비스에 연결할 수 있습니다. Microsoft에서 경로를 보급하는 서비스는 사용자가 선택할 수 없습니다.

* Azure 저장소, SQL 데이터베이스 및 웹 사이트와 같은 서비스는 공용 IP 주소에서 제공됩니다.
* 공용 피어링 라우팅 도메인을 통해 클라우드 서비스의 VIP를 포함하여 공용 IP 주소에서 호스팅되는 서비스에 개인적으로 연결할 수 있습니다.
* 인터넷을 통해 연결하지 않고도 공용 피어링 도메인을 DMZ에 연결하고 WAN에서 해당 공용 IP 주소의 모든 Azure 서비스에 연결할 수 있습니다.

## <a name="services"></a><a name="services"></a>Services

이 섹션에서는 공용 피어링을 통해 사용할 수 있는 서비스를 보여 주며 있습니다. 공용 피어링은 더 이상 사용되지 않으므로 공용 피어링에 새 서비스 나 추가 서비스를 추가할 계획은 없습니다. 공용 피어링을 사용하고 사용하려는 서비스가 Microsoft 피어링을 통해서만 지원되는 경우 Microsoft 피어링으로 전환해야 합니다. 지원되는 서비스 목록은 [Microsoft 피어링을](expressroute-faqs.md#microsoft-peering) 참조하십시오.

**지원:**

* Power BI
* 대부분의 Azure 서비스가 지원됩니다. 지원 여부를 확인하는 데 사용할 서비스를 직접 확인합니다.

**지원되지 않음:**
  * CDN
  * Azure Front Door
  * 다단계 인증 서버(레거시)
  * Traffic Manager

특정 서비스에 대한 가용성을 확인하려면 해당 서비스에 대한 설명서를 확인하여 해당 서비스에 대해 게시된 예약 범위가 있는지 확인할 수 있습니다. 그런 다음 대상 서비스의 IP 범위를 조회하고 [Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드 XML 파일에](https://www.microsoft.com/download/details.aspx?id=56519)나열된 범위와 비교할 수 있습니다. 또는 설명을 위해 해당 서비스에 대한 지원 티켓을 열 수 있습니다.

## <a name="peering-comparison"></a><a name="compare"></a>피어링 비교

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Azure 공용 피어링에는 각 BGP 세션에 연결된 1개의 NAT IP 주소가 있습니다. 2NAT IP 주소를 초과하는 경우 Microsoft 피어링으로 이동합니다. Microsoft 피어링을 사용하면 사용자 고유의 NAT 할당을 구성하고 선택적 접두사 보급 알림에 경로 필터를 사용할 수 있습니다. 자세한 내용은 [Microsoft 피어링으로 이동을](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)참조하십시오.
>

## <a name="custom-route-filters"></a>사용자 지정 경로 필터

필요한 경로만 이용하도록 네트워크 내에 사용자 지정 경로 필터를 정의할 수 있습니다. 라우팅 구성에 대한 자세한 내용은 [라우팅](expressroute-routing.md) 페이지를 참조하세요.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell 단계


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

공용 피어링은 더 이상 사용되지 않으므로 새 ExpressRoute 회로에서 공용 피어링을 구성할 수 없습니다.

1. 프로비저닝되고 활성화된 ExpressRoute 회로가 있는지 확인합니다. 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   응답은 다음 예제와 유사합니다.

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. 회로에 Azure 공용 피어링을 구성합니다. 더 진행하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * 선택 사항:
   * 하나를 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 실행하여 회로에 Azure 공용 피어링을 구성합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   MD5 해시를 사용하려는 경우 다음 예제를 사용합니다.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Azure 공용 피어링 세부 정보를 가져오려면

다음 cmdlet을 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure 공용 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 200개에서 600개로 업데이트됩니다.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure 공용 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLI 단계


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. 먼저 ExpressRoute 회로가 프로비전되고 사용 가능한지 확인합니다. 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route list
   ```

   응답은 다음 예제와 유사합니다.

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. 회로에 Azure 공용 피어링을 구성합니다. 더 진행하기 전에 다음 정보가 있는지 확인합니다.

   * 기본 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 보조 링크에 대한 /30 서브넷입니다. 유효한 공용 IPv4 접두사여야 합니다.
   * 피어링을 설정할 유효한 VLAN ID입니다. 회로에 다른 피어링이 동일한 VLAN ID를 사용하지 않는지 확인합니다.
   * 피어링에 대한 AS 숫자입니다. 2바이트 및 4바이트 AS 번호를 모두 사용할 수 있습니다.
   * **선택 사항 -** 사용하기로 선택한 경우 MD5 해시를 사용합니다.

   다음 예제를 실행하여 회로에 Azure 공용 피어링을 구성합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   MD5 해시를 사용하려는 경우 다음 예제를 사용합니다.

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > 고객 ASN이 아닌 피어링 ASN로 AS 번호를 지정했는지 확인합니다.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Azure 공용 피어링 세부 정보를 보려면

다음 예제를 사용하여 구성 세부 정보를 가져올 수 있습니다.

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

다음 예제와 유사하게 출력됩니다.

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure 공용 피어링 구성을 업데이트하려면

다음 예제를 사용하여 구성의 일부를 업데이트할 수 있습니다. 이 예제에서는 회로의 VLAN ID를 200개에서 600개로 업데이트됩니다.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure 공용 피어링을 삭제하려면

다음 예제를 실행하여 피어링 구성을 제거할 수 있습니다.

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure Portal 단계

피어링을 구성하려면 이 문서에 포함된 PowerShell 또는 CLI 단계를 사용합니다. 피어링을 관리하려면 아래 섹션을 사용할 수 있습니다. 참고로 이러한 단계는 [포털에서 Microsoft 피어링을](expressroute-howto-routing-portal-resource-manager.md#msft)관리하는 것과 유사합니다.

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Azure 공용 피어링 세부 정보를 보려면

포털에서 피어링을 선택하여 Azure 공용 피어링의 속성을 봅니다.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Azure 공용 피어링 구성을 업데이트하려면

피어링행행을 선택한 다음 피어링 속성을 수정합니다.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Azure 공용 피어링을 삭제하려면

삭제 아이콘을 선택하여 피어링 구성을 제거합니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 [가상 네트워크를 ExpressRoute 회로에 연결합니다.](expressroute-howto-linkvnet-arm.md)

* ExpressRoute 워크플로에 대한 자세한 내용은 [ExpressRoute 워크플로](expressroute-workflows.md)를 참조하세요.
* 회로 피어링에 대한 자세한 내용은 [ExpressRoute 회로 및 라우팅 도메인](expressroute-circuit-peerings.md)을 참조하세요.
* 가상 네트워크 작업에 대한 자세한 내용은 [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.