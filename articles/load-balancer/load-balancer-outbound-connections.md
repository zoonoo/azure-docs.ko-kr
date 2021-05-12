---
title: 아웃바운드 연결을 위한 SNAT(Source Network Address Translation)
titleSuffix: Azure Load Balancer
description: Azure Load Balancer에서 아웃바운드 인터넷 연결(SNAT)을 사용하는 방법을 알아봅니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 99f15afdab917fe28e22df8cb0e372b6c30c8526
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027332"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>아웃바운드 연결을 위해 SNAT(Source Network Address Translation) 사용

Azure 퍼블릭 부하 분산 장치의 프런트 엔드 IP를 사용하여 백 엔드 인스턴스에 대한 인터넷에 아웃바운드 연결을 제공할 수 있습니다. 이 구성에서는 **SNAT(Source Network Address Translation)** 을 사용합니다. SNAT는 백 엔드의 IP 주소를 부하 분산 장치의 공용 IP 주소로 다시 생성합니다. 

SNAT는 백 엔드 인스턴스의 **IP 위장** 을 가능하게 합니다. 이렇게 하면 외부 소스에서 백 엔드 인스턴스에 대한 직접 주소를 알지 못합니다. 백 엔드 인스턴스 간에 IP 주소를 공유하면 정적 공용 IP의 비용을 줄일 수 있습니다. 알려진 IP 주소는 알려진 공용 IP로부터의 트래픽으로 IP 허용 목록을 간소화하는 것과 같은 시나리오를 지원합니다. 

>[!Note]
> 다수의 아웃바운드 연결이 필요한 애플리케이션이나 지정된 가상 네트워크에서 단일 IP 집합을 사용해야 하는 기업 고객의 경우 [Virtual Network NAT](../virtual-network/nat-overview.md)을 사용하는 것이 권장됩니다. 동적 할당을 통해 간단하게 구성할 수 있으며 각 IP 주소에서 SNAT 포트를 가장 효율적으로 사용할 수 있습니다. 가상 네트워크의 모든 리소스에서 부하 분산 장치를 공유할 필요 없이 IP 주소 집합을 공유할 수 있습니다.

>[!Important]
> 아웃바운드 SNAT가 구성되지 않은 경우에도 동일한 지역 내의 Azure 스토리지 계정에 계속 액세스할 수 있으며, 백 엔드 리소스는 Windows 업데이트와 같은 Microsoft 서비스에 계속 액세스할 수 있습니다.

>[!NOTE] 
>이 문서에서는 Azure Resource Manager 배포에 대해서만 설명합니다. Azure의 모든 클래식 배포 시나리오는 [아웃 바운드 연결(클래식)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic)을 참조하세요.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> 백 엔드 리소스에서 프런트 엔드 IP 주소 공유

부하 분산 장치의 백 엔드 리소스에 ILPIP(인스턴스 수준 공용 IP) 주소가 없는 경우, 백 엔드 리소스는 퍼블릭 부하 분산 장치의 프런트 엔드 IP를 통해 아웃바운드 연결을 설정합니다. 포트는 고유한 흐름을 유지하는 데 사용되는 고유 식별자 생성에 사용됩니다. 인터넷은 5개의 튜플을 사용하여 해당 고유성을 제공합니다.

5개의 튜플은 다음으로 구성됩니다.

* 대상 IP
* 대상 포트
* 원본 IP
* 소스 포트 및 프로토콜을 통해 해당 고유성을 제공합니다.

인바운드 연결에 포트를 사용하는 경우 해당 포트의 인바운드 연결 요청에 대한 **수신기** 가 존재합니다. 아웃바운드 연결에는 해당 포트를 사용할 수 없습니다. 아웃바운드 연결을 설정하려면 고유한 트래픽 흐름을 전달하고 유지할 수 있는 포트를 대상에 제공하는 **임시 포트** 가 사용됩니다. 임시 포트가 SNAT에 사용되는 경우 **SNAT 포트** 라고 합니다. 

정의에 따르면 모든 IP 주소에는 65,535개의 포트가 있습니다. 각 포트는 TCP(Transmission Control Protocol) 및 UDP(사용자 데이터그램 프로토콜)의 인바운드 또는 아웃바운드 연결에 사용할 수 있습니다. 

공용 IP 주소를 부하 분산 장치에 프런트 엔드 IP로 추가하면 Azure는 SNAT에 적합한 64,000개의 포트를 제공합니다.

