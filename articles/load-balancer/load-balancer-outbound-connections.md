---
title: 아웃바운드 연결
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure에서 VM이 공용 인터넷 서비스를 사용하여 통신하는 방법을 설명합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2020
ms.author: allensu
ms.openlocfilehash: 79399d0890f61d723f371528408d226f6a192ce4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336499"
---
# <a name="outbound-connections"></a>아웃바운드 연결

Azure Load Balancer는 다른 메커니즘을 통해 아웃 바운드 연결을 제공 합니다. 이 문서에서는 시나리오 및 시나리오를 관리 하는 방법을 설명 합니다. 

## <a name="outbound-connections-scenario-overview"></a><a name="scenarios"></a>아웃 바운드 연결 시나리오 개요

이러한 시나리오에서 사용 되는 용어입니다. 자세한 내용은 [용어](#terms):

* [원본 네트워크 주소 변환 (SNAT)](#snat)
* [포트 가장 (PAT)](#pat)
* TCP (전송 제어 프로토콜)
* UDP (사용자 데이터 그램 프로토콜)
* Network Address Translation
* Internet Control 메시지 프로토콜
* 캡슐화 보안 프로토콜

### <a name="scenarios"></a>시나리오

* [시나리오 1](#scenario1) -공용 IP를 사용 하는 가상 컴퓨터
* [시나리오 2](#scenario2) -공용 IP를 사용 하지 않는 가상 컴퓨터.
* [시나리오 3](#scenario3) -공용 IP가 없고 표준 부하 분산 장치를 사용 하지 않는 가상 컴퓨터.

### <a name="scenario-1---virtual-machine-with-public-ip"></a><a name="scenario1"></a>시나리오 1-공용 IP를 사용 하는 가상 컴퓨터

| 연결 | 방법 | IP 프로토콜 |
| ---------- | ------ | ------------ |
| 공용 부하 분산 장치 또는 독립 실행형 | [SNAT](#snat) </br> [포트](#pat) 를 가장 하는 데 사용 되지 않습니다. | TCP </br> UDP </br> ICMP </br> ESP |

#### <a name="description"></a>Description

Azure는 모든 아웃 바운드 흐름에 대해 인스턴스의 NIC IP 구성에 할당 된 공용 IP를 사용 합니다. 인스턴스에 있는 모든 삭제 포트를 사용할 수 있습니다. VM이 부하 분산 되었는지 여부는 중요 하지 않습니다. 이 시나리오는 다른 시나리오에 우선합니다. 

VM에 할당된 공용 IP는 1:다가 아닌 1:1 관계이며 상태 비저장 1:1 NAT로 구현됩니다.

### <a name="scenario-2---virtual-machine-without-public-ip"></a><a name="scenario2"></a>시나리오 2-공용 IP가 없는 가상 컴퓨터

| 연결 | 방법 | IP 프로토콜 |
| ------------ | ------ | ------------ |
| 공용 부하 분산 장치 | [포트 가장 (PAT)](#pat)로 [SNAT](#snat) 에 부하 분산 장치 프런트 엔드를 사용 합니다.| TCP </br> UDP |

#### <a name="description"></a>Description

부하 분산 장치 리소스는 부하 분산 장치 규칙을 사용 하 여 구성 됩니다. 이 규칙은 백 엔드 풀을 사용 하 여 공용 IP 프런트 엔드 간에 링크를 만드는 데 사용 됩니다. 

이 규칙 구성을 완료 하지 않으면 시나리오 3에 설명 된 대로 동작 합니다. 

수신기를 사용 하는 규칙은 상태 프로브를 성공 하는 데 필요 하지 않습니다.

VM이 아웃 바운드 흐름을 만들 때 Azure는 원본 IP 주소를 공용 부하 분산 장치 프런트 엔드의 공용 IP 주소로 변환 합니다. 이 변환은 [SNAT](#snat)를 통해 수행 됩니다. 

부하 분산 장치 프런트 엔드 공용 IP 주소의 삭제 포트는 VM에서 발생 하는 개별 흐름을 구별 하는 데 사용 됩니다. SNAT은 아웃바운드 흐름이 생성될 때 동적으로 [삭제 포트를 미리 할당](#preallocatedports)합니다. 

이 컨텍스트에서 SNAT에 사용되는 임시 포트를 SNAT 포트라고 부릅니다. SNAT 포트는 [기본 snat 포트 할당 테이블](#snatporttable)에 설명 된 대로 미리 할당 됩니다.

### <a name="scenario-3---virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a> 시나리오 3-공용 IP가 없고 표준 부하 분산 장치를 사용 하지 않는 가상 컴퓨터

| 연결 | 방법 | IP 프로토콜 |
| ------------ | ------ | ------------ |
|없음 </br> 기본 부하 분산 장치 | 포트를 가장 하는 [SNAT](#snat) [(PAT)](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Description

VM이 아웃 바운드 흐름을 만들 때 Azure는 아웃 바운드 흐름의 원본 IP 주소를 공용 원본 IP 주소로 변환 합니다. 이 공용 IP 주소는 **구성할** 수 없으며 예약할 수 없습니다. 이 주소는 구독의 공용 IP 리소스 제한에 대해 계산 되지 않습니다. 

를 다시 배포 하는 경우 공용 IP 주소가 해제 되 고 새 공용 IP가 요청 됩니다. 

* Virtual Machine
* 가용성 집합
* 가상 머신 크기 집합  

허용 목록에 Ip를 추가 하는 경우에는이 시나리오를 사용 하지 마세요. 아웃 바운드 동작을 명시적으로 선언 하는 시나리오 1 또는 2를 사용 합니다. [Snat](#snat) 포트는 [기본 snat 포트 할당 테이블](#snatporttable)에 설명 된 대로 미리 할당 된 됩니다.



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>포트 할당 알고리즘

Azure는 사용 가능한 미리 할당 된 [SNAT](#snat) 포트 수를 결정 하는 알고리즘을 사용 합니다. 알고리즘은 백 엔드 풀의 크기에 대 한 포트 수를 기반으로 합니다. 

부하 분산 장치와 연결 된 모든 공용 IP 주소에 대해 64000 포트를 [SNAT](#snat) 포트로 사용할 수 있습니다. UDP 및 TCP에 대해 동일한 수의 [SNAT](#snat) 포트를 미리 할당 된 합니다. 포트는 IP 프로토콜에 따라 독립적으로 사용 됩니다. 

[SNAT](#snat) 포트 사용은 흐름이 UDP 인지 TCP 인지에 따라 달라 집니다. 

포트는 미리 할당 된 제한까지 동적으로 사용 됩니다.  포트는 흐름이 닫히거나 유휴 시간 제한이 발생할 때 해제 됩니다.

유휴 시간 제한에 대 한 자세한 내용은 [Azure Load Balancer의 아웃 바운드 연결 문제 해결](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 을 참조 하세요. 

포트는 흐름을 고유하게 만드는 데 필요한 경우에만 사용됩니다.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> 동적 SNAT 포트 미리 할당

다음 표에서는 백 엔드 풀 크기 계층에 대 한 [SNAT](#snat) 포트 사전 할당을 보여 줍니다.

| 풀 크기(VM 인스턴스) | IP 구성별로 미리 할당된 SNAT 포트 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

백 엔드 풀의 크기를 변경 하면 설정 된 흐름에 영향을 줄 수 있습니다.

- 백 엔드 풀 크기는 트리거 전환을 다음 계층으로 늘립니다. 다음 계층으로 전환 하는 동안 미리 할당 된 [SNAT](#snat) 포트의 절반이 회수 됩니다. 

- 회수 된 [SNAT](#snat) 포트 시간 제한 및 닫기와 연결 된 흐름입니다. 이러한 흐름은 다시 설정 해야 합니다. 미리 할당된 포트를 사용할 수 있는 한, 새 흐름이 즉시 성공합니다.

- 백 엔드 풀 크기가 줄어들고 하위 계층으로 전환 되 면 사용 가능한 [SNAT](#snat) 포트 수가 늘어납니다. 지정 된 기존 [SNAT](#snat) 포트 및 해당 흐름이 영향을 받지 않습니다.

## <a name="outbound-rules"></a><a name="outboundrules"></a>아웃 바운드 규칙

 아웃 바운드 규칙은 공용 [표준 부하 분산 장치의](load-balancer-standard-overview.md)아웃 바운드 네트워크 주소 변환을 구성할 수 있도록 합니다.  

> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 가상 네트워크의 가상 머신에 대 한 아웃 바운드 연결을 제공할 수 있습니다.  자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

사용자 요구에 맞게이 기능을 확장 하 고 조정 하기 위해 아웃 바운드 연결에 대 한 모든 선언적 제어를 제공 합니다. 이 섹션에서는 위에서 설명한 대로 시나리오 2를 확장 합니다.

![부하 분산 장치 아웃 바운드 규칙](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

아웃 바운드 규칙을 사용 하면 부하 분산 장치를 사용 하 여 처음부터 아웃 바운드 NAT를 정의할 수 있습니다. 기존 아웃 바운드 NAT의 동작을 확장 하 고 조정할 수도 있습니다.

아웃바운드 규칙을 통해 제어할 수 있는 항목은 다음과 같습니다.

- 어떤 가상 머신이 공용 IP 주소로 변환 되어야 하는지 지정 합니다.
- 아웃 바운드 [SNAT](#snat) 포트를 지정 하는 방법입니다.
- 아웃 바운드 변환을 제공할 프로토콜입니다.
- 아웃 바운드 연결 유휴 시간 제한 (4-120 분)에 사용할 기간입니다.
- 유휴 시간 제한 시 TCP 재설정을 보낼지 여부
- 단일 규칙을 사용 하는 TCP 및 UDP 전송 프로토콜 모두

### <a name="outbound-rule-definition"></a>아웃 바운드 규칙 정의

아웃 바운드 규칙은 부하 분산 및 인바운드 NAT 규칙과 동일한 익숙한 구문을 따릅니다. **프런트 엔드**  +  **매개 변수**  +  **백엔드 풀**. 아웃바운드 규칙은 _백 엔드 풀에서 식별된 모든 가상 머신_에 대한 아웃바운드 NAT를 _프런트 엔드_로 변환하도록 구성합니다.  _매개 변수_ 는 아웃 바운드 NAT 알고리즘에 대 한 추가 세분화 된 제어를 제공 합니다.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> 여러 IP 주소를 사용하여 아웃바운드 NAT 확장

프런트 엔드에서 제공 되는 각 추가 IP 주소는 부하 분산 장치에서 SNAT 포트로 사용할 추가 64000 삭제 포트를 제공 합니다. 

여러 IP 주소를 사용 하 여 대규모 시나리오에 대 한 계획을 세워야 합니다. 아웃 바운드 규칙을 사용 하 여 [SNAT 소모](troubleshoot-outbound-connection.md#snatexhaust)를 완화 합니다. 

아웃 바운드 규칙을 사용 하 여 직접 [공용 IP 접두사](https://aka.ms/lbpublicipprefix) 를 사용할 수도 있습니다. 

공용 IP 접두사는 배포의 크기를 늘립니다. Azure 리소스에서 시작 하는 흐름의 허용 목록에 접두사를 추가할 수 있습니다. 공용 IP 주소 접두사를 참조 하도록 부하 분산 장치 내에서 프런트 엔드 IP 구성을 구성할 수 있습니다.  

부하 분산 장치는 공용 IP 접두사를 제어 합니다. 아웃 바운드 규칙은 아웃 바운드 연결에 대 한 공용 IP 접두사 내에 포함 된 모든 공용 IP 주소를 자동으로 사용 합니다. 

공용 IP 접두사 내의 각 IP 주소는 부하 분산 장치에서 SNAT 포트로 사용할 IP 주소 당 추가 64000 삭제 포트를 제공 합니다.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> 아웃 바운드 흐름 유휴 시간 제한 및 TCP 다시 설정

아웃바운드 규칙은 아웃바운드 흐름 유휴 시간 제한을 제어하고 이를 애플리케이션의 요구와 일치시키는 구성 매개 변수를 제공합니다. 아웃바운드 유휴 시간 제한은 기본적으로 4분입니다. 자세한 내용은 [유휴 시간 제한 구성](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)을 참조 하세요. 

부하 분산 장치의 기본 동작은 아웃 바운드 유휴 시간 제한에 도달 했을 때 흐름을 자동으로 삭제 하는 것입니다. `enableTCPReset`매개 변수를 사용 하면 예측 가능한 응용 프로그램 동작 및 컨트롤을 사용할 수 있습니다. 매개 변수는 아웃 바운드 유휴 시간 제한의 제한 시간에 양방향 TCP 다시 설정 (TCP RST)을 보낼지 여부를 결정 합니다. 

지역 가용성을 비롯 한 세부 정보는 [유휴 시간 제한에서 TCP 다시 설정](https://aka.ms/lbtcpreset) 을 검토 합니다.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>아웃바운드 연결 방지

부하 분산 규칙은 아웃 바운드 NAT의 자동 프로그래밍 기능을 제공 합니다. 일부 시나리오에서는 부하 분산 규칙에 따라 아웃 바운드 NAT의 자동 프로그래밍을 사용 하지 않도록 설정 해야 합니다. 규칙을 통해 사용 하지 않도록 설정 하면 동작을 제어 하거나 구체화할 수 있습니다.  

이 매개 변수는 다음 두 가지 방법으로 사용할 수 있습니다.

1. 아웃 바운드 SNAT에 대 한 인바운드 IP 주소를 방지 합니다. 부하 분산 규칙에서 아웃 바운드 SNAT를 사용 하지 않도록 설정 합니다.
  
2. 인바운드 및 아웃 바운드에 대해 동시에 사용 되는 IP 주소의 아웃 바운드 [SNAT](#snat) 매개 변수를 조정 합니다. 아웃 바운드 규칙을 제어할 수 있도록 자동 아웃 바운드 NAT를 사용 하지 않도록 설정 해야 합니다. 인바운드에도 사용 되는 주소의 SNAT 포트 할당을 변경 하려면 `disableOutboundSnat` 매개 변수를 true로 설정 해야 합니다. 

인바운드에 사용 되는 IP 주소를 다시 정의 하려고 하면 아웃 바운드 규칙을 구성 하는 작업이 실패 합니다.  먼저 부하 분산 규칙의 아웃 바운드 NAT를 사용 하지 않도록 설정 합니다.

>[!IMPORTANT]
> 이 매개 변수를 true로 설정 하 고 아웃 바운드 연결을 정의 하는 아웃 바운드 규칙을 포함 하지 않는 경우에는 가상 컴퓨터에서 아웃 바운드 연결이 설정 되지 않습니다.  VM 또는 애플리케이션의 일부 작업은 아웃바운드 연결을 사용할 수 있는지에 따라 달라질 수 있습니다. 시나리오의 종속성을 이해하고 이러한 변경의 영향을 고려해야 합니다.

경우에 따라 VM이 아웃 바운드 흐름을 만드는 것이 바람직하지 않습니다. 아웃 바운드 흐름을 수신 하는 대상 또는 인바운드 흐름을 시작 하는 대상을 관리 하기 위한 요구 사항이 있을 수 있습니다. [네트워크 보안 그룹](../virtual-network/security-overview.md) 을 사용 하 여 VM이 도달할 수 있는 대상을 관리 합니다. NSGs를 사용 하 여 인바운드 흐름을 시작 하는 공용 대상을 관리 합니다.

부하 분산 VM에 NSG를 적용할 때 [서비스 태그](../virtual-network/security-overview.md#service-tags) 및 [기본 보안 규칙](../virtual-network/security-overview.md#default-security-rules)에 주의해야 합니다. VM이 Azure Load Balancer에서 상태 프로브 요청을 받을 수 있는지 확인 합니다.

NSG가 AZURE_LOADBALANCER 기본 태그의 상태 프로브 요청을 차단할 경우 VM 상태 프로브에 실패하고 VM가 표시됩니다. 부하 분산 장치는 해당 VM에 새 흐름을 보내는 작업을 중지합니다.

## <a name="scenarios-with-outbound-rules"></a>아웃 바운드 규칙을 사용 하는 시나리오

### <a name="outbound-rules-scenarios"></a>아웃 바운드 규칙 시나리오

* [시나리오 1](#scenario1out) -공용 ip 또는 접두사의 특정 집합에 대 한 아웃 바운드 연결을 구성 합니다.
* [시나리오 2](#scenario2out) - [SNAT](#snat) 포트 할당을 수정 합니다.
* [시나리오 3](#scenario3out) -아웃 바운드만 사용 합니다.
* [시나리오 4](#scenario4out) -vm에 대 한 아웃 바운드 NAT만 (인바운드 없음)
* [시나리오 5](#scenario5out) -내부 표준 부하 분산 장치에 대 한 아웃 바운드 NAT.
* [시나리오 6](#scenario6out) -공용 표준 부하 분산 장치를 사용 하는 아웃 바운드 NAT에 TCP & UDP 프로토콜을 모두 사용 하도록 설정 합니다.

### <a name="scenario-1"></a><a name="scenario1out"></a>시나리오 1

| 시나리오 |
| -------- |
| 공용 Ip 또는 접두사의 특정 집합에 대 한 아웃 바운드 연결 구성|

#### <a name="details"></a>세부 정보

이 시나리오를 사용 하 여 공용 IP 주소 집합에서 시작 되는 아웃 바운드 연결을 조정할 수 있습니다. 발생에 따라 허용 또는 거부 목록에 공용 Ip 또는 접두사를 추가 합니다.

이 공용 IP 또는 접두사는 부하 분산 규칙에서 사용 하는 것과 동일할 수 있습니다. 

부하 분산 규칙에서 사용 하는 것과 다른 공용 IP 또는 접두사를 사용 하려면 다음을 수행 합니다.  

1. 공용 IP 접두사 또는 공용 IP 주소를 만듭니다.
2. 공용 표준 부하 분산 장치 만들기 
3. 사용할 공용 IP 접두사 또는 공용 IP 주소를 참조 하는 프런트 엔드를 만듭니다. 
4. 백 엔드 풀을 다시 사용 하거나 백 엔드 풀을 만들고 Vm을 공용 부하 분산 장치의 백 엔드 풀에 추가 합니다.
5. 프런트 엔드를 사용 하 여 Vm에 대해 아웃 바운드 NAT를 사용 하도록 공용 부하 분산 장치에서 아웃 바운드 규칙을 구성 합니다. 아웃 바운드에 대해 부하 분산 규칙을 사용 하지 않으려면 부하 분산 규칙에 대해 아웃 바운드 SNAT를 사용 하지 않도록 설정 합니다.

### <a name="scenario-2"></a><a name="scenario2out"></a>시나리오 2

| 시나리오 |
| -------- |
| [SNAT](#snat) 포트 할당 수정 |

#### <a name="details"></a>세부 정보

아웃바운드 규칙을 사용하여 [백 엔드 풀 크기를 기준으로 자동 SNAT 포트 할당](load-balancer-outbound-connections.md#preallocatedports)을 튜닝할 수 있습니다. 

SNAT 고갈를 발생 하는 경우 기본값 1024에서 제공 되는 [SNAT](#snat) 포트 수를 늘립니다. 

각 공용 IP 주소는 최대 64000 개의 임시 포트를 제공 합니다. 백 엔드 풀의 Vm 수는 각 VM에 배포 되는 포트 수를 결정 합니다. 백 엔드 풀의 한 VM은 최대 64000 포트에 액세스할 수 있습니다. 두 Vm의 경우 아웃 바운드 규칙 (2x 32000 = 64000)을 사용 하 여 최대 32000 [SNAT](#snat) 포트를 지정할 수 있습니다. 

아웃 바운드 규칙을 사용 하 여 기본적으로 제공 되는 SNAT 포트를 튜닝할 수 있습니다. 기본 [SNAT](#snat) 포트 할당에 제공 되는 것 보다 더 많은 또는 더 작은 값을 제공 합니다. 아웃 바운드 규칙의 프런트 엔드에 있는 각 공용 IP 주소는 [SNAT](#snat) 포트로 사용 하기 위해 최대 64000 개의 임시 포트를 제공 합니다.  

부하 분산 장치는 [SNAT](#snat) 포트를 8의 배수로 제공 합니다. 8로 나눌 수 없는 값을 제공하면 구성 작업이 거부됩니다. 

공용 IP 주소의 수를 기준으로 사용할 수 있는 것 보다 더 많은 [SNAT](#snat) 포트를 제공 하려고 하면 구성 작업이 거부 됩니다.

VM 당 1만 포트를 제공 하 고 백 엔드 풀에서 7 개의 Vm이 단일 공용 IP를 공유 하는 경우 구성이 거부 됩니다. 7과 1만를 곱하여 64000 포트 제한을 초과 합니다. 아웃 바운드 규칙의 프런트 엔드에 공용 IP 주소를 더 추가 하 여 시나리오를 사용 하도록 설정 합니다. 

포트 수에 0을 지정 하 여 [기본 포트 할당](load-balancer-outbound-connections.md#preallocatedports) 으로 되돌립니다. 첫 번째 50 VM 인스턴스는 1024 포트를 가져오며, 51-100 VM 인스턴스는 최대 인스턴스 수에 대 한 512을 받습니다.  기본 SNAT 포트 할당에 대 한 자세한 내용은 [위의](#snatporttable)항목을 참조 하세요.

### <a name="scenario-3"></a><a name="scenario3out"></a>시나리오 3

| 시나리오 |
| -------- |
|  아웃바운드만 사용 |

#### <a name="details"></a>세부 정보

공용 표준 부하 분산 장치를 사용 하 여 Vm 그룹에 아웃 바운드 NAT를 제공할 수 있습니다. 이 시나리오에서는 추가 규칙을 요구 하지 않고 아웃 바운드 규칙을 단독으로 사용 합니다.

> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 부하 분산 장치 없이도 가상 컴퓨터에 대 한 아웃 바운드 연결을 제공할 수 있습니다.  자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

### <a name="scenario-4"></a><a name="scenario4out"></a>시나리오 4

| 시나리오 |
| -------- |
| VM 전용 아웃바운드 NAT(인바운드 없음) |

> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 부하 분산 장치 없이도 가상 컴퓨터에 대 한 아웃 바운드 연결을 제공할 수 있습니다.  자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

#### <a name="details"></a>세부 정보

이 시나리오의 경우:

1. 공용 IP 또는 접두사를 만듭니다.
2. 공용 표준 부하 분산 장치를 만듭니다. 
3. 아웃 바운드에 전용으로 사용 되는 공용 IP 또는 접두사와 연결 된 프런트 엔드를 만듭니다.
4. Vm에 대 한 백 엔드 풀을 만듭니다.
5. Vm을 백 엔드 풀에 저장 합니다.
6. 아웃 바운드 NAT를 사용 하도록 아웃 바운드 규칙을 구성 합니다.

접두사나 공용 IP를 사용 하 여 [SNAT](#snat) 포트의 크기를 조정 합니다. 허용 또는 거부 목록에 아웃 바운드 연결의 원본을 추가 합니다.

### <a name="scenario-5"></a><a name="scenario5out"></a>시나리오 5

| 시나리오 |
| -------- |
| 내부 표준 부하 분산 장치에 대 한 아웃 바운드 NAT |

> [!NOTE]
> **Azure VIRTUAL NETWORK NAT** 는 내부 표준 부하 분산 장치를 활용 하는 가상 머신에 대 한 아웃 바운드 연결을 제공할 수 있습니다.  자세한 내용은 [Azure VIRTUAL NETWORK NAT 란?을](../virtual-network/nat-overview.md) 참조 하세요.

#### <a name="details"></a>세부 정보

내부 표준 부하 분산 장치는 명시적으로 선언 될 때까지 아웃 바운드 연결을 사용할 수 없습니다. 

자세한 내용은 [아웃 바운드 전용 부하 분산 장치 구성](https://docs.microsoft.com/azure/load-balancer/egress-only)을 참조 하세요.


### <a name="scenario-6"></a><a name="scenario6out"></a>시나리오 6

| 시나리오 |
| -------- |
| 공용 표준 부하 분산 장치를 사용 하는 아웃 바운드 NAT에 TCP & UDP 프로토콜 모두 사용 |

#### <a name="details"></a>세부 정보

공용 표준 부하 분산 장치를 사용 하는 경우 제공 된 자동 아웃 바운드 NAT가 부하 분산 규칙의 전송 프로토콜과 일치 합니다. 

1. 부하 분산 규칙에서 아웃 바운드 [SNAT](#snat) 를 사용 하지 않도록 설정 합니다. 
2. 동일한 부하 분산 장치에서 아웃 바운드 규칙을 구성 합니다.
3. VM에서 이미 사용하고 있는 백 엔드 풀을 다시 사용합니다. 
4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다. 

인바운드 NAT 규칙만 사용하는 경우 아웃바운드 NAT는 제공되지 않습니다. 

1. 백 엔드 풀에 VM을 배치합니다.
2. 공용 ip 주소 또는 공용 IP 접두사를 사용 하 여 하나 이상의 프런트 엔드 IP 구성을 정의 합니다. 
3. 동일한 부하 분산 장치에서 아웃 바운드 규칙을 구성 합니다. 
4. 아웃바운드 규칙의 일부로 "프로토콜"을 "모두"로 지정합니다.

## <a name="terminology"></a><a name="terms"></a> 기술

### <a name="source-network-address-translation"></a><a name="snat"></a>원본 네트워크 주소 변환

| 적용 가능한 프로토콜 |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>세부 정보

Azure에 배포되는 솔루션은 공용 IP 주소 공간에 있는 Azure 외부의 엔드포인트와 통신할 수 있습니다.

인스턴스가 공용 ip 주소를 사용 하 여 대상에 대 한 아웃 바운드 트래픽을 시작 하면 Azure는 리소스의 개인 IP 주소를 공용 IP 주소에 동적으로 매핑합니다. 

이 매핑을 만든 후에는이 아웃 바운드에서 시작 된 흐름에 대 한 반환 트래픽이 흐름이 시작 된 개인 IP 주소에 도달 합니다. 

Azure는 **SNAT (원본 네트워크 주소 변환)** 을 사용 하 여이 기능을 수행 합니다.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>포트 가장 SNAT(PAT)

| 적용 가능한 프로토콜 |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>세부 정보

개인 ip가 단일 공용 IP 주소 뒤에 있는 경우 Azure는 **PAT (포트 주소 변환)** 를 사용 하 여 개인 IP 주소를 숨깁니다. 

삭제 포트는 PAT에 사용되고 풀 크기에 따라 [미리 할당](#preallocatedports)됩니다. 

공용 부하 분산 장치는 공용 Ip가 없는 Vm에 연결 된 경우 각 아웃 바운드 연결 원본이 다시 작성 됩니다. 

원본은 가상 네트워크 개인 IP 주소에서 부하 분산 장치의 프런트 엔드 공용 IP 주소로 다시 작성 됩니다. 

공용 IP 주소 공간에서 아래 흐름의 5 개 튜플은 고유 해야 합니다.

* 원본 IP 주소
* 원본 포트
* IP 전송 프로토콜
* 대상 IP 주소
* 대상 포트 

TCP 또는 UDP 프로토콜을 사용 하 여 포트를 가장 하는 SNAT를 사용할 수 있습니다. 여러 흐름이 단일 공용 IP 주소에서 시작 되므로 SNAT 포트는 개인 원본 IP 주소를 다시 작성 한 후에 사용 됩니다. 포트를 위장 하는 SNAT 알고리즘은 UDP 및 TCP에 대해 SNAT 포트를 다르게 제공 합니다.

### <a name="snat-ports-tcp"></a>SNAT 포트 (TCP)

| 적용 가능한 프로토콜 |
|------------------------|
| TCP |

#### <a name="details"></a>세부 정보

SNAT 포트는 공용 IP 원본 주소에 사용할 수 있는 사용 후 삭제 포트입니다. 단일 대상 IP 주소 및 포트에 대한 흐름 당 하나의 SNAT 포트를 사용합니다. 

동일한 대상 IP 주소, 포트 및 프로토콜에 대한 여러 TCP 흐름의 경우 각 TCP 흐름은 단일 SNAT 포트를 사용합니다. 

이렇게 하면 동일한 공용 IP 주소에서 시작 하 여로 이동할 때 흐름이 고유 하 게 유지 됩니다.

* 동일한 대상 IP 주소
* 포트
* 프로토콜 

여러 흐름은 단일 SNAT 포트를 공유 합니다. 

대상 IP 주소, 포트 및 프로토콜은 공용 IP 주소 공간에서 흐름을 구분하는 추가 원본 포트를 사용하지 않고도 흐름을 고유하게 만들어줍니다.


### <a name="snat-ports-udp"></a>SNAT 포트 (UDP)

| 적용 가능한 프로토콜 |
|------------------------|
| UDP |

#### <a name="details"></a>세부 정보

UDP SNAT 포트는 TCP SNAT 포트와는 다른 알고리즘을 통해 관리됩니다.  부하 분산 장치는 UDP에 대해 포트 제한 원뿔형 NAT 라는 알고리즘을 사용 합니다.

각 흐름의 모든 대상 IP 주소와 포트에 대해 하나의 SNAT 포트가 사용 됩니다.


### <a name="exhaustion"></a><a name="exhaustion"></a>고갈

| 적용 가능한 프로토콜 |
|------------------------|
| 해당 없음 |

#### <a name="details"></a>세부 정보

SNAT 포트 리소스가 고갈되면 기존 흐름에서 SNAT 포트를 릴리스할 때까지 아웃바운드 흐름이 실패합니다. 부하 분산 장치는 흐름이 닫힐 때 SNAT 포트를 회수 합니다.

부하 분산 장치에서 SNAT 포트를 회수 하는 데 4 분의 [유휴 제한 시간이](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 사용 됩니다.

UDP SNAT 포트는 일반적으로 사용 되는 알고리즘의 차이로 인해 TCP SNAT 포트 보다 훨씬 더 빠릅니다. 이러한 차이로 인해 테스트를 디자인 하 고 크기를 조정 합니다.

### <a name="snat-port-release-behavior-tcp"></a>SNAT 포트 해제 동작 (TCP)

| 적용 가능한 프로토콜 |
|------------------------|
| TCP |

#### <a name="details"></a>세부 정보

서버 또는 클라이언트에서 FINACK를 보내는 경우 240 초 후에 SNAT 포트가 해제 됩니다. RST이 표시 되는 경우 15 초 후에 SNAT 포트가 해제 됩니다. 유휴 시간 제한에 도달 하면 포트가 해제 됩니다.

### <a name="snat-port-release-behavior-udp"></a>SNAT 포트 해제 동작 (UDP)

| 적용 가능한 프로토콜 |
|------------------------|
| TCP |

#### <a name="details"></a>세부 정보

유휴 시간 제한에 도달 하면 포트가 해제 됩니다.

### <a name="snat-port-reuse"></a>SNAT 포트 재사용

| 적용 가능한 프로토콜 |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>세부 정보

포트를 해제 한 후에는 포트를 다시 사용할 수 있습니다. SNAT 포트는 지정 된 시나리오에서 사용 가능한 최하위에서 가장 높은 시퀀스 이며, 첫 번째 사용 가능한 SNAT 포트는 새 연결에 사용 됩니다.

## <a name="limitations"></a>제한 사항

- 프런트 엔드 IP 주소당 사용 가능한 사용 후 삭제 포트의 최대 수는 64,000개입니다.
- 구성 가능한 아웃바운드 유휴 시간 제한 범위는 4-120분(240-7200초)입니다.
- 부하 분산 장치는 아웃 바운드 NAT에 대 한 ICMP를 지원 하지 않습니다.
- 아웃 바운드 규칙은 NIC의 기본 IP 구성에만 적용할 수 있습니다.  VM 또는 NVA의 보조 IP에 대 한 아웃 바운드 규칙을 만들 수 없습니다. 여러 Nic가 지원 됩니다.
- 내부 표준 부하 분산 장치를 사용 하는 경우 가상 네트워크 및 기타 Microsoft 플랫폼 서비스를 사용 하지 않는 웹 작업자 역할에 액세스할 수 있습니다. 이 액세스 가능성은 이전 VNet 서비스 및 기타 플랫폼 서비스가 작동 하는 방식에 대 한 부작용 때문에 발생 합니다. 각 서비스 자체 또는 기본 플랫폼이 예 고 없이 변경 될 수 있기 때문에이 부작용에 의존해 서는 안 됩니다. 내부 표준 부하 분산 장치만 사용 하는 경우 필요에 따라 아웃 바운드 연결을 명시적으로 만들어야 한다고 가정 합니다. 이 문서에서 설명 하는 시나리오 3은 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

Azure Load Balancer를 통한 아웃 바운드 연결에 문제가 발생 하는 경우 [아웃 바운드 연결에 대 한 문제 해결 가이드](../load-balancer/troubleshoot-outbound-connection.md)를 참조 하세요.

- [표준 부하 분산 장치](load-balancer-standard-overview.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대 한 질문과 대답](load-balancer-faqs.md)을 참조 하세요.
- 표준 공용 부하 분산 장치에 대 한 [아웃 바운드 규칙](load-balancer-outbound-rules-overview.md) 에 대해 자세히 알아보세요.

