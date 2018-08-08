---
title: Load Balancer 상태 프로브를 사용하여 서비스 보호 | Microsoft Docs
description: 상태 프로브를 사용하여 Load Balancer 뒤의 인스턴스를 모니터링하는 방법을 알아봅니다.
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
ms.date: 07/31/2018
ms.author: kumud
ms.openlocfilehash: 7366273e30132daf7dc5ea15072c574180d1bc8b
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397290"
---
# <a name="load-balancer-health-probes"></a>Load Balancer 상태 프로브

Azure Load Balancer는 상태 프로브를 사용하여 새 흐름을 받을 백 엔드 풀 인스턴스를 결정합니다. 상태 프로브를 사용하여 백 엔드 인스턴스에서 응용 프로그램 오류를 검색할 수 있습니다. 또한 상태 프로브에 대한 사용자 지정 응답을 생성하고, 흐름 제어에 상태 프로브를 사용하고, 새 흐름을 백 엔드 인스턴스로 계속 보낼지, 아니면 보내지 않도록 중지할지 여부에 대한 신호를 Load Balancer에 보낼 수 있습니다. 이는 부하 또는 계획된 가동 중지를 관리하는 데 사용할 수 있습니다.

상태 프로브가 실패하면 Load Balancer에서 각 비정상 인스턴스에 대한 새 연결 보내기를 중지합니다. 새 흐름 및 기존 흐름의 동작은 흐름이 TCP 또는 UDP인지 여부와 사용 중인 Load Balancer SKU에 따라 달라집니다.  자세한 내용은 [프로브 다운 동작](#probedown)을 검토하세요.

> [!IMPORTANT]
> Load Balancer 상태 프로브는 168.63.129.16 IP 주소에서 시작되며, 프로브에서 인스턴스를 표시하기 위해 차단되지 않아야 합니다.  자세한 내용은 [원본 IP 주소 프로브](#probesource)를 검토하세요.

## <a name="health-probe-types"></a>상태 프로브 유형

상태 프로브는 실제 서비스가 제공되는 포트를 포함하여 백 엔드 인스턴스의 모든 포트를 관찰할 수 있습니다. 상태 프로브는 TCP 수신기 또는 HTTP 엔드포인트를 지원합니다. 

UDP 부하 분산의 경우 TCP 또는 HTTP 상태 프로브를 사용하여 백 엔드 인스턴스에 대한 사용자 지정 상태 프로브 신호를 생성해야 합니다.

[표준 Load Balancer](load-balancer-standard-overview.md)와 함께 [HA 포트 부하 분산 규칙](load-balancer-ha-ports-overview.md)을 사용하면, 모든 포트의 부하가 분산되고 단일 상태 프로브 응답에는 전체 인스턴스의 상태가 반영되어야 합니다.  

시나리오에서 연속 오류로 이어질 수 있으므로 VNet의 다른 인스턴스로 상태 프로브를 받는 인스턴스를 통해 상태 프로브를 NAT 또는 프록시하지 않아야 합니다.

상태 프로브 실패를 테스트하거나 개별 인스턴스를 표시하려는 경우 보안 그룹을 사용하여 상태 프로브(대상 또는 [원본](#probesource))를 명시적으로 차단할 수 있습니다.

### <a name="tcp-probe"></a>TCP 프로브

TCP 프로브는 정의된 포트를 통해 3방향 개방형 TCP 핸드셰이크를 수행하여 연결을 시작합니다.  그런 다음, 4방향 폐쇄형 TCP 핸드 셰이크가 이어집니다.

최소 프로브 간격은 5초이고, 비정상 응답의 최소 수는 2입니다.  총 지속 시간은 120초를 초과할 수 없습니다.

TCP 프로브가 실패하는 경우는 다음과 같습니다.
* 인스턴스의 TCP 수신기가 제한 시간 동안 전혀 응답하지 않습니다.  프로브는 프로브 다운을 표시하기 전에 응답하지 않도록 구성된 실패한 프로브 요청의 수에 따라 표시됩니다.
* 프로브가 인스턴스에서 TCP 재설정을 받습니다.

### <a name="http-probe"></a>HTTP 프로브

HTTP 프로브는 TCP 연결을 설정하고, 지정된 경로가 포함된 HTTP GET을 실행합니다. HTTP 프로브는 HTTP GET에 대한 상대 경로를 지원합니다. 인스턴스에서 제한 시간 내에 200 HTTP 상태로 응답하면 상태 프로브가 표시됩니다.  HTTP 상태 프로브는 기본적으로 구성된 상태 프로브 포트를 15초마다 확인하려고 합니다. 최소 프로브 간격은 5초입니다. 총 지속 시간은 120초를 초과할 수 없습니다. 


또한 HTTP 프로브는 부하 분산 장치 순환에서 인스턴스를 제거하는 사용자 고유의 논리를 구현하려는 경우에도 유용할 수 있습니다. 예를 들어 CPU 백분율이 90%를 초과하고 200 이외의 HTTP 상태를 반환하는 경우 인스턴스를 제거하도록 결정할 수 있습니다. 

Cloud Services를 사용하고 w3wp.exe를 사용하는 웹 역할이 있는 경우 웹 사이트의 자동 모니터링도 수행할 수 있습니다. 웹 사이트 코드에서 실패하면 부하 분산 장치 프로브로 200이 아닌 상태를 반환합니다.  HTTP 프로브는 기본 게스트 에이전트 프로브를 재정의합니다. 

HTTP 프로브가 실패하는 경우는 다음과 같습니다.
* HTTP 프로브 엔드포인트에서 200 이외의 HTTP 응답 코드(예: 403, 404 또는 500)를 반환합니다. 이 경우 상태 프로브 다운이 즉시 표시됩니다. 
* HTTP 프로브 엔드포인트에서 31초의 제한 시간 동안 전혀 응답하지 않습니다. 설정된 시간 제한 값에 따라 프로브가 실행되지 않음으로 표시되기 전에 즉, SuccessFailCount 프로브가 전송되기 전에 여러 프로브 요청이 무응답 상태가 될 수 있습니다.
* HTTP 프로브 엔드포인트에서 TCP 재설정을 통해 연결을 닫습니다.

### <a name="guest-agent-probe-classic-only"></a>게스트 에이전트 프로브(클래식만)

클라우드 서비스 역할(작업자 역할 및 웹 역할)은 기본적으로 게스트 에이전트를 사용하여 프로브를 모니터링합니다.   이 옵션은 최후의 수단으로 고려해야 합니다.  상태 프로브는 항상 TCP 또는 HTTP 프로브를 사용하여 명시적으로 정의해야 합니다. 게스트 에이전트 프로브는 대부분의 응용 프로그램 시나리오에 대해 명시적으로 정의된 프로브만큼 효과적이지 않습니다.  

게스트 에이전트 프로브는 VM 내부의 게스트 에이전트를 검사합니다. 그런 다음 인스턴스가 준비 상태인 경우에만 수신 대기하며 HTTP 200 OK로 응답합니다. 다른 상태는 사용 중, 재생 중 또는 중지 중입니다.

자세한 내용은 [상태 프로브에 대한 서비스 정의 파일(csdef) 구성](https://msdn.microsoft.com/library/azure/ee758710.aspx) 또는 [클라우드 서비스를 위한 공용 부하 분산 장치 만들기 시작](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)을 참조하세요.

게스트 에이전트가 HTTP 200 OK로 응답하는 데 실패하면 부하 분산 장치가 인스턴스를 응답하지 않음으로 표시합니다. 그런 다음, 해당 인스턴스에 흐름을 보내지 않도록 중지합니다. 부하 분산 장치는 인스턴스를 계속 검사합니다. 

게스트 에이전트에서 200 HTTP로 응답하면 부하 분산 장치는 새 흐름을 해당 인스턴스에 다시 보냅니다.

웹 역할을 사용하는 경우 웹 사이트 코드는 일반적으로 w3wp.exe에서 실행되며 이는 Azure 패브릭 또는 게스트 에이전트에서 모니터링하지 않습니다. w3wp.exe(예: HTTP 500 응답)의 실패는 게스트 에이전트에 보고되지 않습니다. 결과적으로 부하 분산 장치는 해당 인스턴스를 순환에서 제거하지 않습니다.

## <a name="probe-health"></a>상태 프로브

TCP 및 HTTP 상태 프로브가 정상으로 간주되고 역할 인스턴스를 정상 상태로 표시하는 경우는 다음과 같습니다.

* VM이 처음 부팅될 때 상태 프로브가 성공합니다.
* 앞에서 설명한 SuccessFailCount 숫자가 역할 인스턴스를 정상 상태로 표시하는 데 필요한 성공한 프로브 값을 정의하는 경우. 역할 인스턴스가 제거되면 성공한 연속 프로브 수가 역할 인스턴스를 실행 중으로 표시하는 SuccessFailCount 값보다 크거나 같아야 합니다.

> [!NOTE]
> 역할 인스턴스의 상태가 변동되는 경우 부하 분산 장치는 역할 인스턴스를 다시 정상 상태로 전환하기 전에 좀 더 오래 기다립니다. 이러한 추가 대기 시간은 의도적인 정책으로, 사용자 및 인프라를 보호합니다.

## <a name="probe-count-and-timeout"></a>프로브 수 및 시간 제한

프로브 동작은 다음 사항에 따라 달라집니다.

* 인스턴스가 가동 상태로 표시될 수 있는 성공한 프로브의 수
* 인스턴스가 가동 중지 상태로 표시되는 실패한 프로브의 수

SuccessFailCount에 설정된 시간 제한 및 빈도 값에 따라 인스턴스가 실행 중인지 여부가 확인됩니다. Azure 포털에서 시간 제한은 빈도 값의 두 배로 설정됩니다.

부하 분산 규칙에는 각각의 백 엔드 풀을 정의한 단일 상태 프로브가 있습니다.

## <a name="probedown"></a>프로브 다운 동작

### <a name="tcp-connections"></a>TCP 연결

새 TCP 연결은 정상 상태이고 게스트 OS 및 응용 프로그램에서 새 흐름을 수용할 수 있는 백 엔드 인스턴스에 성공합니다.

백 엔드 인스턴스의 상태 프로브가 실패하더라도 이 백 엔드 인스턴스에 설정된 TCP 연결은 계속 유지됩니다.

백 엔드 풀의 모든 인스턴스에 대한 모든 프로브가 실패하면 새 흐름을 백 엔드 풀로 보내지 않습니다. 표준 Load Balancer는 설정된 TCP 흐름이 계속되도록 허용합니다.  기본 Load Balancer는 백 엔드 풀에 대한 기존의 모든 TCP 흐름을 종료합니다.
 
흐름이 항상 클라이언트와 VM의 게스트 OS 사이에 있어 흐름을 받을 정상적인 백 엔드 인스턴스가 없으므로 모든 프로브가 다운된 풀로 인해 프런트 엔드에서 TCP 연결 열기 시도에 응답하지 않게 됩니다.

### <a name="udp-datagrams"></a>UDP 데이터그램

UDP 데이터 그램은 정상적인 백 엔드 인스턴스에 전달됩니다.

UDP는 비연결형이며 UDP에 대해 추적된 흐름 상태가 없습니다. 백 엔드 인스턴스의 상태 프로브가 실패하면 기존 UDP 흐름이 백 엔드 풀에서 정상적인 다른 인스턴스로 이동할 수 있습니다.

백 엔드 풀의 모든 인스턴스에 대한 모든 프로브가 실패하면 기본 및 표준 Load Balancer에 대한 기존 UDP 흐름이 종료됩니다.


## <a name="probesource"></a>원본 IP 주소 프로브

모든 Load Balancer 상태 프로브는 해당 원본으로 168.63.129.16 IP 주소에서 시작됩니다.  Azure의 Virtual Network에 사용자 고유의 IP 주소를 가져오는 경우 이 상태 프로브 원본 IP 주소는 Microsoft에서 글로벌하게 예약되어 있으므로 고유하게 보장됩니다.  이 주소는 모든 지역에서 동일하며 변경되지 않습니다. 내부 Azure 플랫폼만 이 IP 주소에서 패킷을 소싱할 수 있으므로 이를 보안 위험으로 간주하면 안 됩니다. 

Load Balancer의 상태 프로브에서 인스턴스를 표시하려면 모든 Azure [보안 그룹](../virtual-network/security-overview.md) 및 로컬 방화벽 정책에서 이 IP 주소를 **허용해야 합니다**.

방화벽 정책에서 이 IP 주소를 허용하지 않으면 인스턴스에 연결할 수 없으므로 상태 프로브가 실패하게 됩니다.  차례로 상태 프로브 실패로 인해 Load Balancer에서 인스턴스를 표시합니다.  이로 인해 부하 분산 서비스가 실패할 수 있습니다. 

또한 168.63.129.16이 포함된 Microsoft 소유의 IP 주소 범위를 사용하여 VNet을 구성하면 안됩니다.  이렇게 하면 상태 프로브의 IP 주소와 충돌합니다.

VM에 여러 인터페이스가 있는 경우 받은 인터페이스의 프로브에 응답하도록 보장해야 합니다.  이를 위해 VM에서 인터페이스별로 이 주소에 대해 고유한 원본 NAT를 수행해야 할 수 있습니다.

## <a name="monitoring"></a>모니터링

모든 [표준 Load Balancer](load-balancer-standard-overview.md)는 Azure Monitor를 통해 상태 프로브 상태를 인스턴스별 다차원 메트릭으로 노출합니다.

기본 Load Balancer는 Log Analytics를 통해 백 엔드 풀별 상태 프로브 상태를 노출합니다.  이는 공용 기본 Load Balancer에만 사용할 수 있으며, 내부 기본 Load Balancer에는 사용할 수 없습니다.  [Log Analytics](load-balancer-monitor-log.md)를 사용하여 공용 부하 분산 장치의 프로브 상태 및 프로브 수를 확인할 수 있습니다. Power BI 또는 Azure Operational Insights에서 로깅을 사용하여 부하 분산 장치 상태에 대한 통계를 제공할 수 있습니다.


## <a name="limitations"></a>제한 사항

-  HTTP 상태 프로브는 TLS(HTTPS)를 지원하지 않습니다.  443 포트 대신 TCP 프로브를 사용하세요.

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 Resource Manager에서 공용 부하 분산 장치 만들기 시작](load-balancer-get-started-internet-arm-ps.md)
- [상태 프로브용 REST API](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

