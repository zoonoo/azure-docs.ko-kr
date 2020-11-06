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
ms.openlocfilehash: 947ecaa2efbfb013f1f3e8203d1c4296b9ca329f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422164"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>아웃 바운드 규칙 Azure Load Balancer

아웃 바운드 규칙을 사용 하 여 공용 표준 부하 분산 장치에 대 한 SNAT (원본 네트워크 주소 변환)을 명시적으로 정의할 수 있습니다. 이 구성을 사용 하면 부하 분산 장치의 공용 IP를 사용 하 여 백 엔드 인스턴스에 대 한 아웃 바운드 인터넷 연결을 제공할 수 있습니다.

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
* **아웃 바운드 SNAT 포트를 할당 하는 방법입니다.**
     * 백 엔드 풀 B는 아웃 바운드 연결을 만드는 유일한 풀 이며 백 엔드 풀 B에 모든 SNAT 포트를 제공 하 고 백 엔드 풀 A에는 none을 제공 합니다.
* **아웃 바운드 변환을 제공할 프로토콜입니다.**
     * 백 엔드 풀 B에는 아웃 바운드를 위한 UDP 포트가 필요 합니다. 백 엔드 풀 A에는 TCP가 필요 합니다. A 및 UDP 포트에 TCP 포트를 B에 게 제공 합니다.
* **아웃 바운드 연결 유휴 시간 제한 (4-120 분)에 사용할 기간입니다.**
     * Keepalives를 사용 하는 장기 실행 연결이 있는 경우 최대 120 분 동안 장기 실행 연결에 대 한 유휴 포트를 예약 합니다. 새 연결에 대해 오래 된 연결이 중단 되 고 4 분 후에 포트를 해제 한다고 가정 합니다. 
* **유휴 시간 제한 시 TCP 다시 설정을 보낼지 여부입니다.**
     * 유휴 연결의 시간을 초과 하는 경우 흐름이 중단 된 것을 알 수 있도록 TCP RST을 클라이언트 및 서버에 보냅니다.

## <a name="outbound-rule-definition"></a>아웃 바운드 규칙 정의

아웃 바운드 규칙은 부하 분산 및 인바운드 NAT 규칙과 동일한 익숙한 구문을 따릅니다. **프런트 엔드**  +  **매개 변수**  +  **백엔드 풀**. 

아웃바운드 규칙은 _백 엔드 풀에서 식별된 모든 가상 머신_ 에 대한 아웃바운드 NAT를 _프런트 엔드_ 로 변환하도록 구성합니다.  

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

## <a name="scenarios-with-outbound-rules"></a>아웃 바운드 규칙을 사용 하는 시나리오
        

### <a name="outbound-rules-scenarios"></a>아웃 바운드 규칙 시나리오


