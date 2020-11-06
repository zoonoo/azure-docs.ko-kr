---
title: 아웃 바운드 연결에 대 한 SNAT
description: 아웃 바운드 인터넷 연결에 대해 SNAT를 수행 하는 데 Azure Load Balancer를 사용 하는 방법을 설명 합니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: b3924a563d8266cfa38f24106dbb84102031a182
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331875"
---
# <a name="using-snat-for-outbound-connections"></a>아웃 바운드 연결에 SNAT 사용

Azure 공용 부하 분산 장치의 프런트 엔드 Ip를 사용 하 여 백 엔드 인스턴스에 대 한 인터넷에 아웃 바운드 연결을 제공할 수 있습니다. 이 구성에서는 **SNAT (원본 네트워크 주소 변환)** 을 사용 합니다. SNAT는 백 엔드의 IP 주소를 부하 분산 장치의 공용 IP 주소로 다시 작성 합니다. 

SNAT 백 엔드 인스턴스의 **IP** 를 가장 하는 것을 가능 하 게 합니다. 이렇게 하면 외부 소스에서 백 엔드 인스턴스에 대 한 직접 주소를 갖지 못하게 됩니다. 백 엔드 인스턴스 사이에 IP 주소를 공유 하면 고정 공용 ip의 비용이 줄어들고 알려진 공용 ip의 트래픽이 포함 된 IP 허용 목록을 간소화 하는 등의 시나리오를 지원 합니다. 

