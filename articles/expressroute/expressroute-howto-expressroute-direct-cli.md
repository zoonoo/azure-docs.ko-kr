---
title: 'Azure Express 경로: Express 경로 직접 구성: CLI'
description: Azure CLI를 사용 하 여 Microsoft 글로벌 네트워크에 직접 연결 하도록 Azure Express 경로 다이렉트를 구성 하는 방법을 알아봅니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 09/28/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bdf2c4dda3e272ae04681f886f6e4da31dcebd8
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569828"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Express 경로 직접 구성

Express 경로 다이렉트는 전 세계에 분산 된 피어 링 위치를 통해 Microsoft의 글로벌 네트워크에 직접 연결 하는 기능을 제공 합니다. 자세한 내용은 [ExpressRoute Direct Connect 정보](expressroute-erdirect-about.md)를 참조하세요.

## <a name="create-the-resource"></a><a name="resources"></a>리소스 만들기

1. Azure에 로그인하고 ExpressRoute를 포함하는 구독을 선택합니다. ExpressRoute Direct 리소스와 ExpressRoute 회로가 동일한 구독에 있어야 합니다. Azure CLI에서 다음 명령을 실행합니다.

   ```azurecli
   az login
   ```

   계정에 대한 구독을 확인합니다. 

   ```azurecli
   az account list 
   ```

   ExpressRoute 회로를 만들려는 구독을 선택합니다.

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Expressrouteportslocation 및 expressrouteport Api에 액세스 하려면 구독을 Microsoft 네트워크에 다시 등록 합니다.

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. ExpressRoute Direct가 지원되는 모든 위치를 나열합니다.
    
   ```azurecli
   az network express-route port location list
   ```

   **예제 출력**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. 이전 단계에 나열된 위치 중 하나에 사용 가능한 대역폭이 있는지 여부를 결정합니다.

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **예제 출력**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. 이전 단계에서 선택한 위치를 기준으로 하는 ExpressRoute Direct 리소스를 만듭니다.

   ExpressRoute Direct는 QinQ 및 Dot1Q VLAN 캡슐화를 둘 다 지원합니다. QinQ를 선택한 경우 ExpressRoute Direct 리소스 전체에서 고유하게 식별되는 S-Tag가 각 ExpressRoute 회로에 동적으로 할당됩니다. 회로의 각 C-Tag는 회로에서 고유해야 하지만 ExpressRoute Direct 리소스 전체에서 고유할 필요는 없습니다.  

   Dot1Q 캡슐화를 선택하는 경우 전체 ExpressRoute Direct 리소스에서 C-Tag(VLAN)의 고유성을 관리해야 합니다.  

   > [!IMPORTANT]
   > ExpressRoute Direct에는 한 가지 유형으로만 캡슐화할 수 있습니다. ExpressRoute Direct 리소스를 만든 후에는 캡슐화 종류를 변경할 수 없습니다.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > **Encapsulation** 특성을 **Dot1Q**로 설정할 수도 있습니다. 
   >

   **예제 출력**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>권한 부여의 문자를 생성 합니다 (LOA).

최근 생성 된 Express 경로 직접 리소스 이름, 리소스 그룹 이름 및 LOA를 작성 하는 고객 이름을 입력 하 고 (선택 사항) 문서를 저장할 파일 위치를 정의 합니다. 파일 경로를 참조 하지 않으면 문서가 현재 디렉터리로 다운로드 됩니다.

```azurecli
az network express-route port generate-loa -n Contoso-Direct -g Contoso-Direct-rg --customer-name Contoso --destination C:\Users\SampleUser\Downloads\LOA.pdf
```

## <a name="change-adminstate-for-links"></a><a name="state"></a>링크에 대한 AdminState 변경

이 프로세스를 사용하여 계층 1 테스트를 수행합니다. 각 교차 연결을 기본 및 보조 포트의 각 라우터에 제대로 패치해야 합니다.

1. 링크를 **사용**으로 설정합니다. 이 단계를 반복하여 각 링크를 **사용**으로 설정합니다.

   링크[0]은 1차 포트이고 링크[1]은 2차 포트입니다.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **예제 출력**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   동일한 절차에 따라 `AdminState = "Disabled"`를 사용하여 포트를 작동 중단합니다.

## <a name="create-a-circuit"></a><a name="circuit"></a>회로 만들기

기본적으로 ExpressRoute Direct 리소스를 포함하는 구독에서 10개의 회로를 만들 수 있습니다. Microsoft 지원은 기본 제한을 늘릴 수 있습니다. 사용자는 프로비전된 대역폭과 사용된 대역폭을 둘 다 추적할 책임이 있습니다. 프로비전된 대역폭은 ExpressRoute Direct 리소스의 모든 회로 대역폭 합계입니다. 사용된 대역폭은 기본 실제 인터페이스의 실제 사용량입니다.

여기에 설명된 시나리오만 지원하도록 ExpressRoute Direct에서 추가 회로 대역폭을 사용할 수 있습니다. 대역폭은 40Gbps 및 100Gbps입니다.

지역, 표준 또는 프리미엄 일 **수 있습니다.**

고가 Unlimiteddata **만 가능 합니다** . Express 경로 직접에서는 무제한이 지원 되지 않습니다.

ExpressRoute Direct 리소스에서 회로를 만듭니다.

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  다른 대역폭에는 5Gbps, 10Gbps 및 40Gbps가 포함됩니다.

  **예제 출력**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>다음 단계

ExpressRoute Direct에 대한 자세한 내용은 [개요](expressroute-erdirect-about.md)를 참조하세요.
