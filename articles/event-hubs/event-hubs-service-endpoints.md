---
title: Virtual Network 서비스 엔드포인트 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 가상 네트워크에 Microsoft.EventHub 서비스 끝점을 추가하는 방법에 대한 정보를 제공합니다.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 6de51c23bd6358a6f54fe3baf9e9b256047d4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064887"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Azure Event Hubs에서 Virtual Network 서비스 엔드포인트 사용

[VNet(Virtual Network) 서비스 엔드포인트][vnet-sep]와 Event Hubs를 통합하면 양쪽 엔드에서 네트워크 트래픽 경로를 보호하여 가상 네트워크에 바인딩된 가상 머신과 같은 워크로드의 메시징 기능에 대한 액세스를 보호할 수 있습니다.

하나 이상의 가상 네트워크 서브넷 서비스 엔드포인트에 바인딩하도록 구성된 각 Event Hubs 네임스페이스는 더 이상 가상 네트워크의 권한이 부여된 서브넷을 제외한 다른 곳에서의 트래픽을 허용하지 않습니다. 가상 네트워크 큐브 뷰에서 Event Hubs 네임스페이스를 서비스 엔드포인트에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널을 구성합니다. 

메시징 서비스 엔드포인트의 관찰 가능한 네트워크 주소가 공용 IP 범위에 있음에도 서브넷에 바인딩된 워크로드와 해당하는 Event Hubs 네임스페이스 간에 격리된 프라이빗 관계가 생성됩니다. 이 동작에는 예외가 있습니다. 기본적으로 서비스 끝점을 사용하도록 설정하면 `denyall` 가상 네트워크와 연결된 [IP 방화벽의](event-hubs-ip-filtering.md) 규칙을 사용할 수 있습니다. IP 방화벽에 특정 IP 주소를 추가하여 Event Hub 공용 끝점에 액세스할 수 있습니다. 

> [!IMPORTANT]
> 가상 네트워크는 Event Hubs의 **표준** 및 **전용** 계층에서 지원되며 **기본** 계층에서는 지원되지 않습니다.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 통합에서 사용하도록 설정한 고급 보안 시나리오 

단단하고 구획화된 보안이 필요하고 가상 네트워크 서브넷이 구획화된 서비스 간의 세분화를 제공하는 솔루션은 여전히 해당 구획에 상주하는 서비스 간의 통신 경로가 필요합니다.

TCP/IP에서 HTTPS를 수행하는 경로를 비롯한 구획 간의 즉시 IP 경로를 사용하면 네트워크 레이어에서 취약성을 악용할 위험이 있습니다. 메시징 서비스는 격리된 통신 경로를 제공하며, 여기서 메시지는 당사자 간에 전환할 때 디스크에 기록됩니다. 동일한 Event Hubs 인스턴스에 바인딩된 두 개의 고유 가상 네트워크에 있는 워크로드는 메시지를 통해 효율적이고 안정적으로 통신할 수 있습니다. 이 동안에 해당 네트워크 격리 경계의 무결성은 지켜집니다.
 
즉, 보안이 중요한 클라우드 솔루션은 Azure에서 제공하는 업계 최고의 안정적이고 확장성 있는 비동기 메시징 기능에 액세스할 뿐만 아니라 메시징을 사용하여 HTTPS 및 기타 TLS 보안 소켓 프로토콜을 비롯한 보안 솔루션 구획(피어 간 통신 모드로 달성 가능한 수준보다 본질적으로 더 안전함) 간에 통신을 만들 수도 있습니다.

## <a name="bind-event-hubs-to-virtual-networks"></a>이벤트 허브를 가상 네트워크에 바인딩

**가상 네트워크 규칙**은 Azure Event Hubs 네임스페이스가 특정 가상 네트워크 서브넷의 연결을 수락할지 여부를 제어하는 방화벽 보안 기능입니다.

