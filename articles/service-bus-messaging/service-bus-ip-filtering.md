---
title: Azure Service Bus에 대한 IP 방화벽 규칙 구성
description: 특정 IP 주소에서 Azure Service Bus로 연결을 허용하도록 방화벽 규칙을 사용하는 방법입니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2ba6b1902213af8d968b220a387e419e56e8aa67
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91300958"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>특정 IP 주소 또는 범위에서 Azure Service Bus 네임 스페이스에 대 한 액세스 허용
기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 Service Bus 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트에서만 Azure Service Bus에 액세스할 수 있는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생하는 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route][express-route]에서 Service Bus를 사용하는 경우 온-프레미스 인프라 IP 주소 또는 회사 NAT 게이트웨이 주소로부터의 트래픽만 허용하도록 **방화벽 규칙**을 만들 수 있습니다. 

> [!IMPORTANT]
> 방화벽 및 가상 네트워크는 **프리미엄 계층** Service Bus에서만 지원됩니다. **프리미어** 계층으로 업그레이드할 수 없는 경우에는 SAS(공유 액세스 서명) 토큰을 안전하게 보관하고 권한 있는 사용자만 공유하는 것이 좋습니다. SAS 인증에 대한 자세한 내용은 [인증 및 권한 부여](service-bus-authentication-and-authorization.md#shared-access-signature)를 참조하세요.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 Service Bus 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Service Bus 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

>[!WARNING]
> 방화벽 규칙을 구현하면 다른 Azure 서비스가 Service Bus와 상호 작용하는 것을 방지할 수 있습니다.
>
> 신뢰할 수 있는 Microsoft 서비스는 IP 필터링(방화벽 규칙)이 구현되는 시점에 지원되지 않으며 곧 제공될 예정입니다.
>
> IP 필터링이 작동하지 않는 일반적인 Azure 시나리오(목록은 전체 목록이 **아님**) -
> - Azure Event Grid와 통합
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
>
> 다음 Microsoft 서비스는 가상 네트워크에 있어야 합니다.
> - Azure App Service
> - Azure 기능
> - Azure Monitor(진단 설정)

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal을 사용하여 Service Bus 네임스페이스에 대한 IP 방화벽 규칙을 만드는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Service Bus 네임스페이스**로 이동합니다.
2. 왼쪽 메뉴의 **설정**에서 **네트워킹** 옵션을 선택 합니다.  

    > [!NOTE]
    > **프리미엄** 네임 스페이스에 대 한 **네트워킹** 탭만 표시 됩니다.  
    
    기본적으로 **선택한 네트워크** 옵션이 선택 되어 있습니다. 이 페이지에 하나 이상의 IP 방화벽 규칙 또는 가상 네트워크를 추가 하지 않으면 공용 인터넷을 통해 네임 스페이스에 액세스할 수 있습니다 (액세스 키 사용).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="네트워킹 페이지-기본값" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    **모든 네트워크** 옵션을 선택 하는 경우 Service Bus 네임 스페이스는 모든 IP 주소의 연결을 허용 합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![Azure Portal 네트워킹 페이지의 스크린샷 모든 네트워크에서 액세스를 허용 하는 옵션은 방화벽 및 가상 네트워크 탭에서 선택 합니다.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 지정 된 IP 주소 에서만 액세스를 허용 하려면 선택 된 **네트워크** 옵션이 선택 되어 있지 않은 경우 선택 합니다. **방화벽** 섹션에서 다음 단계를 수행합니다.
    1. **클라이언트 IP 주소 추가** 옵션을 선택하여 현재 클라이언트 IP에 네임스페이스에 대한 액세스 권한을 부여합니다. 
    2. **주소 범위**에 CIDR 표기법으로 특정 IPv4 주소 또는 IPv4 주소 범위를 입력합니다. 
    3. **신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 바이패스하도록 허용**할지 여부를 지정합니다. 

        > [!WARNING]
        > **선택한 네트워크** 옵션을 선택하고 IP 주소 또는 주소 범위를 지정하지 않으면 서비스는 모든 네트워크에 들어오는 트래픽을 허용합니다. 

        ![Azure Portal 네트워킹 페이지의 스크린샷 선택한 네트워크에서 액세스를 허용 하는 옵션이 선택 되 고 방화벽 섹션이 강조 표시 됩니다.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 도구 모음에서 **저장**을 선택하여 설정을 저장합니다. 포털 알림에 확인이 표시될 때가지 몇 분 정도 기다립니다.

    > [!NOTE]
    > 특정 가상 네트워크에 대 한 액세스를 제한 하려면 [특정 네트워크에서 액세스 허용](service-bus-service-endpoints.md)을 참조 하세요.

## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
이 섹션에는 가상 네트워크 및 방화벽 규칙을 만드는 샘플 Azure Resource Manager 템플릿이 있습니다.


다음과 같은 Resource Manager 템플릿을사용 하면 기존 Service Bus 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

- **ipMask**는 단일 IPv4 주소 또는 CIDR 표기법인 IP 주소 블록입니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

## <a name="next-steps"></a>다음 단계

Service Bus에 대한 액세스를 Azure 가상 네트워크로 제한하려면 다음 링크를 참조하세요.

- [Service Bus의 가상 네트워크 서비스 엔드포인트][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services