>[!NOTE]
> 부하 분산 또는 인바운드 NAT 규칙에 사용되는 각 포트는 64,000개의 포트 중에서 8개 포트 범위를 사용하여 SNAT에 적합한 포트의 수를 줄입니다. 부하 분산 또는 NAT 규칙이 서로와 동일한 8개 포트 범위 내에 있는 경우 추가 포트를 사용하지 않습니다. 

[아웃바운드 규칙](./outbound-rules.md) 및 부하 분산 규칙을 통해 SNAT 포트를 백 엔드 인스턴스에 배포할 수 있으며, 이로서 백 엔드 인스턴스는 아웃바운드 연결에 대한 부하 분산 장치의 공용 IP를 공유할 수 있습니다.

아래의 [시나리오 2](#scenario2) 가 구성된 경우 각 백 엔드 인스턴스에 대한 호스트는 아웃바운드 연결의 일부인 패킷을 SNAT합니다. 

백 엔드 인스턴스에서 아웃바운드 연결에 대해 SNAT를 수행하는 경우 호스트는 원본 IP를 프런트 엔드 IP 중 하나로 다시 생성합니다. 

호스트는 고유한 흐름을 유지하기 위해 백 엔드 인스턴스의 SNAT 포트에 각 아웃바운드 패킷의 원본 포트를 다시 생성합니다.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>다양한 시나리오에 대한 아웃바운드 연결 동작
  * 공용 IP를 사용하는 가상 머신.
  * 공용 IP를 사용하지 않는 가상 머신.
  * 공용 IP가 없고 표준 부하 분산 장치를 사용하지 않는 가상 머신.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> 시나리오 1: 공용 IP를 사용하는 가상 머신

 | 연결 | 방법 | IP 프로토콜 |
 | ---------- | ------ | ------------ |
 | 퍼블릭 부하 분산 장치 또는 독립 실행형 | [SNAT(Source Network Address Translation)](#snat) </br> 사용되지 않습니다. | TCP(Transmission Control Protocol) </br> UDP(사용자 데이터그램 프로토콜) </br> ICMP(Internet Control Message Protocol) </br> ESP(보안 페이로드 캡슐화) |

 #### <a name="description"></a>Description

 Azure는 모든 아웃바운드 흐름에 대해 인스턴스 NIC의 IP 구성에 할당된 공용 IP를 사용합니다. 인스턴스에 있는 모든 삭제 포트를 사용할 수 있습니다. VM이 부하 분산되었는지는 중요하지 않습니다. 이 시나리오는 다른 시나리오에 우선합니다. 

 VM에 할당된 공용 IP는 1:다가 아닌 1:1 관계이며 상태 비저장 1:1 NAT로 구현됩니다.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>시나리오 2: 공용 IP가 없고 표준 퍼블릭 Load Balancer 뒤에 있는 가상 머신

 | 연결 | 방법 | IP 프로토콜 |
 | ------------ | ------ | ------------ |
 | 표준 퍼블릭 부하 분산 장치 | [SNAT](#snat)에 대한 부하 분산 장치 프런트 엔드 IP를 사용합니다.| TCP </br> UDP |

 #### <a name="description"></a>Description

 부하 분산 장치 리소스는 SNAT를 사용하도록 설정하는 아웃바운드 규칙 또는 부하 분산 규칙을 사용하여 구성됩니다. 해당 규칙은 백 엔드 풀을 통해 공용 IP 프런트 엔드 간에 연결점을 만드는 데 사용됩니다. 

 해당 규칙 구성을 완료하지 않은 경우, 해당 동작은 시나리오 3에 설명되어 있습니다. 

 수신기 규칙은 상태 프로브 성공에 꼭 필요하지 않습니다.

 VM이 아웃바운드 흐름을 만든 경우 Azure에서는 원본 IP 주소를 퍼블릭 부하 분산 장치 프론트 엔드의 공용 IP 주소로 변환합니다. 이 변환은 [SNAT](#snat)를 통해 수행됩니다. 

 부하 분산 장치 프런트 엔드 공용 IP 주소의 임시 포트는 VM에서 발생하는 개별 흐름을 구별하는 데 사용됩니다. SNAT은 아웃바운드 흐름이 생성될 때 동적으로 [삭제 포트를 미리 할당](#preallocatedports)합니다. 

 이 컨텍스트에서 SNAT에 사용되는 임시 포트를 SNAT 포트라고 부릅니다. [아웃바운드 규칙](./outbound-rules.md)을 명시적으로 구성하는 것이 좋습니다. 부하 분산 규칙을 통해 기본 SNAT를 사용하는 경우 SNAT 포트는 [기본 SNAT 포트 할당 테이블](#snatporttable)에 설명된 대로 미리 할당됩니다.

> [!NOTE]
> **Azure Virtual Network NAT** 은 부하 분산 장치 없이도 가상 머신에 대한 아웃바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-overview.md)을 참조하세요.

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>시나리오 3: 공용 IP가 없고 표준 내부 Load Balancer 뒤에 있는 가상 머신

 | 연결 | 방법 | IP 프로토콜 |
 | ------------ | ------ | ------------ |
 | 표준 내부 부하 분산 장치 | 인터넷 연결이 없습니다.| 없음 |

 #### <a name="description"></a>Description
 
표준 내부 부하 분산 장치를 사용하는 경우 SNAT에 대한 임시 IP 주소를 사용하지 않습니다. 이 기능은 기본적으로 보안을 지원합니다. 이 기능을 사용하면 리소스에서 사용하는 모든 IP 주소를 구성 및 예약할 수 있습니다. 

표준 내부 부하 분산 장치 사용 시 인터넷에 대한 아웃바운드 연결을 구현하려면 [시나리오 1](#scenario1)의 동작을 따르도록 인스턴스 수준 공용 IP 주소를 구성합니다. 

또 다른 옵션은 아웃바운드 규칙이 구성된 표준 퍼블릭 부하 분산 장치에 백 엔드 인스턴스를 추가하는 것입니다. 백 엔드 인스턴스는 내부 부하 분산을 위해 내부 부하 분산 장치에 추가됩니다. 해당 배포는 [시나리오 2](#scenario2)의 동작을 따릅니다. 

> [!NOTE]
> **Azure Virtual Network NAT** 은 부하 분산 장치 없이도 가상 머신에 대한 아웃바운드 연결을 제공할 수 있습니다. 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-overview.md)을 참조하세요.

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>시나리오 4: 공용 IP가 없고 기본 Load Balancer 뒤에 있는 가상 머신

 | 연결 | 방법 | IP 프로토콜 |
 | ------------ | ------ | ------------ |
 |없음 </br> 기본 Load Balancer | 인스턴스 수준 동적 IP 주소를 사용하는 [SNAT](#snat)| TCP </br> UDP | 

 #### <a name="description"></a>Description

 VM이 아웃바운드 흐름을 만든 경우 Azure에서는 원본 IP 주소를 동적으로 지정된 공용 원본 IP 주소로 변환합니다. 이 공용 IP 주소는 **구성할** 수 없으며 예약할 수 없습니다. 이 주소는 구독의 공용 IP 리소스 제한에 대해 계산되지 않습니다. 

다음을 다시 배포하는 경우 공용 IP 주소가 해제되고 새 공용 IP가 요청됩니다. 

 * Virtual Machine
 * 가용성 집합
 * 가상 머신 크기 집합 

 허용 목록에 IP를 추가하는 데 이 시나리오를 사용하지 마세요. 아웃바운드 동작을 명시적으로 선언하는 시나리오 1 또는 2를 사용하세요. [SNAT](#snat) 포트는 [기본 SNAT 포트 할당 테이블](#snatporttable)에 설명된 대로 사전 할당됩니다.

## <a name="exhausting-ports"></a><a name="scenarios"></a> 포트 고갈

동일한 대상 IP 및 대상 포트에 대한 모든 연결에서 SNAT 포트를 사용합니다. 이 연결은 백 엔드 인스턴스 또는 **클라이언트** 에서 **서버** 로의 고유한 **트래픽 흐름** 을 유지합니다. 해당 프로세스는 트래픽을 처리할 고유 포트를 서버에 제공합니다. 해당 프로세스가 없으면 클라이언트 머신은 패킷이 어떤 흐름에 속해 있는지 인식하지 못합니다.

다수의 브라우저가 https://www.microsoft.com 으로 이동한다고 가정해 보세요. 구성은 다음과 같습니다.

* 대상 IP = 23.53.254.142
* 대상 포트 = 443
* 프로토콜 = TCP

반환 트래픽(연결을 설정하는 데 사용되는 SNAT 포트)에 대해 서로 다른 대상 포트를 사용하지 않는 경우, 클라이언트는 쿼리 결과를 구분할 수 없습니다.

아웃바운드 연결이 폭주할 수 있습니다. 백 엔드 인스턴스에 충분한 포트가 할당되지 못할 수 있습니다. **연결 재사용** 을 사용하도록 설정하지 않으면 SNAT **포트 고갈** 이 발생할 위험이 증가합니다.

포트 고갈이 발생하면 대상 IP에 대한 새 아웃바운드 연결이 실패하게 됩니다. 연결은 포트가 사용 가능하게 되는 경우 성공합니다. 고갈은 IP 주소에 있는 64,000개의 포트가 다수의 백 엔드 인스턴스에 퍼져 있을 때 발생합니다. SNAT 포트 고갈을 완화하는 방법에 대한 지침은 [문제 해결 가이드](./troubleshoot-outbound-connection.md)를 참조하세요.  

TCP 연결의 경우 부하 분산 장치는 모든 대상 IP 및 포트에 대해 단일 SNAT 포트를 사용합니다. 이렇게 다용도로 사용하면 동일한 SNAT 포트를 사용하여 동일한 대상 IP에 다수의 연결이 사용 설정됩니다. 연결이 서로 다른 대상 포트에 연결되지 않은 경우 다용도 사용이 제한됩니다.

UDP 연결의 경우 부하 분산 장치는 대상 포트에 관계 없이 대상 IP당 하나의 SNAT 포트를 사용하는 **포트 제한 원뿔형 NAT** 알고리즘을 사용합니다. 

포트는 연결에 무제한으로 재사용되지만 대상 IP 또는 포트가 서로 다른 경우에만 재사용됩니다.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> 기본 포트 할당

부하 분산 장치의 프런트 엔드 IP로 할당된 각 공용 IP에는 백 엔드 풀 멤버로 64,000개의 SNAT 포트가 지정됩니다. 포트는 백 엔드 풀 구성원과 공유할 수 없습니다. SNAT 포트의 범위는 단일 백 엔드 인스턴스가 패킷이 올바르게 라우팅되는지 확인하는 데만 사용할 수 있습니다. 

명시적 아웃바운드 규칙을 사용하여 SNAT 포트 할당을 구성하는 것이 좋습니다. 이 규칙은 각 백 엔드 인스턴스가 아웃바운드 연결에 사용할 수 있는 SNAT 포트 수를 최대화합니다. 

부하 분산 규칙을 통해 아웃바운드 SNAT의 자동 할당을 사용해야 하는 경우 할당 테이블에서 포트 할당을 정의합니다.

다음 <a name="snatporttable"></a>테이블은 백 엔드 풀 크기 계층의 SNAT 포트 미리 할당을 보여 줍니다.

| 풀 크기(VM 인스턴스) | IP 구성별로 미리 할당된 SNAT 포트 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> 최대 크기가 10인 백 엔드 풀이 있는 경우, 명시적 아웃바운드 규칙을 정의하면 각 인스턴스는 64,000/10 = 6,400개의 포트를 가질 수 있습니다. 위 테이블에 따르면 자동 할당을 선택한 경우에만 각 인스턴스는 1,024개를 가질 수 있습니다.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> 아웃바운드 규칙 및 Virtual Network NAT

Azure Load Balancer 아웃바운드 규칙 및 Virtual Network NAT는 가상 네트워크에서 송신에 사용할 수 있는 옵션입니다.

아웃바운드 규칙에 대한 자세한 내용은 [아웃바운드 규칙](outbound-rules.md)을 참조하세요.

Azure Virtual Network NAT에 대한 자세한 내용은 [Azure Virtual Network NAT란?](../virtual-network/nat-overview.md)을 참조하세요.

## <a name="constraints"></a>제약 조건

*   새 패킷의 전송 없이 연결이 유휴 상태인 경우 포트는 4~120분 후에 해제됩니다.
  * 이 임계값은 아웃바운드 규칙을 통해 구성할 수 있습니다.
*   각 IP 주소는 SNAT에 사용할 수 있는 64,000개의 포트를 제공합니다.
*   각 포트는 대상 IP 주소에 대한 TCP 및 UDP 연결 모두에 사용할 수 있습니다.
  * UDP SNAT 포트는 대상 포트가 고유한지와 관계없이 필요합니다. 하나의 대상 IP에 대한 모든 UDP 연결의 경우 하나의 UDP SNAT 포트만이 사용됩니다.
  * 대상 포트가 서로 다른 경우 동일한 대상 IP에 대한 다수의 연결에 TCP SNAT 포트를 사용할 수 있습니다.
*   지정된 SNAT 포트에 백 엔드 인스턴스가 부족하면 SNAT 고갈이 발생합니다. 부하 분산 장치는 사용되지 않는 SNAT 포트를 계속 사용할 수 있습니다. 백 엔드 인스턴스가 사용하는 SNAT 포트가 지정된 SNAT 포트의 개수를 넘어설 경우 새 아웃바운드 연결을 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

*   [SNAT 고갈로 인한 아웃바운드 연결 실패 문제 해결](./troubleshoot-outbound-connection.md)
*   [SNAT 메트릭을 검토](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation)하고 해당 메트릭을 필터링, 분할, 확인하는 올바른 방법을 숙지하세요.
