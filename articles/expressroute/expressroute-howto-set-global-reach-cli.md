---
title: 'ExpressRoute Global Reach 구성: Azure CLI | Microsoft Docs'
description: 이 문서는 온-프레미스 네트워크 간의 사설 네트워크를 설정하고 Global Reach를 사용하도록 설정하기 위해 ExpressRoute 회로를 함께 연결하는 데 유용합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384061"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Azure CLI를 사용하여 ExpressRoute Global Reach 구성(미리 보기)
이 문서는 Azure CLI를 사용하여 ExpressRoute Global Reach를 구성하는 데 도움이 됩니다. 자세한 내용은 [ExpressRoute Global Reach](expressroute-global-reach.md)를 참조하세요.
 
## <a name="before-you-begin"></a>시작하기 전에
> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 


구성을 시작하기 전에 다음 요구 사항을 확인해야 합니다.

* Azure CLI 최신 버전 설치 [Azure CLI 설치](/cli/azure/install-azure-cli) 및 [Azure CLI 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.
* ExpressRoute 회로 프로비전 [워크플로](expressroute-workflows.md)를 이해합니다.
* ExpressRoute 회로가 프로비전된 상태인지 확인합니다.
* Azure 개인 피어링이 ExpressRoute 회로에 구성되어 있는지 확인합니다.  

### <a name="log-into-your-azure-account"></a>Azure 계정에 로그인
구성을 시작하기 전에 Azure 계정에 로그인해야 합니다. 이 명령은 기본 브라우저를 열고 사용자 Azure 계정의 로그인 자격 증명을 요청하는 메시지를 표시합니다.  

```azurecli
az login
```

Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

```azurecli
az account list
```

사용할 구독을 지정합니다.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>구성에 대한 ExpressRoute 회로 식별
두 ExpressRoute 회로가 지원되는 국가에 위치하고 서로 다른 피어링 위치에서 만들어진 경우에 한하여 ExpressRoute Global Reach를 사용하도록 설정할 수 있습니다. 사용자 구독이 두 회로 모두를 소유하는 경우 아래 섹션에서 구성을 실행할 회로를 하나 선택할 수 있습니다. 두 회로가 서로 다른 Azure 구독에 있는 경우 한 Azure 구독에서 권한을 부여하고, 다른 Azure 구독에서 구성 명령을 실행할 때 권한 부여 키를 전달해야 합니다.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>온-프레미스 네트워크 간 연결 사용

이 명령을 실행하여 연결을 사용하도록 설정할 경우 다음 값을 고려하세요.

* *peer-circuit*은 전체 리소스 ID여야 합니다. 예:  

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* *-AddressPrefix*는 /29 IPv4 서브넷이어야 합니다. 예를 들어, 다음과 같습니다. “10.0.0.0/29”입니다. 두 ExpressRoute 회로 간의 연결을 설정하는 데 이 서브넷의 IP 주소가 사용됩니다. Azure VNet 또는 온-프레미스 네트워크에서는 이 서브넷의 주소를 사용하지 말아야 합니다.

다음 CLI를 실행하여 두 개의 ExpressRoute 회로를 연결합니다. 다음 예제 명령을 사용합니다.

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI 출력은 다음 예제와 유사합니다.

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

위의 작업이 완료되면 두 ExpressRoute 회로를 통해 양쪽에 있는 온-프레미스 네트워크 간에 연결이 설정됩니다.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>서로 다른 Azure 구독의 ExpressRoute 회로

두 회로가 동일한 Azure 구독에 있지 않으면 권한 부여가 필요합니다. 다음 구성에서는 회로 2의 구독에서 권한 부여가 생성되고 권한 부여 키가 회로 1에 전달됩니다.

권한 부여 키를 생성합니다. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

CLI 출력은 다음과 같이 표시됩니다.

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

회로 2의 리소스 ID와 함께 권한 부여 키를 기록해 둡니다.

회로 1에서 다음 명령을 실행합니다. 회로 2의 리소스 ID 및 권한 부여 키 전달 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

위의 작업이 완료되면 두 ExpressRoute 회로를 통해 양쪽에 있는 온-프레미스 네트워크 간에 연결이 설정됩니다.

## <a name="get-and-verify-the-configuration"></a>구성을 가져와서 확인

구성이 수행된 회로에서 구성을 확인하려면 다음 명령을 사용합니다. 예를 들어, 위의 예에서는 회로 1입니다.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

CLI 출력에는 *CircuitConnectionStatus*가 표시됩니다. 두 회로 간에 연결이 설정되면 “연결됨”, 설정되지 않으면 “연결 끊김”이 표시됩니다. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>온-프레미스 네트워크 간 연결을 사용 안 함

이를 사용하지 않으려면 구성이 수행된 회로에 대해 이 명령을 실행하세요. 예를 들어, 위의 예제에서는 회로 1입니다.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

CLI 표시를 실행하여 상태를 확인할 수 있습니다. 

위의 작업이 완료되면 사용자의 ExpressRoute 회로를 통해 온-프레미스 네트워크 간에 연결이 해제됩니다. 


## <a name="next-steps"></a>다음 단계
* [ExpressRoute Global Reach에 대해 자세히 알아봅니다.](expressroute-global-reach.md)
* [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
* [Azure 가상 네트워크에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)


