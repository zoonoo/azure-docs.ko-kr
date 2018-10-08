---
title: 유휴 시 Load Balancer TCP 재설정 | Microsoft Docs
description: 유휴 시간 초과 시 양방향 TCP RST 패킷이 포함된 Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: kumud
ms.openlocfilehash: 0b533a48e94db880f23d42decc5c3fb39a27e5ac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395647"
---
# <a name="load-balancer-with-tcp-reset-on-idle-public-preview"></a>유휴 시 TCP 재설정이 포함된 Load Balancer(공개 미리 보기)

[표준 Load Balancer](load-balancer-standard-overview.md)를 사용하면 구성 가능한 각 유휴 시간 초과에 대한 양방향 TCP 재설정(TCP RST 패킷)을 포함하여 시나리오의 응용 프로그램 동작을 보다 예측 가능하게 만들 수 있습니다.  Load Balancer의 기본 동작은 흐름의 유휴 시간이 초과되면 흐름을 자동으로 끊는 것입니다.

![Load Balancer TCP 재설정](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)

>[!NOTE] 
>유휴 시간 초과 시 TCP 재설정 기능이 포함된 Load Balancer는 현재 공개 미리 보기로, 제한된 [지역](#regions)에서 사용할 수 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
이 기본 동작을 변경하고 인바운드 NAT 규칙, 부하 분산 규칙 및 [아웃바운드 규칙](https://aka.ms/lboutboundrules)에서 유휴 시간 초과 시 TCP 재설정 보내기를 사용하도록 설정합니다.  규칙에서 사용하도록 설정하면 Load Balancer는 모든 일치 흐름의 유휴 시간 초과 시 클라이언트 및 서버 엔드포인트 둘 다에 양방향 TCP 재설정(TCP RST 패킷)을 보냅니다.

TCP RST 패킷을 수신하는 엔드포인트는 즉시 해당 소켓을 닫습니다. 그러면 연결 해제가 발생했으며 동일한 TCP 연결의 이후 통신은 실패할 것이라는 알림이 곧바로 엔드포인트에 제공됩니다.  응용 프로그램은 소켓이 닫히면 연결을 제거할 수 있으며 필요에 따라 TCP 연결 시간이 초과될 때까지 기다리지 않고 연결을 다시 설정할 수 있습니다.

이렇게 하면 많은 시나리오에서 흐름의 유휴 시간 초과를 새로 고치기 위해 TCP(또는 응용 프로그램 계층) 연결 유지를 보내는 것을 줄일 수 있습니다. 

유휴 기간이 구성에서 허용하는 기간을 초과하거나 TCP 재설정을 사용하도록 설정하면 응용 프로그램이 원치 않는 동작을 보여 주는 경우 계속 TCP 연결 유지(또는 응용 프로그램 계층 연결 유지)를 사용하여 TCP 연결의 활동성을 모니터링해야 할 수도 있습니다.  또한 연결이 경로의 특정 위치, 특히 응용 프로그램 계층 연결 유지에 프록시 설정된 경우에도 연결 유지가 유용할 수 있습니다.  

종단 간 시나리오 전체를 신중하게 검토하여 TCP 재설정을 사용하도록 설정하고 유휴 시간 초과를 조정하는 것이 유리한지, 응용 프로그램이 원하는 대로 동작하게 하려면 추가 조치가 필요한지 여부를 결정합니다.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>유휴 시간 초과 시 TCP 재설정 사용

API 버전 2018-07-01을 사용하면 개별 규칙을 기반으로 유휴 시간 초과 시 양방향 TCP 재설정 보내기를 사용하도록 설정할 수 있습니다.

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="regions"></a> 지역 가용성

이 매개 변수는 현재 다음 지역에서 적용됩니다.  여기에 나열되지 않은 지역에서는 매개 변수가 적용되지 않습니다.

| 지역 |
|---|
| 동남아시아 |
| 서유럽 |
| 미국 동부 2 |
| 영국 북부 |
| 미국 서부 |

미리 보기가 다른 지역으로 확장되면 이 테이블도 업데이트됩니다.  

## <a name="limitations"></a>제한 사항

- [지역 가용성](#regions)이 제한되어 있습니다.
- 포털은 TCP 재설정을 구성하거나 보는 데 사용할 수 없습니다.  대신 템플릿, REST API, Az CLI 2.0 또는 PowerShell을 사용하세요.

## <a name="next-steps"></a>다음 단계

- [표준 Load Balancer](load-balancer-standard-overview.md)에 대해 알아봅니다.
- [아웃바운드 규칙](load-balancer-outbound-rules-overview.md)에 대해 알아봅니다.
