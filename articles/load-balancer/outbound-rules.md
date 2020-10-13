---
title: 아웃 바운드 규칙 Azure Load Balancer
description: 이 문서에서는 Azure Load Balancer를 사용 하 여 인터넷 트래픽 송신을 제어 하는 아웃 바운드 규칙을 구성 하는 방법을 설명 합니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002638"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>아웃 바운드 규칙 Azure Load Balancer

아웃 바운드 규칙을 사용 하 여 공용 표준 부하 분산 장치 아웃 바운드 SNAT (원본 네트워크 주소 변환)을 구성할 수 있습니다. 이 구성을 사용 하면 부하 분산 장치의 공용 IP를 프록시로 사용할 수 있습니다.

이 구성을 사용 하면 다음을 수행할 수 있습니다.

* IP 위장
* 허용 목록 단순화.
* 배포에 대 한 공용 IP 리소스의 수를 줄입니다.

아웃 바운드 규칙을 사용 하 여 아웃 바운드 인터넷 연결을 완벽 하 게 제어할 수 있습니다. 아웃 바운드 규칙을 사용 하면 특정 요구에 맞게이 기능을 확장 하 고 조정할 수 있습니다. 

아웃 바운드 규칙은 백 엔드 VM에 인스턴스 수준 공용 IP 주소 (ILPIP)가 없는 경우에만 수행 됩니다.

