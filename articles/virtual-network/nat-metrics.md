---
title: Azure Virtual Network NAT에 대한 메트릭 및 경고
titleSuffix: Azure Virtual Network
description: Virtual Network NAT에 사용할 수 있는 Azure Monitor 메트릭 및 경고를 이해합니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 51713db8532eb33f102de9320dea9eaeb98c4019
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359076"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT 메트릭

Azure Virtual Network NAT 게이트웨이 리소스는 다차원 메트릭을 제공합니다. 이러한 메트릭을 사용하여 작업을 관찰하고 [문제를 해결](nat-metrics.md)할 수 있습니다.  SNAT 소모와 같은 중요한 문제에 대해 경고를 구성할 수 있습니다.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="인터넷으로의 아웃바운드를 위한 Virtual Network NAT">
</p>

*그림: 인터넷으로의 아웃바운드를 위한 Virtual Network NAT*

>[!NOTE] 
>Virtual Network NAT는 현재 공개 미리 보기로 제공됩니다. 현재 제한된 [지역](nat-overview.md#region-availability) 세트에서만 사용할 수 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms)을 참조하세요.

## <a name="metrics"></a>메트릭

NAT 게이트웨이 리소스는 Azure Monitor에서 다음과 같은 다차원 메트릭을 제공합니다.

| 메트릭 | Description | 권장 집계 | 차원 |
|---|---|---|---|
| 바이트 | 인바운드 및 아웃바운드 처리된 바이트 | 합계 | 방향(In; Out), 프로토콜(6 TCP; 17 UDP) |
| 패킷 수 | 인바운드 및 아웃바운드 처리된 패킷 | 합계 | 방향(In; Out), 프로토콜(6 TCP; 17 UDP) |
| 삭제된 패킷 수 | NAT 게이트웨이에서 삭제된 패킷 | 합계 | / |
| SNAT 연결 수 | 간격당 상태 전환 횟수 | 합계 | 연결 상태, 프로토콜(6 TCP; 17 UDP) |
| 총 SNAT 연결 수 | 현재 활성 SNAT 연결(~ SNAT 포트 사용 중) | 합계 | 프로토콜(6 TCP; 17 UDP) |


## <a name="alerts"></a>경고

메트릭에 대한 경고는 위의 각 [메트릭](#metrics)에 대해 Azure Monitor에서 구성할 수 있습니다.

## <a name="limitations"></a>제한 사항

Resource Health는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Virtual Network NAT](nat-overview.md)에 대한 자세한 정보
* [NAT 게이트웨이 리소스](nat-gateway-resource.md)에 대한 자세한 정보
* [Azure Monitor](../azure-monitor/overview.md)에 대한 자세한 정보
* [NAT 게이트웨이 리소스 문제 해결](troubleshoot-nat.md)에 대해 알아봅니다.
* [UserVoice에서 Virtual Network NAT에 대해 다음에 빌드할 항목을 알려주세요](https://aka.ms/natuservoice).


