---
title: Azure 방화벽 위협 인텔리전스 구성
description: 방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 위협 인텔리전스 기반 필터링을 사용하도록 설정할 수 있습니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 136ceeb271bec29bdbfc4572626936ee67f05556
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568131"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure 방화벽 위협 인텔리전스 구성

Azure 방화벽 정책에 대 한 위협 인텔리전스 기반 필터링을 구성 하 여 알려진 악성 IP 주소 및 도메인의 트래픽을 경고 및 거부 하도록 구성할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.<br>

위협 인텔리전스 기반 필터링을 구성한 경우 연결 된 규칙은 NAT 규칙, 네트워크 규칙 또는 응용 프로그램 규칙 보다 먼저 처리 됩니다.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="위협 인텔리전스 정책":::

## <a name="threat-intelligence-mode"></a>위협 인텔리전스 모드

규칙이 트리거될 때 경고만 기록 하도록 선택 하거나 경고 및 거부 모드를 선택할 수 있습니다.

기본적으로 위협 인텔리전스 기반 필터링은 경고 모드에서 사용하도록 설정됩니다.

## <a name="allowed-list-addresses"></a>허용 되는 목록 주소

위협 인텔리전스가 지정 된 주소, 범위 또는 서브넷을 필터링 하지 않도록 허용 된 IP 주소 목록을 구성할 수 있습니다.



## <a name="logs"></a>로그

다음 로그 발췌문에서는 트리거된 규칙을 보여줍니다.

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>테스트

- **아웃바운드 테스트** - 환경이 손상되었음을 의미하는 아웃바운드 트래픽 경고는 드물게 발생합니다. 아웃바운드 경고가 작동하는지 테스트하는 데 도움이 되도록 경고를 트리거하는 테스트 FQDN이 생성되었습니다. 아웃바운드 테스트에 **testmaliciousdomain.eastus.cloudapp.azure.com**을 사용합니다.

- **인바운드 테스트** - DNAT 규칙이 방화벽에 구성된 경우 들어오는 트래픽에 대한 경고를 볼 수 있습니다. DNAT 규칙에서 특정 원본만 허용되고 트래픽이 거부되는 경우에도 마찬가지입니다. Azure Firewall은 알려진 모든 포트 스캐너에 대해 경고하지 않습니다. 악의적인 작업에도 참여하는 것으로 알려진 스캐너에만 경고합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Security 인텔리전스 보고서](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) 검토