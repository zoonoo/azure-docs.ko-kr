---
title: Azure DDoS Protection Standard에 대 한 DDoS protection 원격 분석 보기 및 구성
description: Azure DDoS Protection Standard에 대해 DDoS protection 원격 분석을 보고 구성 하는 방법에 대해 알아봅니다.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0be184921ff0bd6b98dd2975acb4e0d5c8b26ba0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716196"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS 보호 원격 분석 보기 및 구성

Azure DDoS Protection 표준은 DDoS 공격 분석을 통해 자세한 공격 인사이트와 시각화를 제공합니다. DDoS 공격으로부터 자신의 가상 네트워크를 보호하는 고객은 공격 완화 보고서 및 완화 흐름 로그를 통해 공격 트래픽 및 공격을 완화하는 데 수행된 작업에 대해 자세히 파악할 수 있습니다. 다양 한 원격 분석은 DDoS 공격 기간 동안 상세 메트릭을 포함 하 여 Azure Monitor를 통해 노출 됩니다. DDoS Protection에서 노출하는 Azure Monitor 메트릭에 대한 경고를 구성할 수 있습니다. 로깅은 Azure Monitor 진단 인터페이스를 통한 고급 분석을 위해 [Azure 센티널](../sentinel/connect-azure-ddos-protection.md), Splunk (azure Event Hubs), OMS Log Analytics 및 Azure Storage와 추가로 통합 될 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * DDoS protection 원격 분석 보기
> * DDoS 완화 정책 보기
> * DDoS protection 원격 분석 유효성 검사 및 테스트

### <a name="metrics"></a>메트릭

> [!NOTE]
> **집계** 에 대 한 여러 옵션이 Azure Portal에 표시 되지만 아래 표에 나열 된 집계 유형만 각 메트릭에 대해 지원 됩니다. 이 혼란을 위해 죄송 합니다 .이 문제를 해결 하기 위해 노력 하 고 있습니다.

Azure DDoS Protection Standard에 대해 다음 [메트릭을](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) 사용할 수 있습니다. 이러한 메트릭은 진단 설정을 통해 내보낼 수도 있습니다 ( [DDoS 진단 로깅 보기 및 구성](diagnostic-logging.md)참조).


| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 형식 | Description |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | 인바운드 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대 | 인바운드 바이트가 삭제된 DDoS| 
| BytesForwardedDDoS | 인바운드 바이트가 전달된 DDoS | 초당 바이트 수 | 최대 | 인바운드 바이트가 전달된 DDoS |
| BytesInDDoS | 인바운드 바이트 DDoS | 초당 바이트 수 | 최대 | 인바운드 바이트 DDoS |
| DDoSTriggerSYNPackets | DDoS 완화를 트리거하는 인바운드 SYN 패킷 | 초당 개수 | 최대 | DDoS 완화를 트리거하는 인바운드 SYN 패킷 |
| DDoSTriggerTCPPackets | DDoS 완화를 트리거하는 인바운드 TCP 패킷 | 초당 개수 | 최대 | DDoS 완화를 트리거하는 인바운드 TCP 패킷 |
| DDoSTriggerUDPPackets | DDoS 완화를 트리거하는 인바운드 UDP 패킷 | 초당 개수 | 최대 | DDoS 완화를 트리거하는 인바운드 UDP 패킷 |
| IfUnderDDoSAttack | DDoS 공격 진행 여부 | 개수 | 최대 | DDoS 공격 진행 여부 |
| PacketsDroppedDDoS | 인바운드 패킷이 삭제된 DDoS | 초당 개수 | 최대 | 인바운드 패킷이 삭제된 DDoS |
| PacketsForwardedDDoS | 인바운드 패킷이 전달된 DDoS | 초당 개수 | 최대 | 인바운드 패킷이 전달된 DDoS |
| PacketsInDDoS | 인바운드 패킷 DDoS | 초당 개수 | 최대 | 인바운드 패킷 DDoS |
| TCPBytesDroppedDDoS | 인바운드 TCP 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대 | 인바운드 TCP 바이트가 삭제된 DDoS |
| TCPBytesForwardedDDoS | 인바운드 TCP 바이트가 전달된 DDoS | 초당 바이트 수 | 최대 | 인바운드 TCP 바이트가 전달된 DDoS |
| TCPBytesInDDoS | 인바운드 TCP 바이트 DDoS | 초당 바이트 수 | 최대 | 인바운드 TCP 바이트 DDoS |
| TCPPacketsDroppedDDoS | 인바운드 TCP 패킷이 삭제된 DDoS | 초당 개수 | 최대 | 인바운드 TCP 패킷이 삭제된 DDoS |
| TCPPacketsForwardedDDoS | 인바운드 TCP 패킷이 전달된 DDoS | 초당 개수 | 최대 | 인바운드 TCP 패킷이 전달된 DDoS |
| TCPPacketsInDDoS | 인바운드 TCP 패킷 DDoS | 초당 개수 | 최대 | 인바운드 TCP 패킷 DDoS |
| UDPBytesDroppedDDoS | 인바운드 UDP 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대 | 인바운드 UDP 바이트가 삭제된 DDoS |
| UDPBytesForwardedDDoS | 인바운드 UDP 바이트가 전달된 DDoS | 초당 바이트 수 | 최대 | 인바운드 UDP 바이트가 전달된 DDoS |
| UDPBytesInDDoS | 인바운드 UDP 바이트 DDoS | 초당 바이트 수 | 최대 | 인바운드 UDP 바이트 DDoS |
| UDPPacketsDroppedDDoS | 인바운드 UDP 패킷이 삭제된 DDoS | 초당 개수 | 최대 | 인바운드 UDP 패킷이 삭제된 DDoS |
| UDPPacketsForwardedDDoS | 인바운드 UDP 패킷이 전달된 DDoS | 초당 개수 | 최대 | 인바운드 UDP 패킷이 전달된 DDoS |
| UDPPacketsInDDoS | 인바운드 UDP 패킷 DDoS | 초당 개수 | 최대 | 인바운드 UDP 패킷 DDoS |

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- 이 자습서의 단계를 완료 하려면 먼저 [Azure DDoS standard 보호 계획](manage-ddos-protection.md) 을 만들고 가상 네트워크에서 DDoS Protection standard를 사용 하도록 설정 해야 합니다.
- DDoS는 가상 네트워크 내의 리소스에 할당된 공용 IP 주소를 모니터링합니다. 가상 네트워크에 공용 IP 주소가 있는 리소스가 없으면 먼저 공용 IP 주소를 사용하여 리소스를 만들어야 합니다. Azure App Service 환경을 제외 하 고 [azure 서비스](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (가상 네트워크에 있는 Azure 부하 분산 장치를 포함 하는 Azure 부하 분산 장치)에 나열 된 리소스 관리자 (클래식이 아님)를 통해 배포 된 모든 리소스의 공용 IP 주소를 모니터링할 수 있습니다. 이 자습서를 계속 진행하려면 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 가상 머신을 빠르게 만들면 됩니다.  

## <a name="view-ddos-protection-telemetry"></a>DDoS protection 원격 분석 보기

공격에 대한 원격 분석이 Azure Monitor를 통해 실시간으로 제공됩니다. 원격 분석은 공용 IP 주소를 완화 하는 경우에만 사용할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 DDoS Protection 요금제로 이동 합니다.
2. **모니터링** 아래에서 **메트릭** 을 선택합니다.
3. **범위** 를 선택합니다. 로깅할 공용 IP 주소를 포함 하는 **구독** 을 선택 하 고 **리소스 종류** 에 대 한 **공용 ip 주소** 를 선택한 다음 메트릭을 로깅할 특정 공용 Ip 주소를 선택 하 고 **적용** 을 선택 합니다.
4. **집계** 유형을 **Max** 로 선택 합니다.

메트릭 이름은 다양한 패킷 유형과 바이트 및 패킷을 나타내며, 각 메트릭에서 태그 이름의 기본 구조는 다음과 같습니다.

- **Dropped 태그 이름**(예: **Inbound Packets Dropped DDoS**): DDoS 보호 시스템에서 삭제/스크럽한 패킷의 수입니다.
- **Forwarded 태그 이름**(예: **Inbound Packets Forwarded DDoS**): DDoS 시스템에서 대상 VIP로 전달한 패킷의 수입니다(필터링되지 않은 트래픽).
- **태그 이름 없음**(예: **Inbound Packets DDoS**): 스크럽 시스템으로 들어온 패킷의 총 수입니다(삭제 및 전달된 패킷의 합계를 나타냄).

## <a name="view-ddos-mitigation-policies"></a>DDoS 완화 정책 보기

DDoS 보호 표준은 DDoS를 사용하도록 설정된 가상 네트워크에서 보호되는 리소스의 각 공용 IP 주소에 대해 자동 조정된 세 가지 완화 정책(TCP SYN, TCP 및 UDP)을 적용합니다. 다음 그림에 표시 된 것 처럼 DDoS 완화 및 **인바운드 UDP 패킷을** 트리거하여 **집계** 유형을 포함 하는 DDoS 완화 메트릭을 ' 최대 '로 트리거할 **인바운드 TCP 패킷을** 선택 하 여 정책 임계값을 볼 수 있습니다.

![완화 정책 보기](./media/manage-ddos-protection/view-mitigation-policies.png)

정책 임계값은 Azure 기계 학습 기반의 네트워크 트래픽 프로파일링을 통해 자동으로 구성됩니다. 정책 임계값을 위반한 경우에만 공격을 받고 있는 IP 주소에 대해 DDoS 완화가 발생합니다.

## <a name="validate-and-test"></a>유효성 검사 및 테스트

DDoS 공격을 시뮬레이션 하 여 DDoS protection 원격 분석의 유효성을 검사 하려면 [DDoS 검색 유효성 검사](test-through-simulations.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

- DDoS 보호 메트릭에 대한 경고 구성
- DDoS protection 원격 분석 보기
- DDoS 완화 정책 보기
- DDoS protection 원격 분석 유효성 검사 및 테스트

공격 완화 보고서 및 흐름 로그를 구성 하는 방법을 알아보려면 다음 자습서를 계속 진행 합니다.

> [!div class="nextstepaction"]
> [DDoS 진단 로깅 보기 및 구성](diagnostic-logging.md)