![Load Balancer 아웃바운드 규칙](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

아웃 바운드 규칙을 사용 하 여 아웃 바운드 **SNAT** 동작을 명시적으로 정의할 수 있습니다.

아웃바운드 규칙을 통해 제어할 수 있는 항목은 다음과 같습니다.

* **공용 IP 주소로 변환 되는 가상 컴퓨터**
     * 두 규칙은 백 엔드 풀 A는 IP 주소 A와 B를 사용 하 고 백 엔드 풀 B는 IP 주소 C 및 D를 사용 합니다.
* **아웃 바운드 SNAT 포트를 지정 하는 방법입니다.**
     * 백 엔드 풀 B는 아웃 바운드 연결을 만드는 유일한 풀 이며 백 엔드 풀 B에 모든 SNAT 포트를 제공 하 고 백 엔드 풀 A에는 none을 제공 합니다.
* **아웃 바운드 변환을 제공할 프로토콜입니다.**
     * 백 엔드 풀 B에는 아웃 바운드를 위한 UDP 포트가 필요 합니다. 백 엔드 풀 A에는 TCP가 필요 합니다. A 및 UDP 포트에 TCP 포트를 B에 게 제공 합니다.
* **아웃 바운드 연결 유휴 시간 제한 (4-120 분)에 사용할 기간입니다.**
     * Keepalives를 사용 하는 장기 실행 연결이 있는 경우 최대 120 분 동안 장기 실행 연결에 대 한 유휴 포트를 예약 합니다. 새 연결에 대해 오래 된 연결이 중단 되 고 4 분 후에 포트를 해제 한다고 가정 합니다. 
* **유휴 시간 제한 시 TCP 다시 설정을 보낼지 여부입니다.**
     * 유휴 연결의 시간을 초과 하는 경우 흐름이 중단 된 것을 알 수 있도록 TCP RST을 클라이언트 및 서버에 보냅니다.

## <a name="outbound-rule-definition"></a>아웃 바운드 규칙 정의

아웃 바운드 규칙은 부하 분산 및 인바운드 NAT 규칙과 동일한 익숙한 구문을 따릅니다. **프런트 엔드**  +  **매개 변수**  +  **백엔드 풀**. 

아웃바운드 규칙은 _백 엔드 풀에서 식별된 모든 가상 머신_에 대한 아웃바운드 NAT를 _프런트 엔드_로 변환하도록 구성합니다.  

_매개 변수_ 는 아웃 바운드 NAT 알고리즘에 대 한 추가 세분화 된 제어를 제공 합니다.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 여러 IP 주소를 사용하여 아웃바운드 NAT 확장

프런트 엔드에서 제공 되는 각 추가 IP 주소는 부하 분산 장치에서 SNAT 포트로 사용할 추가 64000 삭제 포트를 제공 합니다. 

여러 IP 주소를 사용 하 여 대규모 시나리오에 대 한 계획을 세워야 합니다. 아웃 바운드 규칙을 사용 하 여 [SNAT 소모](troubleshoot-outbound-connection.md#snatexhaust)를 완화 합니다. 

아웃 바운드 규칙을 사용 하 여 직접 [공용 IP 접두사](https://aka.ms/lbpublicipprefix) 를 사용할 수도 있습니다. 

공용 IP 접두사는 배포의 크기를 늘립니다. Azure 리소스에서 시작 하는 흐름의 허용 목록에 접두사를 추가할 수 있습니다. 공용 IP 주소 접두사를 참조 하도록 부하 분산 장치 내에서 프런트 엔드 IP 구성을 구성할 수 있습니다.  

부하 분산 장치는 공용 IP 접두사를 제어 합니다. 아웃 바운드 규칙은 아웃 바운드 연결에 대 한 공용 IP 접두사 내에 포함 된 모든 공용 IP 주소를 자동으로 사용 합니다. 

공용 IP 접두사 내의 각 IP 주소는 부하 분산 장치에서 SNAT 포트로 사용할 IP 주소 당 추가 64000 삭제 포트를 제공 합니다.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 아웃 바운드 흐름 유휴 시간 제한 및 TCP 다시 설정

아웃바운드 규칙은 아웃바운드 흐름 유휴 시간 제한을 제어하고 이를 애플리케이션의 요구와 일치시키는 구성 매개 변수를 제공합니다. 아웃바운드 유휴 시간 제한은 기본적으로 4분입니다. 자세한 내용은 [유휴 시간 제한 구성](load-balancer-tcp-idle-timeout.md)을 참조 하세요. 

부하 분산 장치의 기본 동작은 아웃 바운드 유휴 시간 제한에 도달 했을 때 흐름을 자동으로 삭제 하는 것입니다. `enableTCPReset`매개 변수를 사용 하면 예측 가능한 응용 프로그램 동작 및 컨트롤을 사용할 수 있습니다. 매개 변수는 아웃 바운드 유휴 시간 제한의 제한 시간에 양방향 TCP 다시 설정 (TCP RST)을 보낼지 여부를 결정 합니다. 

지역 가용성을 비롯 한 세부 정보는 [유휴 시간 제한에서 TCP 다시 설정](https://aka.ms/lbtcpreset) 을 검토 합니다.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>아웃 바운드 연결을 명시적으로 보안 및 제어

부하 분산 규칙은 아웃 바운드 NAT의 자동 프로그래밍 기능을 제공 합니다. 일부 시나리오에서는 부하 분산 규칙에 따라 아웃 바운드 NAT의 자동 프로그래밍을 사용 하지 않도록 설정 해야 합니다. 규칙을 통해 사용 하지 않도록 설정 하면 동작을 제어 하거나 구체화할 수 있습니다.  

이 매개 변수는 다음 두 가지 방법으로 사용할 수 있습니다.

1. 아웃 바운드 SNAT에 대 한 인바운드 IP 주소를 방지 합니다. 부하 분산 규칙에서 아웃 바운드 SNAT를 사용 하지 않도록 설정 합니다.
  
2. 인바운드 및 아웃 바운드에 대해 동시에 사용 되는 IP 주소의 아웃 바운드 **SNAT** 매개 변수를 조정 합니다. 아웃 바운드 규칙을 제어할 수 있도록 자동 아웃 바운드 NAT를 사용 하지 않도록 설정 해야 합니다. 인바운드에도 사용 되는 주소의 SNAT 포트 할당을 변경 하려면 `disableOutboundSnat` 매개 변수를 true로 설정 해야 합니다. 

인바운드에 사용 되는 IP 주소를 다시 정의 하려고 하면 아웃 바운드 규칙을 구성 하는 작업이 실패 합니다.  먼저 부하 분산 규칙의 아웃 바운드 NAT를 사용 하지 않도록 설정 합니다.

>[!IMPORTANT]
> 이 매개 변수를 true로 설정 하 고 아웃 바운드 연결을 정의 하는 아웃 바운드 규칙을 포함 하지 않는 경우에는 가상 컴퓨터에서 아웃 바운드 연결이 설정 되지 않습니다.  VM 또는 애플리케이션의 일부 작업은 아웃바운드 연결을 사용할 수 있는지에 따라 달라질 수 있습니다. 시나리오의 종속성을 이해하고 이러한 변경의 영향을 고려해야 합니다.

경우에 따라 VM이 아웃 바운드 흐름을 만드는 것이 바람직하지 않습니다. 아웃 바운드 흐름을 수신 하는 대상 또는 인바운드 흐름을 시작 하는 대상을 관리 하기 위한 요구 사항이 있을 수 있습니다. [네트워크 보안 그룹](../virtual-network/security-overview.md) 을 사용 하 여 VM이 도달할 수 있는 대상을 관리 합니다. NSGs를 사용 하 여 인바운드 흐름을 시작 하는 공용 대상을 관리 합니다.

부하 분산 VM에 NSG를 적용할 때 [서비스 태그](../virtual-network/security-overview.md#service-tags) 및 [기본 보안 규칙](../virtual-network/security-overview.md#default-security-rules)에 주의해야 합니다. 

VM이 Azure Load Balancer에서 상태 프로브 요청을 받을 수 있는지 확인 합니다.

NSG가 AZURE_LOADBALANCER 기본 태그의 상태 프로브 요청을 차단 하는 경우 VM 상태 프로브가 실패 하 고 VM을 사용할 수 없는 것으로 표시 합니다. 부하 분산 장치는 해당 VM에 대 한 새 흐름 전송을 중지 합니다.

## <a name="limitations"></a>제한 사항

- 프런트 엔드 IP 주소당 사용 가능한 사용 후 삭제 포트의 최대 수는 64,000개입니다.
- 구성 가능한 아웃바운드 유휴 시간 제한 범위는 4-120분(240-7200초)입니다.
- 부하 분산 장치는 아웃 바운드 NAT에 대 한 ICMP를 지원 하지 않습니다.
- 아웃 바운드 규칙은 NIC의 기본 IP 구성에만 적용할 수 있습니다.  VM 또는 NVA의 보조 IP에 대 한 아웃 바운드 규칙을 만들 수 없습니다. 여러 Nic가 지원 됩니다.
- **가용성 집합** 내의 모든 가상 컴퓨터는 아웃 바운드 연결에 대 한 백 엔드 풀에 추가 되어야 합니다. 
- 아웃 바운드 연결에 대 한 **가상 머신 확장 집합** 내의 모든 가상 머신을 백 엔드 풀에 추가 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 표준 Load Balancer](load-balancer-overview.md) 에 대 한 자세한 정보
- [Azure Load Balancer에 대 한 질문과 대답](load-balancer-faqs.md) 을 참조 하세요.

