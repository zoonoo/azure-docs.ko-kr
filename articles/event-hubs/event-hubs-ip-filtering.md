---
title: Azure Event Hubs 방화벽 규칙 | Microsoft Docs
description: 특정 IP 주소에서 Azure Event Hubs로 연결을 차단하도록 방화벽 규칙을 사용합니다.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 12240135401b267fd7c60e579fdf5a12e10ffce9
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963005"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>특정 IP 주소나 범위에서 Azure Event Hubs 네임스페이스에 대한 액세스 허용
기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 Event Hubs 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트에서만 Azure Event Hub에 액세스할 수 있는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생하는 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route][express-route]와 함께 Event Hubs를 사용하는 경우 온-프레미스 인프라 IP 주소에서 오는 트래픽만 허용하도록 **방화벽 규칙** 을 만들 수 있습니다. 

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 Event Hubs 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Event Hubs 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.


## <a name="important-points"></a>중요 사항
- 이 기능은 **표준** 계층과 **전용** 계층 모두에서 지원됩니다. **기본** 계층에서는 지원되지 않습니다.
- Event Hubs 네임스페이스의 방화벽 규칙을 켜면 허용된 공용 IP 주소에서 작동하는 서비스에서 요청하는 경우를 제외하고, 기본적으로 들어오는 요청은 차단됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다. 예외적으로 IP 필터링이 사용으로 설정된 경우에도 특정 **신뢰할 수 있는 서비스** 에서 Event Hubs 리소스에 대한 액세스를 허용할 수 있습니다. 신뢰할 수 있는 서비스 목록은 [신뢰할 수 있는 Microsoft 서비스](#trusted-microsoft-services)를 참조하세요.
- 지정된 IP 주소 또는 가상 네트워크의 서브넷에서만 트래픽을 허용하도록 네임스페이스에 대해 **하나 이상의 IP 방화벽 규칙 또는 가상 네트워크 규칙** 을 지정합니다. IP 및 가상 네트워크 규칙이 없는 경우 액세스 키를 사용하여 퍼블릭 인터넷을 통해 네임스페이스에 액세스할 수 있습니다.  


## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal을 사용하여 Event Hubs 네임스페이스에 대한 IP 방화벽 규칙을 만드는 방법을 보여줍니다. 

1. **Azure Portal** 에서 [Event Hubs 네임스페이스](https://portal.azure.com)로 이동합니다.
4. 왼쪽 메뉴의 **설정** 에서 **네트워킹** 을 선택합니다. **표준** 또는 **전용** 네임스페이스에 대해서만 **네트워킹** 탭이 표시됩니다. 
    
    > [!WARNING]
    > **선택한 네트워크** 옵션을 선택하고 이 페이지에 하나 이상의 IP 방화벽 규칙이나 가상 네트워크를 추가하지 않으면 액세스 키를 사용하여 **퍼블릭 인터넷** 을 통해 네임스페이스에 액세스할 수 있습니다.  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="네트워크 탭 - 선택한 네트워크 옵션" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    **모든 네트워크** 옵션을 선택하면 이벤트 허브는 액세스 키를 사용한 모든 IP 주소에서의 연결을 허용합니다. 이 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![“모든 네트워크” 옵션이 선택된 “방화벽 및 가상 네트워크” 페이지를 보여 주는 스크린샷.](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 특정 IP 주소에 대한 액세스를 제한하려면 **선택한 네트워크** 옵션이 선택되었는지 확인합니다. **방화벽** 섹션에서 다음 단계를 수행합니다.
    1. **클라이언트 IP 주소 추가** 옵션을 선택하여 현재 클라이언트 IP에 네임스페이스에 대한 액세스 권한을 부여합니다. 
    2. **주소 범위** 에 CIDR 표기법으로 특정 IPv4 주소 또는 IPv4 주소 범위를 입력합니다. 

    >[!WARNING]
    > **선택한 네트워크** 옵션을 선택하고 이 페이지에 하나 이상의 IP 방화벽 규칙이나 가상 네트워크를 추가하지 않으면 액세스 키를 사용하여 퍼블릭 인터넷을 통해 네임스페이스에 액세스할 수 있습니다.
1. **신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 바이패스하도록 허용** 할지 여부를 지정합니다. 자세한 내용은 [신뢰할 수 있는 Microsoft 서비스](#trusted-microsoft-services)를 참조하세요. 

      ![방화벽 - 모든 네트워크 옵션 선택됨](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. 도구 모음에서 **저장** 을 선택하여 설정을 저장합니다. 포털 알림에 확인이 표시될 때가지 몇 분 정도 기다립니다.

    > [!NOTE]
    > 특정 가상 네트워크에 대한 액세스를 제한하려면 [특정 네트워크에서 액세스 허용](event-hubs-service-endpoints.md)을 참조하세요.

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용

> [!IMPORTANT]
> 방화벽 규칙은 Event Hubs의 **표준** 및 **전용** 계층에서 지원됩니다. 기본 계층에서는 지원되지 않습니다.

다음과 같은 Resource Manager 템플릿을 사용하면 기존 Event Hubs 네임스페이스에 IP 필터 규칙을 추가할 수 있습니다.

템플릿의 **ipMask** 는 단일 IPv4 주소 또는 CIDR 표기법인 IP 주소 블록입니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

가상 네트워크나 방화벽 규칙을 추가할 `defaultAction`의 값을 `Deny`로 설정합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

> [!IMPORTANT]
> IP와 가상 네트워크 규칙이 없으면 `defaultAction`을 `deny`로 설정하더라도 모든 트래픽 흐름이 네임스페이스로 이동합니다.  퍼블릭 인터넷을 통해 네임스페이스에 액세스할 수 있습니다(액세스 키 사용). 지정된 IP 주소 또는 가상 네트워크의 서브넷에서만 트래픽을 허용하도록 네임스페이스에 대해 하나 이상의 IP 규칙 또는 가상 네트워크 규칙을 지정합니다.  

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 액세스를 Azure 가상 네트워크로 제한하려면 다음 링크를 참조하세요.

- [Event Hubs용 Virtual Network Service 엔드포인트][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
