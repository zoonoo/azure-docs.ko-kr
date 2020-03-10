---
title: Azure Virtual Network NAT 연결 문제 해결
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Virtual Network NAT와 관련된 문제를 해결합니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: d56cd3b3d286d69a51d8cc14eb8020343cf7295a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302987"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Azure Virtual Network NAT 연결 문제 해결

이 문서는 Virtual Network NAT를 사용하는 경우 관리자가 연결 문제를 진단하고 해결하는 데 도움이 됩니다.

>[!NOTE] 
>Virtual Network NAT는 현재 공개 미리 보기로 제공됩니다. 현재 제한된 [지역](nat-overview.md#region-availability) 세트에서만 사용할 수 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms)을 참조하세요.

## <a name="problems"></a>문제

- [SNAT 소모](#snat-exhaustion)
- [ICMP ping이 실패함](#icmp-ping-is-failing)

이러한 문제를 해결하려면 다음 섹션의 단계를 따르세요.

## <a name="resolution"></a>해결 방법

### <a name="snat-exhaustion"></a>SNAT 소모

단일 [NAT 게이트웨이 리소스](nat-gateway-resource.md)는 64,000~1,000,000개의 동시 흐름을 지원합니다.  각 IP 주소는 64,000개의 SNAT 포트를 사용 가능한 인벤토리에 제공합니다. NAT 게이트웨이 리소스당 최대 16개의 IP 주소를 사용할 수 있습니다.  SNAT 메커니즘은 [여기](nat-gateway-resource.md#source-network-address-translation)서 자세히 설명하고 있습니다.

#### <a name="steps"></a>단계:

1. 애플리케이션에서 아웃바운드 연결을 만드는 방법(예: 코드 검토 또는 패킷 캡처)을 조사합니다. 
2. 이 활동이 예상되는 동작인지, 아니면 애플리케이션이 잘못 작동하는지를 확인합니다.  Azure Monitor에서 [메트릭](nat-metrics.md)을 사용하여 검색 결과를 구체화합니다.
3. 적절한 패턴을 따르는지 여부를 평가합니다.
4. NAT 게이트웨이 리소스에 할당된 추가 IP 주소를 사용하여 SNAT 포트 소모가 완화되는지 여부를 평가합니다.

#### <a name="design-pattern"></a>디자인 패턴:

가능한 경우 항상 연결 다시 사용 및 연결 풀링을 활용합니다.  이 패턴은 리소스 소모 문제를 완전히 방지하고 예측 가능한 동작이 수행됩니다. 이러한 패턴의 기본 형식은 다양한 개발 라이브러리 및 프레임워크에서 찾을 수 있습니다.
- [비동기 폴링 패턴](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply)을 장기 실행 작업에 사용하여 다른 작업에 대한 연결 리소스를 확보하는 것이 좋습니다.
- 중간 시스템의 시간이 초과되지 않도록 방지하기 위해 수명이 긴 흐름(예: 다시 사용되는 TCP 연결)에서 TCP keepalive 또는 애플리케이션 계층 keepalive를 사용해야 합니다.
- 일시적 실패 또는 오류 복구 중에 공격적인 다시 시도/버스트를 방지하기 위해 정상적인 [다시 시도 패턴](https://docs.microsoft.com/azure/architecture/patterns/retry)을 사용해야 합니다.
모든 HTTP 작업에 대한 새 TCP 연결("원자성 연결"이라고도 함)을 만드는 것은 안티패턴입니다.  원자성 연결은 애플리케이션의 크기가 효율적으로 조정되지 않고 리소스가 낭비되지 않도록 합니다.  항상 여러 작업을 동일한 연결로 파이프라인합니다.  애플리케이션은 트랜잭션 속도 및 리소스 비용에서 이점을 활용할 수 있습니다.  애플리케이션에서 전송 계층 암호화(예: TLS)를 사용하는 경우 새 연결 처리와 관련된 상당한 비용이 발생합니다.  추가 모범 사례 패턴은 [Azure 클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns/)을 검토하세요.

#### <a name="mitigations"></a>해결 방법

다음과 같이 아웃바운드 연결의 크기를 조정할 수 있습니다.

| 시나리오 | 완화 방법 |
|---|---|
| 사용량이 많은 기간 동안 SNAT 포트 및 SNAT 포트 소모에 대한 경합이 발생하고 있습니다. | 추가 공용 IP 주소 리소스 또는 공용 IP 접두사 리소스를 추가할 수 있는지 확인합니다. 이렇게 추가하면 최대 16개의 IP 주소를 NAT 게이트웨이에 사용할 수 있습니다. 이 추가 기능을 통해 사용 가능한 SNAT 포트에 대한 더 많은 인벤토리(IP 주소당 64,000개)가 제공되며 시나리오를 더 확장할 수 있습니다.|
| 16개의 IP 주소를 이미 제공했지만 여전히 SNAT 포트 소모가 발생하고 있습니다. | 애플리케이션 환경을 여러 서브넷에 분산하고, 각 서브넷에 대한 NAT 게이트웨이 리소스를 제공합니다. |

>[!NOTE]
>SNAT 소모가 발생하는 이유를 이해하는 것이 중요합니다. 확장 가능하고 안정적인 시나리오에 적합한 패턴을 사용하고 있는지 확인하세요.  요구의 원인을 이해하지 않고 SNAT 포트를 시나리오에 추가하는 것은 최후의 수단이어야 합니다. 시나리오에서 SNAT 포트 인벤토리를 압박하는 이유를 이해할 수 없는 경우 더 많은 IP 주소를 추가하여 SNAT 포트를 인벤토리에 추가하면 애플리케이션 크기 조정과 동일한 소진 오류가 지연될 뿐입니다.  다른 비효율성 및 안티패턴을 마스킹할 수 있습니다.

### <a name="icmp-ping-is-failing"></a>ICMP ping이 실패함

[Virtual Network NAT](nat-overview.md)는 IPv4 UDP 및 TCP 프로토콜을 지원합니다. ICMP는 지원되지 않으며 실패할 것으로 예상됩니다.  대신 TCP 연결 테스트(예: "TCP ping") 및 UDP별 애플리케이션 계층 테스트를 사용하여 엔드투엔드 연결의 유효성을 검사합니다.

다음 표는 테스트를 시작하는 데 사용할 수 있는 도구의 시작 시점으로 사용할 수 있습니다.

| 운영 체제 | 제네릭 TCP 연결 테스트 | TCP 애플리케이션 계층 테스트 | UDP |
|---|---|---|---|
| Linux | nc(제네릭 연결 테스트) | curl(TCP 애플리케이션 계층 테스트) | 애플리케이션별 |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | 애플리케이션별 |

## <a name="next-steps"></a>다음 단계

- [Virtual Network NAT](nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](nat-gateway-resource.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스에 대한 메트릭 및 경고](nat-metrics.md)에 대한 자세한 정보
