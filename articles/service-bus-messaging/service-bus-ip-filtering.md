---
title: Azure Service Bus IP 연결 필터 | Microsoft Docs
description: 특정 IP 주소에서 Azure Service Bus로 연결을 차단하도록 IP 필터링을 사용하는 방법입니다.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: clemensv
ms.openlocfilehash: c6e9eef762d4a9eb95685d94c61ce10d499bb155
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884806"
---
# <a name="use-ip-filters"></a>IP 필터 사용

잘 알려진 특정 사이트에서 Azure Service Bus만이 엑세스 가능한 시나리오의 경우 *IP 필터* 기능을 사용하면 특정 IPv4 주소에서 시작된 트래픽을 거부하거나 허용하는 규칙을 구성할 수 있습니다. 예를 들어 이 주소는 회사 NAT 게이트웨이의 주소일 것입니다.

## <a name="when-to-use"></a>사용하는 경우

특정 IP 주소에 대해 Service Bus 엔드포인트를 차단하는 것이 유용한 두 가지 사용 사례가 있습니다.

- Service Bus는 지정된 범위의 IP 주소에서 오는 트래픽만 수신하고 이외의 트래픽은 거부해야 합니다. 예를 들어 [Azure ExpressRoute][express-route]에서 Service Bus를 사용하여 온-프레미스 인프라에 개인 연결을 만듭니다.
- Service Bus 관리자에 의해 의심스러운 것으로 식별된 IP 주소의 트래픽을 거부해야 합니다.

## <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 Service Bus 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다.

Service Bus 네임스페이스에서 거부된 IP 규칙과 일치하는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다.

## <a name="default-setting"></a>기본 설정

기본적으로 Service Bus에 대한 포털의 **IP 필터** 그리드는 비어있습니다. 이러한 기본 설정은 네임스페이스가 모든 IP 주소의 연결을 수락한다는 것을 의미합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다.

## <a name="ip-filter-rule-evaluation"></a>IP 필터 규칙 평가

IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

예를 들어 70.37.104.0/24 범위의 주소를 수락하고 그 외의 주소는 거부하려는 경우 그리드에 있는 첫 번째 규칙이 주소 범위 70.37.104.0/24를 수락해야 합니다. 다음 규칙은 0.0.0.0/0 범위를 사용하여 모든 주소를 거부해야 합니다.

> [!NOTE]
> IP 주소를 거부하면 다른 Azure 서비스(예: Azure Stream Analytics, Azure Virtual Machines 또는 포털의 Device Explorer)가 Service Bus와 상호 작용하는 것을 막을 수 있습니다.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 가상 네트워크 규칙 만들기

> ![중요]Virtual Network는 **프리미엄 계층** Service Bus에서만 지원됩니다.

다음과 같은 Resource Manager 템플릿을사용 하면 기존 Service Bus 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

- **ipFilterRuleName**은 최대 128자 길이의 대/소문자를 구분하는 고유한 영숫자 문자열이어야 합니다.
- **ipFilterAction**은 IP 필터 규칙에 적용할 작업으로 **거부** 또는 **허용**입니다.
- **ipMask**는 단일 IPv4 주소 또는 CIDR 표기법인 IP 주소 블록입니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Service Bus에 대한 액세스를 Azure 가상 네트워크로 제한하려면 다음 링크를 참조하세요.

- [Service Bus의 Virtual Network 서비스 엔드포인트][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services