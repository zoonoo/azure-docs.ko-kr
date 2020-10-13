---
title: Azure Service Bus에 대 한 가상 네트워크 서비스 끝점 구성
description: 이 문서에서는 가상 네트워크에 ServiceBus 서비스 끝점을 추가 하는 방법에 대 한 정보를 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1b62f69bad4484239b3a6c5d6f7ae910fbdef03f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843382"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>특정 가상 네트워크에서 Azure Service Bus 네임 스페이스에 대 한 액세스 허용

Service Bus와 [VNet (Virtual Network) 서비스 끝점][vnet-sep] 을 통합 하면 가상 네트워크에 바인딩된 가상 머신과 같은 워크 로드에서 메시지 기능에 안전 하 게 액세스할 수 있으며, 두 쪽 모두에서 네트워크 트래픽 경로를 안전 하 게 보호할 수 있습니다.

하나 이상의 가상 네트워크 서브넷 서비스 끝점에 바인딩되도록 구성 된 경우 해당 Service Bus 네임 스페이스는 더 이상 권한이 있는 가상 네트워크 및 선택적으로 특정 인터넷 IP 주소 로부터의 트래픽을 허용 하지 않습니다. 가상 네트워크 큐브 뷰에서 Service Bus 네임스페이스를 서비스 엔드포인트에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널을 구성합니다.

메시징 서비스 엔드포인트의 관찰 가능한 네트워크 주소가 공용 IP 범위에 있음에도 서브넷에 바인딩된 워크로드와 해당하는 Service Bus 네임스페이스 간에 격리된 프라이빗 관계가 생성됩니다.

>[!WARNING]
> Virtual Networks 통합을 구현하면 다른 Azure 서비스가 Service Bus와 상호 작용하지 않도록 방지할 수 있습니다.
>
> 신뢰할 수 있는 Microsoft 서비스는 Virtual Networks가 구현되는 시점에 지원되지 않습니다.
>
> Virtual Networks가 작동하지 않는 일반적인 Azure 시나리오(목록은 전체 목록이 **아님**) -
> - Azure Event Grid와 통합
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
>
> 아래 Microsoft 서비스는 가상 네트워크에 있어야 합니다.
> - Azure App Service
> - Azure 기능
> - Azure Monitor(진단 설정)

> [!IMPORTANT]
> Virtual Network는 [프리미엄 계층](service-bus-premium-messaging.md) Service Bus 네임스페이스에서만 지원됩니다.
> 
> Service Bus에서 VNet 서비스 끝점을 사용 하는 경우 표준 및 프리미엄 계층 Service Bus 네임 스페이스를 혼합 하는 응용 프로그램에서 이러한 끝점을 사용 하도록 설정 하면 안 됩니다. 표준 계층은 Vnet를 지원 하지 않습니다. 끝점은 프리미엄 계층 네임 스페이스로만 제한 됩니다.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 통합에서 사용하도록 설정한 고급 보안 시나리오 

엄격하게 분류된 보안이 필요한 솔루션은 일반적으로 해당 구획에 위치한 서비스 간에 통신 경로가 필요합니다. 해당 솔루션에서 가상 네트워크 서브넷은 분류된 서비스 간를 구분합니다.

TCP/IP에서 HTTPS를 수행하는 경로를 비롯한 구획 간의 즉시 IP 경로를 사용하면 네트워크 레이어에서 취약성을 악용할 위험이 있습니다. 메시징 서비스는 완전히 격리된 통신 경로를 제공합니다. 여기서 메시지는 항목을 전환할 때 디스크에도 작성됩니다. 동일한 Service Bus 인스턴스에 바인딩된 두 개의 고유 가상 네트워크에 있는 워크로드는 메시지를 통해 효율적이고 안정적으로 통신할 수 있습니다. 이 동안에 해당 네트워크 격리 경계의 무결성은 지켜집니다.
 
즉, 보안이 중요한 클라우드 솔루션은 Azure에서 제공하는 업계 최고의 안정적이고 확장성 있는 비동기 메시징 기능에 액세스할 뿐만 아니라 메시징을 사용하여 HTTPS 및 기타 TLS 보안 소켓 프로토콜을 비롯한 보안 솔루션 구획(피어 간 통신 모드로 달성 가능한 수준보다 본질적으로 더 안전함) 간에 통신을 만들 수도 있습니다.

## <a name="binding-service-bus-to-virtual-networks"></a>Virtual Networks에 Service Bus 바인딩

*가상 네트워크 규칙*은 Azure Service Bus 서버가 특정 가상 네트워크 서브넷의 연결을 수락할지 여부를 제어하는 방화벽 보안 기능입니다.

Virtual Networks에 Service Bus를 바인딩하는 작업은 2단계 프로세스입니다. 먼저 Virtual Network 서브넷에 **Virtual Network 서비스 끝점** 을 만들고 [서비스 끝점 개요][vnet-sep]에 설명 된 대로 **ServiceBus** 에 대해 사용 하도록 설정 해야 합니다. 서비스 엔드포인트를 추가했다면 여기에 **가상 네트워크 규칙**을 사용하여 Service Bus 네임스페이스를 바인딩합니다.

