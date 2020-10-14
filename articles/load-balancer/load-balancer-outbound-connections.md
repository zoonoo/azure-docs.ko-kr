---
title: 아웃 바운드 프록시 Azure Load Balancer
description: 아웃 바운드 인터넷 연결을 위해 Azure Load Balancer를 프록시로 사용 하는 방법을 설명 합니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 422f8106ac52c85f0680d54e420d0f1b4d326910
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017695"
---
# <a name="outbound-proxy-azure-load-balancer"></a>아웃 바운드 프록시 Azure Load Balancer

Azure 부하 분산 장치는 아웃 바운드 인터넷 연결에 대 한 프록시로 사용할 수 있습니다. 부하 분산 장치는 백 엔드 인스턴스에 대 한 아웃 바운드 연결을 제공 합니다. 

이 구성에서는 **SNAT (원본 네트워크 주소 변환)** 을 사용 합니다. SNAT는 백 엔드의 IP 주소를 부하 분산 장치의 공용 IP 주소로 다시 작성 합니다. 

SNAT 백 엔드 인스턴스의 **IP** 를 가장 하는 것을 가능 하 게 합니다. 이렇게 하면 외부 소스에서 백 엔드 인스턴스에 대 한 직접 주소를 갖지 못하게 됩니다. 

백 엔드 인스턴스 사이에 IP 주소를 공유 하면 고정 공용 ip의 비용이 줄어들고 알려진 공용 ip의 트래픽이 포함 된 IP 허용 목록을 간소화 하는 등의 시나리오를 지원 합니다. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> 리소스 간 포트 공유

부하 분산 장치의 백 엔드 리소스에 ILPIP (인스턴스 수준 공용 IP) 주소가 없는 경우 공용 부하 분산 장치의 프런트 엔드 IP를 통해 아웃 바운드 연결을 설정 합니다.

포트는 고유한 흐름을 유지 하는 데 사용 되는 고유 식별자를 생성 하는 데 사용 됩니다. 인터넷은 5 튜플을 사용 하 여 이러한 차이를 제공 합니다.

5 개 튜플은 다음과 같이 구성 됩니다.

* 대상 IP
* 대상 포트
* 원본 IP
* 이러한 차이를 제공 하는 원본 포트 및 프로토콜입니다.

인바운드 연결에 포트를 사용 하는 경우 해당 포트의 인바운드 연결 요청에 대 한 **수신기** 를 포함 하 고 아웃 바운드 연결에 사용할 수 없습니다. 

아웃 바운드 연결을 설정 하려면 사용 **후 삭제 포트** 를 사용 하 여 통신 하 고 별도의 트래픽 흐름을 유지 관리할 수 있는 포트를 대상에 제공 해야 합니다. 

각 IP 주소에는 65535 개의 포트가 있습니다. 첫 번째 1024 포트는 **시스템 포트로**예약 됩니다. TCP 및 UDP의 인바운드 또는 아웃 바운드 연결에 각 포트를 사용할 수 있습니다. 

나머지 포트 중 Azure는 64000을 사용 하 여 사용 **후 삭제 포트**를 제공 합니다. IP 주소를 프런트 엔드 IP 구성으로 추가 하는 경우 SNAT에 이러한 사용 후 삭제 포트를 사용할 수 있습니다.

아웃 바운드 규칙을 통해 이러한 SNAT 포트는 아웃 바운드 연결에 대 한 부하 분산 장치의 공용 IP를 공유할 수 있도록 백엔드 인스턴스에 배포할 수 있습니다.

각 백 엔드 인스턴스에 대 한 호스트의 네트워킹은 아웃 바운드 연결의 일부인 패킷에 SNAT를 수행 합니다. 호스트는 공용 IP 중 하나로 원본 IP를 다시 작성 합니다. 호스트는 각 아웃 바운드 패킷의 원본 포트를 SNAT 포트 중 하나로 다시 작성 합니다.

## <a name="exhausting-ports"></a><a name="scenarios"></a> 포트 소모

동일한 대상 IP 및 대상 포트에 대 한 모든 연결에서 SNAT 포트를 사용 합니다. 이 연결은 백 엔드 인스턴스 또는 **클라이언트** 에서 **서버로**의 고유한 **트래픽 흐름** 을 유지 합니다. 이 프로세스를 통해 서버에 트래픽을 처리할 고유 포트를 제공 합니다. 이 프로세스가 없으면 클라이언트 컴퓨터는 패킷이 속한 흐름을 인식 하지 못합니다.

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

## <a name="port-allocation"></a><a name="preallocatedports"></a> 포트 할당

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
> 최대 크기가 6 인 백 엔드 풀이 있는 경우 명시적 아웃 바운드 규칙을 정의 하는 경우 각 인스턴스에 64000/10 = 6400 포트가 있을 수 있습니다. 위의 표에 따르면 자동 할당을 선택한 경우에만 1024이 있습니다.

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

