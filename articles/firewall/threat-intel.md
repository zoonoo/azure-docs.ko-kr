---
title: Azure 방화벽 위협 인텔리전스 기반 필터링
description: 방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 위협 인텔리전스 기반 필터링을 사용하도록 설정할 수 있습니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168664"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure 방화벽 위협 인텔리전스 기반 필터링

방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 위협 인텔리전스 기반 필터링을 사용하도록 설정할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다. [지능형 보안 그래프는](https://www.microsoft.com/en-us/security/operations/intelligence) Microsoft 위협 인텔리전스를 지원하며 Azure 보안 센터를 비롯한 여러 서비스에서 사용됩니다.

![방화벽 위협 인텔리전스](media/threat-intel/firewall-threat.png)

위협 인텔리전스 기반 필터링을 사용하도록 설정한 경우 NAT 규칙, 네트워크 규칙 또는 응용 프로그램 규칙 보다 앞서 연결된 규칙이 처리됩니다.

규칙이 트리거될 때 경고를 기록하거나 경고 및 거부 모드를 선택할 수 있습니다.

기본적으로 위협 인텔리전스 기반 필터링은 경고 모드에서 활성화됩니다. 해당 지역에서 포털 인터페이스를 사용할 수 있게 될 때까지 는 이 기능을 끄거나 모드를 변경할 수 없습니다.

![위협 인텔리전스 기반 필터링 포털 인터페이스](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>로그

다음 로그 발췌는 트리거된 규칙을 보여 주었습니다.

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

- **아웃바운드 테스트** - 아웃바운드 트래픽 경고는 환경이 손상되었음을 의미하므로 드물게 발생해야 합니다. 아웃바운드 경고를 테스트하기 위해 경고를 트리거하는 테스트 FQDN이 만들어졌습니다. 아웃바운드 테스트에 **testmaliciousdomain.eastus.cloudapp.azure.com** 사용합니다.

- **인바운드 테스트** - 방화벽에서 DNAT 규칙이 구성된 경우 들어오는 트래픽에 대한 경고가 표시될 수 있습니다. 이는 DNAT 규칙에 특정 소스만 허용되고 트래픽이 달리 거부된 경우에도 마찬가지입니다. Azure 방화벽은 알려진 모든 포트 스캐너에 대해 경고하지 않습니다. 악의적인 활동에도 관여하는 것으로 알려진 스캐너에서만 가능합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 로그 분석 샘플](log-analytics-samples.md) 참조
- [Azure 방화벽 배포 및 구성](tutorial-firewall-deploy-portal.md) 방법 알아보기
- Microsoft [보안 인텔리전스 보고서](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) 검토