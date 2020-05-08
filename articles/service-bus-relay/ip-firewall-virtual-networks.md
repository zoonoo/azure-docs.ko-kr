---
title: Azure Relay 네임 스페이스에 대 한 Congigure IP 방화벽
description: 이 문서에서는 방화벽 규칙을 사용 하 여 특정 IP 주소에서 Azure Relay 네임 스페이스에 대 한 연결을 허용 하는 방법을 설명 합니다.
services: service-bus-relay
documentationcenter: ''
author: spelluru
ms.service: service-bus-relay
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: e2a9e89ca8c3b77b09a58a45959f1acda1457b56
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984812"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Azure Relay 네임 스페이스에 대 한 IP 방화벽 구성
기본적으로 요청에 유효한 인증 및 권한 부여가 제공 되는 한, 릴레이 네임 스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용 하면 [CIDR (클래스 없는 도메인 간 라우팅)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법에서 ipv4 주소 또는 ipv4 주소 범위 집합 으로만 제한할 수 있습니다.

이 기능은 Azure Relay 잘 알려진 특정 사이트 에서만 액세스할 수 있어야 하는 경우에 유용 합니다. 방화벽 규칙을 사용 하면 특정 IPv4 주소에서 시작 되는 트래픽을 허용 하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express 경로](../expressroute/expressroute-faqs.md#supported-services)와 릴레이를 사용 하는 경우 온-프레미스 인프라 IP 주소의 트래픽만 허용 하는 **방화벽 규칙** 을 만들 수 있습니다. 


> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. 


## <a name="enable-ip-firewall-rules"></a>IP 방화벽 규칙 사용
IP 방화벽 규칙은 네임 스페이스 수준에서 적용 됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. 네임 스페이스에서 허용 되는 IP 규칙과 일치 하지 않는 IP 주소 로부터의 연결 시도는 권한 없음으로 거부 됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

### <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal를 사용 하 여 네임 스페이스에 대 한 IP 방화벽 규칙을 만드는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com)에서 **릴레이 네임 스페이스** 로 이동 합니다.
2. 왼쪽 메뉴에서 **네트워킹** 옵션을 선택 합니다. 다음 **에서 액세스 허용** 섹션에서 **모든 네트워크** 옵션을 선택 하는 경우 릴레이 네임 스페이스는 모든 IP 주소의 연결을 허용 합니다. 이 설정은 0.0.0.0/0 IP 주소 범위를 허용 하는 규칙과 동일 합니다. 

    ![방화벽-모든 네트워크 옵션을 선택 합니다.](./media/ip-firewall/all-networks-selected.png)
1. 특정 네트워크 및 IP 주소에 대 한 액세스를 제한 하려면 **선택한 네트워크** 옵션을 선택 합니다. **방화벽** 섹션에서 다음 단계를 수행 합니다.
    1. **클라이언트 ip 주소 추가** 옵션을 선택 하 여 현재 클라이언트 ip에 네임 스페이스에 대 한 액세스 권한을 부여 합니다. 
    2. **주소 범위**에 대해 CIDR 표기법으로 특정 ipv4 주소 또는 ipv4 주소 범위를 입력 합니다. 
    3. **신뢰할 수 있는 Microsoft 서비스가이 방화벽을 우회 하도록 허용할지**여부를 지정 합니다. 

        ![방화벽-모든 네트워크 옵션을 선택 합니다.](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. 도구 모음에서 **저장** 을 선택 하 여 설정을 저장 합니다. 확인이 포털 알림에 표시 될 때까지 몇 분 정도 기다립니다.


### <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
다음 리소스 관리자 템플릿에서는 기존 릴레이 네임 스페이스에 IP 필터 규칙을 추가할 수 있습니다.

템플릿은 단일 IPv4 주소 또는 CIDR 표기법의 IP 주소 블록인 **Ipmask**의 매개 변수 하나를 사용 합니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

템플릿을 배포하려면 [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)에 대한 지침을 따르세요.



## <a name="next-steps"></a>다음 단계
다른 네트워크 보안 관련 기능에 대 한 자세한 내용은 [네트워크 보안](network-security.md)을 참조 하세요.


<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[l n k-배포]:: 