>[!Note]
> 지정 된 가상 네트워크에서 단일 Ip 집합을 사용 해야 하는 많은 수의 아웃 바운드 연결 또는 엔터프라이즈 고객이 필요한 응용 프로그램의 경우 [NAT Virtual Network](https://docs.microsoft.com/azure/virtual-network/nat-overview) 권장 솔루션입니다. 동적 할당은 간단한 구성을 허용 하 고 각 IP 주소에서 SNAT 포트를 가장 효율적으로 사용 하는 > 합니다. 또한 부하 분산 장치 > 공유 하지 않아도 가상 네트워크의 모든 리소스가 IP 주소 집합을 공유할 수 있습니다.

>[!Important]
> 아웃 바운드 SNAT가 구성 되지 않은 경우에도 동일한 지역 내에서 Azure storage 계정은 여전히 액세스할 수 있으며 백엔드 리소스는 Windows 업데이트와 같은 Microsoft 서비스에 계속 액세스할 수 있습니다.

>[!NOTE] 
>이 문서에서는 Azure Resource Manager 배포에 대해서만 다룹니다. Azure의 모든 클래식 배포 시나리오는 [아웃 바운드 연결(클래식)](load-balancer-outbound-connections-classic.md)을 참조하세요.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> 백 엔드 리소스에서 프런트 엔드 IP 주소 공유

부하 분산 장치의 백 엔드 리소스에 ILPIP (인스턴스 수준 공용 IP) 주소가 없는 경우 공용 부하 분산 장치의 프런트 엔드 IP를 통해 아웃 바운드 연결을 설정 합니다. 포트는 고유한 흐름을 유지 하는 데 사용 되는 고유 식별자를 생성 하는 데 사용 됩니다. 인터넷은 5 튜플을 사용 하 여 이러한 차이를 제공 합니다.

5 개 튜플은 다음과 같이 구성 됩니다.

* 대상 IP
* 대상 포트
* 원본 IP
* 이러한 차이를 제공 하는 원본 포트 및 프로토콜입니다.

인바운드 연결에 포트를 사용 하는 경우 해당 포트의 인바운드 연결 요청에 대 한 **수신기** 를 포함 하 고 아웃 바운드 연결에 사용할 수 없습니다. 아웃 바운드 연결을 설정 하려면 사용 **후 삭제 포트** 를 사용 하 여 통신 하 고 별도의 트래픽 흐름을 유지 관리할 수 있는 포트를 대상에 제공 해야 합니다. 이러한 사용 후 삭제 포트를 사용 하 여 SNAT를 수행 하는 경우 **snat 포트** 라고 합니다. 

정의에 따라 모든 IP 주소에 65535 포트가 있습니다. TCP (전송 제어 프로토콜) 및 UDP (사용자 데이터 그램 프로토콜)의 인바운드 또는 아웃 바운드 연결에 각 포트를 사용할 수 있습니다. 공용 IP 주소를 프런트 엔드 IP로 부하 분산 장치에 추가 하면 Azure는 64000을 SNAT 포트로 사용할 수 있도록 합니다. 

>[!NOTE]
> 부하 분산 또는 인바운드 NAT 규칙에 사용 되는 각 포트는 이러한 64000 포트에서 8 개 포트의 범위를 사용 하 여 SNAT에 적합 한 포트 수를 줄입니다. 부하 > 분산 또는 nat 규칙이 8의 동일한 범위에 있는 경우 추가 포트를 사용 하지 않습니다. 

[아웃 바운드 규칙](https://docs.microsoft.com/azure/load-balancer/outbound-rules) 및 부하 분산 규칙을 통해 이러한 SNAT 포트를 백 엔드 인스턴스에 배포 하 여 아웃 바운드 연결에 대 한 부하 분산 장치의 공용 ip를 공유할 수 있습니다.

아래 [시나리오 2](#scenario2) 가 구성 된 경우 각 백 엔드 인스턴스에 대 한 호스트는 아웃 바운드 연결의 일부인 패킷에 대해 SNAT를 수행 합니다. 백 엔드 인스턴스에서 아웃 바운드 연결에 대해 SNAT를 수행 하는 경우 호스트는 원본 IP를 프런트 엔드 Ip 중 하나로 다시 작성 합니다. 고유한 흐름을 유지 하기 위해 호스트는 각 아웃 바운드 패킷의 원본 포트를 백 엔드 인스턴스에 할당 된 SNAT 포트 중 하나로 다시 작성 합니다.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>여러 시나리오에 대 한 아웃 바운드 연결 동작
  * 공용 IP를 사용 하는 가상 컴퓨터.
  * 공용 IP를 사용 하지 않는 가상 컴퓨터.
  * 공용 IP가 없고 표준 부하 분산 장치를 사용 하지 않는 가상 컴퓨터.
        

 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> 시나리오 1: 공용 IP를 사용 하는 가상 컴퓨터


 | 연결 | 방법 | IP 프로토콜 |
 | ---------- | ------ | ------------ |
 | 공용 부하 분산 장치 또는 독립 실행형 | [SNAT (원본 네트워크 주소 변환)](#snat) </br> 사용되지 않습니다. | TCP (전송 제어 프로토콜) </br> UDP (사용자 데이터 그램 프로토콜) </br> ICMP (Internet Control Message Protocol) </br> ESP (보안 페이로드 캡슐화) |


 #### <a name="description"></a>Description


 Azure는 모든 아웃 바운드 흐름에 대해 인스턴스의 NIC IP 구성에 할당 된 공용 IP를 사용 합니다. 인스턴스에 있는 모든 삭제 포트를 사용할 수 있습니다. VM이 부하 분산 되었는지 여부는 중요 하지 않습니다. 이 시나리오는 다른 시나리오에 우선합니다. 


 VM에 할당된 공용 IP는 1:다가 아닌 1:1 관계이며 상태 비저장 1:1 NAT로 구현됩니다.


 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>시나리오 2: 공용 IP가 없고 표준 공용 Load Balancer 뒤에 있는 가상 컴퓨터


 | 연결 | 방법 | IP 프로토콜 |
 | ------------ | ------ | ------------ |
 | 공용 부하 분산 장치 | [SNAT](#snat)에 대 한 부하 분산 장치 프런트 엔드 ip 사용.| TCP </br> UDP |


 #### <a name="description"></a>Description


 부하 분산 장치 리소스는 아웃 바운드 규칙 또는 기본 SNAT를 사용 하는 부하 분산 규칙을 사용 하 여 구성 됩니다. 이 규칙은 백 엔드 풀을 사용 하 여 공용 IP 프런트 엔드 간에 링크를 만드는 데 사용 됩니다. 


 이 규칙 구성을 완료 하지 않으면 시나리오 3에 설명 된 대로 동작 합니다. 


 수신기를 사용 하는 규칙은 상태 프로브를 성공 하는 데 필요 하지 않습니다.


 VM이 아웃 바운드 흐름을 만들 때 Azure는 원본 IP 주소를 공용 부하 분산 장치 프런트 엔드의 공용 IP 주소로 변환 합니다. 이 변환은 [SNAT](#snat)를 통해 수행 됩니다. 


 부하 분산 장치 프런트 엔드 공용 IP 주소의 삭제 포트는 VM에서 발생 하는 개별 흐름을 구별 하는 데 사용 됩니다. SNAT은 아웃바운드 흐름이 생성될 때 동적으로 [삭제 포트를 미리 할당](#preallocatedports)합니다. 


 이 컨텍스트에서 SNAT에 사용되는 임시 포트를 SNAT 포트라고 부릅니다. [아웃 바운드 규칙](https://docs.microsoft.com/azure/load-balancer/outbound-rules) 은 명시적으로 구성 하는 것이 좋습니다. 부하 분산 규칙을 통해 기본 SNAT를 사용 하는 경우 [기본 snat 포트 할당 테이블](#snatporttable)에 설명 된 대로 snat 포트는 미리 할당 됩니다.


 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario3"></a>시나리오 3: 공용 IP가 없고 기본 Load Balancer 뒤에 있는 가상 컴퓨터


 | 연결 | 방법 | IP 프로토콜 |
 | ------------ | ------ | ------------ |
 |없음 </br> 기본 부하 분산 장치 | 인스턴스 수준 동적 IP 주소를 사용 하는 [SNAT](#snat)| TCP </br> UDP | 

 #### <a name="description"></a>Description


 VM이 아웃 바운드 흐름을 만들 때 Azure는 원본 IP 주소를 동적으로 할당 된 공용 원본 IP 주소로 변환 합니다. 이 공용 IP 주소는 **구성할** 수 없으며 예약할 수 없습니다. 이 주소는 구독의 공용 IP 리소스 제한에 대해 계산 되지 않습니다. 


 를 다시 배포 하는 경우 공용 IP 주소가 해제 되 고 새 공용 IP가 요청 됩니다. 


 * Virtual Machine
 * 가용성 집합
 * 가상 머신 크기 집합 


 허용 목록에 Ip를 추가 하는 경우에는이 시나리오를 사용 하지 마세요. 아웃 바운드 동작을 명시적으로 선언 하는 시나리오 1 또는 2를 사용 합니다. [Snat](#snat) 포트는 [기본 snat 포트 할당 테이블](#snatporttable)에 설명 된 대로 미리 할당 된 됩니다.


## <a name="exhausting-ports"></a><a name="scenarios"></a> 포트 소모

동일한 대상 IP 및 대상 포트에 대 한 모든 연결에서 SNAT 포트를 사용 합니다. 이 연결은 백 엔드 인스턴스 또는 **클라이언트** 에서 **서버로** 의 고유한 **트래픽 흐름** 을 유지 합니다. 이 프로세스를 통해 서버에 트래픽을 처리할 고유 포트를 제공 합니다. 이 프로세스가 없으면 클라이언트 컴퓨터는 패킷이 속한 흐름을 인식 하지 못합니다.

여러 브라우저가로 이동 한다고 상상해 보세요 https://www.microsoft.com .

* 대상 IP = 23.53.254.142
* 대상 포트 = 443
* 프로토콜 = TCP

반환 트래픽 (연결을 설정 하는 데 사용 되는 SNAT 포트)에 대해 서로 다른 대상 포트가 없으면 클라이언트는 다른 쿼리 결과를 구분 하는 방법을 사용할 수 없습니다.

아웃 바운드 연결이 버스트 될 수 있습니다. 백 엔드 인스턴스에 충분 한 포트를 할당할 수 있습니다. **연결을 다시** 사용 하도록 설정 하지 않으면 SNAT **포트 고갈** 위험이 증가 합니다.

포트 소모가 발생 하면 대상 IP에 대 한 새 아웃 바운드 연결이 실패 합니다. 포트를 사용할 수 있게 되 면 연결에 성공 합니다. 이러한 소모는 IP 주소의 64000 포트가 여러 백 엔드 인스턴스에 씬 확산 될 때 발생 합니다. SNAT 포트 소모를 완화 하는 방법에 대 한 지침은 [문제 해결 가이드](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)를 참조 하세요.  

TCP 연결의 경우 부하 분산 장치는 모든 대상 IP 및 포트에 대해 단일 SNAT 포트를 사용 합니다. 이 이러한 다용도 동일한 SNAT 포트를 사용 하 여 동일한 대상 IP에 여러 연결을 설정할 수 있습니다. 이 이러한 다용도 다른 대상 포트에 연결 되지 않은 경우에는 제한 됩니다.

UDP 연결의 경우 부하 분산 장치는 대상 포트에 관계 없이 대상 IP 당 하나의 SNAT 포트를 사용 하는 **포트 제한 원뿔형 NAT** 알고리즘을 사용 합니다. 

포트는 제한 없는 수의 연결에 다시 사용 됩니다. 포트는 대상 IP 또는 포트가 서로 다른 경우에만 다시 사용 됩니다.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> 기본 포트 할당

부하 분산 장치의 프런트 엔드 IP로 할당 된 각 공용 IP에는 백 엔드 풀 구성원에 대해 64000 SNAT 포트가 지정 됩니다. 백 엔드 풀 구성원과 포트를 공유할 수 없습니다. SNAT 포트의 범위는 단일 백 엔드 인스턴스에 의해서만 반환 패킷이 올바르게 라우팅되도록 하는 데만 사용할 수 있습니다. 

명시적 아웃 바운드 규칙을 사용 하 여 SNAT 포트 할당을 구성 하는 것이 좋습니다. 이 규칙은 각 백 엔드 인스턴스가 아웃 바운드 연결에 사용할 수 있는 SNAT 포트 수를 최대화 합니다. 

부하 분산 규칙을 통해 아웃 바운드 SNAT의 자동 할당을 사용 해야 하는 경우 할당 테이블에서 포트 할당을 정의 합니다.

다음 <a name="snatporttable"></a> 표에서는 백 엔드 풀 크기 계층에 대 한 SNAT 포트 사전 할당을 보여 줍니다.

| 풀 크기(VM 인스턴스) | IP 구성별로 미리 할당된 SNAT 포트 |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> 최대 크기가 10 인 백 엔드 풀이 있는 경우 명시적 아웃 바운드 규칙을 정의 하는 경우 각 인스턴스에 64000/10 = 6400 포트가 있을 수 있습니다. 위의 표에 따르면 자동 할당을 선택한 경우에만 1024이 있습니다.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> 아웃 바운드 규칙 및 Virtual Network NAT

Azure Load Balancer 아웃 바운드 규칙 및 Virtual Network NAT는 가상 네트워크에서 송신에 사용할 수 있는 옵션입니다.

아웃 바운드 규칙에 대 한 자세한 내용은 [아웃 바운드 규칙](outbound-rules.md)을 참조 하세요.

Azure Virtual Network NAT에 대 한 자세한 내용은 [azure VIRTUAL NETWORK nat 란?](../virtual-network/nat-overview.md)을 참조 하세요.

## <a name="constraints"></a>제약 조건

*   **TCP RST** 을 받거나 보낼 때 15 초 후에 포트가 해제 됩니다.
*   **Finack** 를 받거나 보내는 경우 240 초 후에 포트가 해제 됩니다.
*   새 패킷이 전송 되지 않고 연결이 유휴 상태 이면 4 ~ 120 분 후에 포트가 해제 됩니다.
  * 이 임계값은 아웃 바운드 규칙을 통해 구성할 수 있습니다.
*   각 IP 주소는 SNAT에 사용할 수 있는 64000 포트를 제공 합니다.
*   각 포트는 대상 IP 주소에 대 한 TCP 및 UDP 연결 모두에 사용할 수 있습니다.
  * UDP SNAT 포트는 대상 포트가 고유한 지 여부에 관계 없이 필요 합니다. 대상 IP에 대 한 모든 UDP 연결의 경우 하나의 UDP SNAT 포트가 사용 됩니다.
  * 대상 포트가 서로 다른 경우에는 동일한 대상 IP에 대 한 여러 연결에 TCP SNAT 포트를 사용할 수 있습니다.
*   지정 된 SNAT 포트에서 백 엔드 인스턴스가 실행 되 면 SNAT 고갈가 발생 합니다. 부하 분산 장치는 사용 되지 않는 SNAT 포트를 계속 사용할 수 있습니다. 백 엔드 인스턴스의 사용 된 SNAT 포트가 지정 된 SNAT 포트를 초과 하면 새로운 아웃 바운드 연결을 설정할 수 없습니다.

## <a name="next-steps"></a>다음 단계

*   [SNAT 고갈로 인 한 아웃 바운드 연결 오류 해결](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [SNAT 메트릭을 검토](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) 하 고이를 필터링, 분할 및 표시 하는 올바른 방법을 숙지 하세요.

