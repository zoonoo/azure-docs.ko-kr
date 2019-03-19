---
title: Azure 방화벽 위협 인텔리전스 기반 필터링
description: Azure 방화벽 위협 인텔리전스 필터링에 대해 알아봅니다
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730523"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Azure 방화벽 위협 인텔리전스 기반 필터링-공개 미리 보기

경고를 알려진 악성 IP 주소 및 도메인 트래픽을 거부 하 여 방화벽에 대 한 위협 인텔리전스 기반 필터링을 사용할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드가에서 제공 됩니다. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) Microsoft 위협 인텔리전스를 구동 하 고 Azure Security Center를 비롯 한 여러 서비스에서 사용 됩니다.

![방화벽 위협 인텔리전스](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> 위협 인텔리전스 기반 필터링은 현재 공개 미리 보기로 제공 하 고 미리 보기 서비스 수준 계약을 사용 하 여 제공 됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

위협 인텔리전스 기반 필터링을 사용 하는 경우 연결 된 규칙의 NAT 규칙, 네트워크 규칙을 응용 프로그램 규칙 보다 먼저 처리 됩니다. 미리 보기에서 가장 높은 신뢰도 레코드만 포함 됩니다.

규칙이 트리거되면 하거나 경고를 선택 하 고 모드를 거부 될 때만 경고를 기록 하도록 선택할 수 있습니다.

기본적으로 위협 인텔리전스에 따라 필터링 하는 경고 모드로 사용 됩니다. 이 기능을 해제 하 하거나 해당 지역의 포털 인터페이스를 사용할 수 있게 될 때까지 모드를 변경할 수 없습니다.

![위협 인텔리전스 기반 필터링 포털 인터페이스](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>로그

다음 로그 발췌문에 트리거 규칙:

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

- **아웃 바운드 테스트** -환경의 손상 된 것 처럼 아웃 바운드 트래픽 경고는 드물게 발생 해야 합니다. 하는 데 테스트 아웃 바운드 경고도 작업 하는 경고를 트리거하는 테스트 FQDN을 만들었습니다. 사용 하 여 **testmaliciousdomain.eastus.cloudapp.azure.com** 아웃 바운드 테스트 합니다.

- **테스트 인바운드** -DNAT 규칙 방화벽에서 구성 된 경우 들어오는 트래픽을 경고 표시 될 수 있습니다. 특정 원본만 DNAT 규칙에서 허용 되지 않으며 트래픽 그렇지 않으면 거부 됩니다 하는 경우에 마찬가지입니다. Azure 방화벽이 모든 알려진된 포트 스캐너;에 대해 경고 하지 않습니다. 스캐너에만 알려진 악의적인 활동에도 참여.

## <a name="next-steps"></a>다음 단계

- 참조 [Azure 방화벽 Log Analytics 샘플](log-analytics-samples.md)
- 자세한 방법 [배포 하 고 Azure 방화벽 구성](tutorial-firewall-deploy-portal.md)
- 검토를 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)