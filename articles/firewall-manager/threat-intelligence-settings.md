---
title: Azure 방화벽 위협 인텔리전스 구성
description: Azure 방화벽 정책에 대 한 위협 인텔리전스 기반 필터링을 구성 하 여 알려진 악성 IP 주소 및 도메인의 트래픽을 경고 및 거부 하는 방법에 대해 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100651727"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure 방화벽 위협 인텔리전스 구성

Azure 방화벽 정책에 대 한 위협 인텔리전스 기반 필터링을 구성 하 여 알려진 악성 IP 주소 및 도메인의 트래픽을 경고 및 거부 하도록 구성할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence)는 Microsoft 위협 인텔리전스를 구동하며 Azure Security Center를 비롯한 여러 서비스에서 사용됩니다.<br>

위협 인텔리전스 기반 필터링을 구성한 경우 연결 된 규칙은 NAT 규칙, 네트워크 규칙 또는 응용 프로그램 규칙 보다 먼저 처리 됩니다.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="위협 인텔리전스 정책":::

## <a name="threat-intelligence-mode"></a>위협 인텔리전스 모드

다음 표에서 설명 하는 세 가지 모드 중 하나에서 위협 인텔리전스를 구성할 수 있습니다. 기본적으로 위협 인텔리전스 기반 필터링은 경고 모드에서 사용하도록 설정됩니다.

|Mode |Description  |
|---------|---------|
|`Off`     | 방화벽에 대해 위협 인텔리전스 기능을 사용할 수 없습니다. |
|`Alert only`     | 방화벽을 통과 하는 트래픽이 알려진 악성 IP 주소와 도메인에 대 한 높은 신뢰도 경고를 받게 됩니다. |
|`Alert and deny`     | 트래픽이 차단 되 고, 알려진 악의적인 IP 주소 및 도메인에서 방화벽을 통과 하려고 할 때 트래픽이 감지 되 면 신뢰도 높은 경고가 수신 됩니다. |

> [!NOTE]
> 위협 인텔리전스 모드는 부모 정책에서 자식 정책으로 상속 됩니다. 자식 정책은 부모 정책과 동일 하거나 엄격한 모드로 구성 되어야 합니다.

## <a name="allowlist-addresses"></a>Allowlist 주소

위협 인텔리전스는 가양성을 트리거하고 실제로 유효한 트래픽을 차단할 수 있습니다. 위협 인텔리전스가 지정 된 주소, 범위 또는 서브넷을 필터링 하지 않도록 허용 된 IP 주소 목록을 구성할 수 있습니다.  

![Allowlist 주소](media/threat-intelligence-settings/allow-list.png)

CSV 파일을 업로드 하 여 한 번에 여러 항목으로 allowlist를 업데이트할 수 있습니다. CSV 파일에는 IP 주소 및 범위만 포함할 수 있습니다. 이 파일은 제목을 포함할 수 없습니다.

> [!NOTE]
> 위협 인텔리전스 allowlist 주소는 부모 정책에서 자식 정책으로 상속 됩니다. 부모 정책에 추가 되는 모든 IP 주소 또는 범위는 모든 자식 정책에도 적용 됩니다.

## <a name="logs"></a>로그

다음 로그 발췌는 악의적인 사이트로의 아웃 바운드 트래픽에 대 한 트리거된 규칙을 보여 줍니다.

```json
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

- **아웃바운드 테스트** - 환경이 손상되었음을 의미하는 아웃바운드 트래픽 경고는 드물게 발생합니다. 아웃바운드 경고가 작동하는지 테스트하는 데 도움이 되도록 경고를 트리거하는 테스트 FQDN이 생성되었습니다. 아웃바운드 테스트에 **testmaliciousdomain.eastus.cloudapp.azure.com** 을 사용합니다.

- **인바운드 테스트** - DNAT 규칙이 방화벽에 구성된 경우 들어오는 트래픽에 대한 경고를 볼 수 있습니다. DNAT 규칙에서 특정 원본만 허용되고 트래픽이 거부되는 경우에도 마찬가지입니다. Azure Firewall은 알려진 모든 포트 스캐너에 대해 경고하지 않습니다. 악의적인 작업에도 참여하는 것으로 알려진 스캐너에만 경고합니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Security 인텔리전스 보고서](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) 검토