Virtual Networks에 Event Hubs를 바인딩하는 작업은 2단계 프로세스입니다. 먼저 가상 네트워크의 서브넷에 **가상 네트워크 서비스 끝점을** 만들고 [서비스 끝점 개요][vnet-sep] 문서에 설명된 대로 **Microsoft.EventHub에** 사용하도록 설정해야 합니다. 서비스 엔드포인트를 추가했다면 여기에 **가상 네트워크 규칙**을 사용하여 Event Hubs 네임스페이스를 바인딩합니다.

가상 네트워크 규칙은 가상 네트워크 서브넷을 사용하는 Event Hubs 네임스페이스의 연결입니다. 규칙이 있는 한 서브넷에 바인딩된 모든 워크로드는 Event Hubs 네임스페이스에 대한 액세스 권한이 부여됩니다. 이벤트 허브 자체는 아웃바운드 연결을 설정하지 않으며, 액세스 권한을 얻을 필요가 없으므로 이 규칙을 사용하도록 설정하여 서브넷에 대한 액세스 권한을 부여하지 않습니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure 포털을 사용하여 가상 네트워크 서비스 끝점을 추가하는 방법을 보여 주며, 이 섹션에서는 액세스를 제한하려면 이 이벤트 허브 네임스페이스에 대한 가상 네트워크 서비스 끝점을 통합해야 합니다.

1. [Azure 포털에서](https://portal.azure.com) **이벤트 허브 네임스페이스로** 이동합니다.
2. 왼쪽 메뉴에서 **네트워킹** 옵션을 선택합니다. **모든 네트워크** 옵션을 선택하면 이벤트 허브는 모든 IP 주소의 연결을 허용합니다. 이 설정은 0.0.0.0.0/0 IP 주소 범위를 허용하는 규칙과 동일합니다. 

    ![방화벽 - 선택한 모든 네트워크 옵션](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 특정 네트워크에 대한 액세스를 중단하려면 페이지 상단에서 **선택한 네트워크** 옵션을 선택합니다.
2. 페이지의 **가상 네트워크** 섹션에서 **+기존 가상 네트워크 추가***를 선택합니다. **+ 새 VNet을** 만들려면 새 가상 네트워크 만들기를 선택합니다. 

    ![기존 가상 네트워크 추가](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. 가상 네트워크 목록에서 가상 네트워크를 선택한 다음 **서브넷을**선택합니다. 목록에 가상 네트워크를 추가하기 전에 서비스 끝점을 사용하도록 설정해야 합니다. 서비스 끝점을 사용하도록 설정하지 않으면 포털에서 이를 사용하도록 설정하라는 메시지가 표시됩니다.
   
   ![서브넷 선택](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. 서브넷에 대한 서비스 끝점이 **Microsoft.EventHub**에 대해 활성화된 후 다음과 같은 성공적인 메시지가 표시됩니다. 페이지를 추가하려면 페이지 하단에 **추가를** 선택합니다. 

    ![서브넷 선택 및 엔드포인트 설정](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 서비스 끝점을 활성화할 수 없는 경우 Resource Manager 템플릿을 사용하여 누락된 가상 네트워크 서비스 끝점을 무시할 수 있습니다. 이 기능은 포털에서 사용할 수 없습니다.
6. 설정을 저장하려면 도구 모음에 **저장을** 선택합니다. 확인이 포털 알림에 표시될 때까지 몇 분 동안 기다립니다.

    ![네트워크 저장](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용

다음과 같은 Resource Manager 템플릿을사용 하면 기존 Event Hubs 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

* **namespaceName**: Event Hubs 네임스페이스입니다.
* **vnetRuleName**: 만들 Virtual Network 규칙의 이름입니다.
* **virtualNetworkingSubnetId**: 가상 네트워크 서브넷에 대해 정규화된 Resource Manager 경로입니다(예: 가상 네트워크 기본 서브넷의 경우 `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`).

> [!NOTE]
> 가능한 거부 규칙은 없지만 Azure Resource Manager 템플릿은 기본 작업이 **"허용"** 으로 설정되며 연결을 제한하지 않습니다.
> Virtual Network 또는 방화벽 규칙을 만들 때 ***"defaultAction"*** 을 변경해야 합니다.
> 
> 원본
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 가상 네트워크 서비스 엔드포인트][vnet-sep]
- [Azure Event Hubs IP 필터링][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