* 공용 Ip 또는 접두사의 특정 집합에 대 한 아웃 바운드 연결을 구성 합니다.
* [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트 할당을 수정 합니다.
* 아웃 바운드만 사용 합니다.
* Vm에 대 한 아웃 바운드 NAT만 (인바운드 없음)
* 내부 표준 부하 분산 장치에 대 한 아웃 바운드 NAT.
* 공용 표준 부하 분산 장치를 사용 하는 아웃 바운드 NAT에 TCP & UDP 프로토콜을 모두 사용 하도록 설정 합니다.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>시나리오 1: 공용 Ip 또는 접두사의 특정 집합에 대 한 아웃 바운드 연결 구성


#### <a name="details"></a>세부 정보


이 시나리오를 사용 하 여 공용 IP 주소 집합에서 시작 되는 아웃 바운드 연결을 조정할 수 있습니다. 발생에 따라 허용 또는 거부 목록에 공용 Ip 또는 접두사를 추가 합니다.


이 공용 IP 또는 접두사는 부하 분산 규칙에서 사용 하는 것과 동일할 수 있습니다. 


부하 분산 규칙에서 사용 하는 것과 다른 공용 IP 또는 접두사를 사용 하려면 다음을 수행 합니다. 


1. 공용 IP 접두사 또는 공용 IP 주소를 만듭니다.
2. 공용 표준 부하 분산 장치 만들기 
3. 사용할 공용 IP 접두사 또는 공용 IP 주소를 참조 하는 프런트 엔드를 만듭니다. 
4. 백 엔드 풀을 다시 사용 하거나 백 엔드 풀을 만들고 Vm을 공용 부하 분산 장치의 백 엔드 풀에 추가 합니다.
5. 프런트 엔드를 사용 하 여 Vm에 대해 아웃 바운드 NAT를 사용 하도록 공용 부하 분산 장치에서 아웃 바운드 규칙을 구성 합니다. 아웃 바운드에 대해 부하 분산 규칙을 사용 하는 것은 권장 되지 않으며, 부하 분산 규칙에서 아웃 바운드 SNAT를 사용 하지 않도록 설정 합니다.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>시나리오 2: [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트 할당 수정


#### <a name="details"></a>세부 정보


아웃바운드 규칙을 사용하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)을 튜닝할 수 있습니다. 


SNAT 고갈를 발생 하는 경우 기본값 1024에서 제공 되는 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트 수를 늘립니다. 


각 공용 IP 주소는 최대 64000 개의 임시 포트를 제공 합니다. 백 엔드 풀의 Vm 수는 각 VM에 배포 되는 포트 수를 결정 합니다. 백 엔드 풀의 한 VM은 최대 64000 포트에 액세스할 수 있습니다. 두 Vm의 경우 아웃 바운드 규칙 (2x 32000 = 64000)을 사용 하 여 최대 32000 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트를 지정할 수 있습니다. 


아웃 바운드 규칙을 사용 하 여 기본적으로 제공 되는 SNAT 포트를 튜닝할 수 있습니다. 기본 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트 할당에 제공 되는 것 보다 더 많은 또는 더 작은 값을 제공 합니다. 아웃 바운드 규칙의 프런트 엔드에 있는 각 공용 IP 주소는 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트로 사용 하기 위해 최대 64000 개의 임시 포트를 제공 합니다. 


부하 분산 장치는 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트를 8의 배수로 제공 합니다. 8로 나눌 수 없는 값을 제공하면 구성 작업이 거부됩니다. 각 부하 분산 규칙 및 인바운드 NAT 규칙은 8 개 포트의 범위를 사용 합니다. 부하 분산 또는 인바운드 NAT 규칙이 다른 범위와 8을 공유 하는 경우 추가 포트가 사용 되지 않습니다.


공용 IP 주소의 수를 기준으로 사용할 수 있는 것 보다 더 많은 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트를 제공 하려고 하면 구성 작업이 거부 됩니다. 예를 들어 VM 당 1만 포트를 제공 하 고 백 엔드 풀에서 7 개의 Vm이 단일 공용 IP를 공유 하는 경우 구성이 거부 됩니다. 7과 1만를 곱하여 64000 포트 제한을 초과 합니다. 아웃 바운드 규칙의 프런트 엔드에 공용 IP 주소를 더 추가 하 여 시나리오를 사용 하도록 설정 합니다. 


포트 수에 0을 지정 하 여 [기본 포트 할당](load-balancer-outbound-connections.md#preallocatedports) 으로 되돌립니다. 첫 번째 50 VM 인스턴스는 1024 포트를 가져오며, 51-100 VM 인스턴스는 최대 인스턴스 수에 대 한 512을 받습니다. 기본 SNAT 포트 할당에 대 한 자세한 내용은 [snat 포트 할당 표](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)를 참조 하세요.


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>시나리오 3: 아웃 바운드만 사용


#### <a name="details"></a>세부 정보


공용 표준 부하 분산 장치를 사용 하 여 Vm 그룹에 아웃 바운드 NAT를 제공 합니다. 이 시나리오에서는 추가 규칙을 구성 하지 않고 아웃 바운드 규칙을 단독으로 사용 합니다.


> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 부하 분산 장치 없이도 가상 컴퓨터에 대 한 아웃 바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>시나리오 4: Vm에 대 한 아웃 바운드 NAT만 (인바운드 없음)


> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 부하 분산 장치 없이도 가상 컴퓨터에 대 한 아웃 바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

#### <a name="details"></a>세부 정보


이 시나리오의 경우 Azure Load Balancer 아웃 바운드 규칙 및 Virtual Network NAT는 가상 네트워크에서 송신에 사용할 수 있는 옵션입니다.


1. 공용 IP 또는 접두사를 만듭니다.
2. 공용 표준 부하 분산 장치를 만듭니다. 
3. 아웃 바운드에 전용으로 사용 되는 공용 IP 또는 접두사와 연결 된 프런트 엔드를 만듭니다.
4. Vm에 대 한 백 엔드 풀을 만듭니다.
5. Vm을 백 엔드 풀에 저장 합니다.
6. 아웃 바운드 NAT를 사용 하도록 아웃 바운드 규칙을 구성 합니다.



접두사나 공용 IP를 사용 하 여 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 포트의 크기를 조정 합니다. 허용 또는 거부 목록에 아웃 바운드 연결의 원본을 추가 합니다.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>시나리오 5: 내부 표준 부하 분산 장치에 대 한 아웃 바운드 NAT


> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 내부 표준 부하 분산 장치를 활용 하는 가상 머신에 대 한 아웃 바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

#### <a name="details"></a>세부 정보


내부 표준 부하 분산 장치는 인스턴스 수준 공용 Ip 또는 Virtual Network NAT를 통해 명시적으로 선언 될 때까지 또는 백 엔드 풀 구성원을 아웃 바운드 전용 부하 분산 장치 구성과 연결 하 여 내부 표준 부하 분산 장치에 대 한 아웃 바운드 연결을 사용할 수 없습니다. 


자세한 내용은 [아웃 바운드 전용 부하 분산 장치 구성](https://docs.microsoft.com/azure/load-balancer/egress-only)을 참조 하세요.




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>시나리오 6: 공용 표준 부하 분산 장치를 사용 하는 아웃 바운드 NAT에 TCP & UDP 프로토콜 모두 사용


#### <a name="details"></a>세부 정보


공용 표준 부하 분산 장치를 사용 하는 경우 제공 된 자동 아웃 바운드 NAT가 부하 분산 규칙의 전송 프로토콜과 일치 합니다. 


1. 부하 분산 규칙에서 아웃 바운드 [SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) 를 사용 하지 않도록 설정 합니다. 
2. 동일한 부하 분산 장치에서 아웃 바운드 규칙을 구성 합니다.
3. VM에서 이미 사용하고 있는 백 엔드 풀을 다시 사용합니다. 
4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다. 


인바운드 NAT 규칙만 사용하는 경우 아웃바운드 NAT는 제공되지 않습니다. 


1. 백 엔드 풀에 VM을 배치합니다.
2. 공용 ip 주소 또는 공용 IP 접두사를 사용 하 여 하나 이상의 프런트 엔드 IP 구성을 정의 합니다. 
3. 동일한 부하 분산 장치에서 아웃 바운드 규칙을 구성 합니다. 
4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다.


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