가상 네트워크 규칙은 가상 네트워크 서브넷을 사용하는 Service Bus 네임스페이스의 연결입니다. 규칙이 있는 한 서브넷에 바인딩된 모든 워크로드는 Service Bus 네임스페이스에 대한 액세스 권한이 부여됩니다. Service Bus 자체는 아웃바운드 연결을 설정하지 않고, 액세스 권한을 가져올 필요도 없습니다. 따라서 이 규칙을 사용하여 서브넷에 대한 액세스 권한이 부여되지 않습니다.

> [!NOTE]
> 네트워크 서비스 끝점은 가상 네트워크에서 실행 되는 응용 프로그램에 Service Bus 네임 스페이스에 대 한 액세스를 제공 합니다. 가상 네트워크는 끝점의 연결을 제어 하지만 Service Bus 엔터티 (큐, 토픽 또는 구독)에서 수행할 수 있는 작업은 제공 하지 않습니다. Azure AD (Azure Active Directory)를 사용 하 여 응용 프로그램이 네임 스페이스 및 해당 엔터티에 대해 수행할 수 있는 작업에 권한을 부여 합니다. 자세한 내용은 [Service Bus 엔터티에 액세스 하기 위해 AZURE AD를 사용 하 여 응용 프로그램 인증 및 권한 부여](authenticate-application.md)를 참조 하세요.


## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal를 사용 하 여 가상 네트워크 서비스 끝점을 추가 하는 방법을 보여 줍니다. 액세스를 제한 하려면이 Event Hubs 네임 스페이스에 대 한 가상 네트워크 서비스 끝점을 통합 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Service Bus 네임스페이스**로 이동합니다.
2. 왼쪽 메뉴의 **설정**에서 **네트워킹** 옵션을 선택 합니다.  

    > [!NOTE]
    > **프리미엄** 네임 스페이스에 대 한 **네트워킹** 탭만 표시 됩니다.  
    
    기본적으로 **선택한 네트워크** 옵션이 선택 되어 있습니다. 이 페이지에 하나 이상의 IP 방화벽 규칙 또는 가상 네트워크를 추가 하지 않으면 공용 인터넷을 통해 네임 스페이스에 액세스할 수 있습니다 (액세스 키 사용).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="네트워킹 페이지-기본값" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    **모든 네트워크** 옵션을 선택 하는 경우 Service Bus 네임 스페이스는 모든 IP 주소의 연결을 허용 합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![방화벽 - 모든 네트워크 옵션 선택됨](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. 특정 가상 네트워크에 대 한 액세스를 제한 하려면 **선택한 네트워크** 옵션이 선택 되어 있지 않은 경우 선택 합니다.
1. 페이지의 **Virtual Network** 섹션에서 **+ 기존 가상 네트워크 추가**를 선택 합니다. 

    ![기존 가상 네트워크 추가](./media/service-endpoints/add-vnet-menu.png)
3. 가상 네트워크 목록에서 가상 네트워크를 선택 하 고 **서브넷**을 선택 합니다. 목록에 가상 네트워크를 추가 하기 전에 서비스 끝점을 사용 하도록 설정 해야 합니다. 서비스 끝점이 사용 되도록 설정 되지 않은 경우 포털에서 사용 하도록 설정 하 라는 메시지를 표시 합니다.
   
   ![서브넷 선택](./media/service-endpoints/select-subnet.png)

4. **ServiceBus**에 대 한 서브넷에 대 한 서비스 끝점을 사용 하도록 설정한 후 다음과 같은 성공적인 메시지가 표시 됩니다. 페이지 맨 아래에서 **추가** 를 선택 하 여 네트워크를 추가 합니다. 

    ![서브넷 선택 및 엔드포인트 설정](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > 서비스 끝점을 사용 하도록 설정할 수 없는 경우 리소스 관리자 템플릿을 사용 하 여 누락 된 가상 네트워크 서비스 끝점을 무시할 수 있습니다. 이 기능은 포털에서 사용할 수 없습니다.
6. 도구 모음에서 **저장**을 선택하여 설정을 저장합니다. 확인이 포털 알림에서 표시 될 때까지 몇 분 정도 기다립니다. **저장** 단추를 사용 하지 않도록 설정 해야 합니다. 

    ![네트워크 저장](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > 특정 IP 주소 또는 범위에서 액세스를 허용 하는 방법에 대 한 지침은 [특정 ip 주소 또는 범위에서 액세스 허용](service-bus-ip-filtering.md)을 참조 하세요.

## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
다음과 같은 Resource Manager 템플릿을사용 하면 기존 Service Bus 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

* **namespaceName**: Service Bus 네임스페이스입니다.
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

템플릿:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
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
                    "service": "Microsoft.ServiceBus"
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
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
- [Azure Service Bus IP 필터링][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
