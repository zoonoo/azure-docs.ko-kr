---
title: Azure 서비스 버스에 대한 IP 방화벽 규칙 구성
description: 특정 IP 주소에서 Azure Service Bus로 연결을 허용하도록 방화벽 규칙을 사용하는 방법입니다.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 24591c20ed707d9541eece0698ecd6e6b5ddee35
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878190"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Azure 서비스 버스에 대한 IP 방화벽 규칙 구성
기본적으로 Service Bus 네임스페이스는 유효한 인증 및 권한 부여와 함께 요청이 제공되는 한 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(클래스리스 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법의 IPv4 주소 또는 IPv4 주소 범위 집합으로만 제한할 수 있습니다.

이 기능은 Azure Service Bus가 잘 알려진 특정 사이트에서만 액세스할 수 있어야 하는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생한 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route를][express-route]사용하여 서비스 버스를 사용하는 경우 온-프레미스 인프라 IP 주소 또는 회사 NAT 게이트웨이의 주소에서만 트래픽을 허용하는 **방화벽 규칙을** 만들 수 있습니다. 

> [!IMPORTANT]
> 방화벽과 가상 네트워크는 서비스 버스의 **프리미엄** 계층에서만 지원됩니다. **프리미어** 계층으로 업그레이드하는 것이 옵션이 아닌 경우 SAS(공유 액세스 서명) 토큰을 안전하게 유지하고 권한이 있는 사용자와만 공유하는 것이 좋습니다. SAS 인증에 대한 자세한 내용은 [인증 및 권한 부여를](service-bus-authentication-and-authorization.md#shared-access-signature)참조하십시오.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 Service Bus 네임스페이스 수준에서 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Service Bus 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure 포털을 사용하여 Service Bus 네임스페이스에 대한 IP 방화벽 규칙을 만드는 방법을 보여 주십니다. 

1. [Azure 포털에서](https://portal.azure.com) **서비스 버스 네임스페이스로** 이동합니다.
2. 왼쪽 메뉴에서 **네트워킹** 옵션을 선택합니다. 기본적으로 모든 **네트워크** 옵션이 선택됩니다. 서비스 버스 네임스페이스는 모든 IP 주소의 연결을 허용합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![방화벽 - 선택한 모든 네트워크 옵션](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. 페이지 상단에서 **선택한 네트워크** 옵션을 선택합니다. **방화벽** 섹션에서 다음 단계를 따르십시오.
    1. 클라이언트 **IP 주소 추가** 옵션을 선택하여 현재 클라이언트 IP에 네임스페이스에 대한 액세스 권한을 부여합니다. 
    2. **주소 범위의**경우 CIDR 표기판에 특정 IPv4 주소 또는 IPv4 주소 범위를 입력합니다. 
    3. **신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용할지**여부를 지정합니다. 

        ![방화벽 - 선택한 모든 네트워크 옵션](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. 설정을 저장하려면 도구 모음에 **저장을** 선택합니다. 확인이 포털 알림에 표시될 때까지 몇 분 동안 기다립니다.

## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
이 섹션에는 가상 네트워크 및 방화벽 규칙을 만드는 샘플 Azure 리소스 관리자 템플릿이 있습니다.


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

- [Service Bus의 Virtual Network 서비스 엔드포인트][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
