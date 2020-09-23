---
title: Azure RTOS의 보안 모듈 구성 및 사용자 지정
description: Azure RTOS의 보안 모듈을 구성 하 고 사용자 지정 하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9106e00fe2146978f97b480e3afd3b7ed58c9130
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937774"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Azure RTOS (미리 보기)에 대 한 보안 모듈 구성 및 사용자 지정

다음 파일을 사용 하 여 장치 동작을 구성 합니다.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/inc/asc_port. h

 각 구성의 기본 동작은 다음 표에 나와 있습니다. 

### <a name="general"></a>일반

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | 문자열 | --- | 디바이스의 고유 식별자  |
| ASC_SECURITY_MODULE_PENDING_TIME  | 번호 | 300 | 보안 모듈 보류 시간 (초)입니다. 시간이 일시 중단으로 상태 변경을 초과 하는 경우 |

#### <a name="collection"></a>컬렉션

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | 번호 | 10 | 수집기 높은 우선 순위 그룹 간격 (초)입니다. |
| ASC_MEDIUM_PRIORITY_INTERVAL | 번호 | 30 | 수집기 중간 우선 순위 그룹 간격 (초)입니다. |
| ASC_LOW_PRIORITY_INTERVAL | 번호 | 145440  | 수집기 낮은 우선 순위 그룹 간격 (초)입니다. |

#### <a name="collector-network-activity"></a>수집기 네트워크 활동

수집기 네트워크 활동 구성을 사용자 지정 하려면 다음을 사용 합니다.

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | 부울 | false | `TCP`네트워크 작업 필터링 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | 부울 | false | `UDP`네트워크 활동 이벤트 필터링 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | 부울 | false | `ICMP`네트워크 활동 이벤트 필터링 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | 부울 | true | 유니캐스트 들어오는 패킷만 캡처, false 캡처로 설정 하면 브로드캐스트 및 멀티 캐스트 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | 번호 | 64 | 메모리에 저장할 최대 IPv4 네트워크 이벤트 수 |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | 번호 | 64  | 메모리에 저장할 IPv6 네트워크 이벤트의 최대 수 |


## <a name="compile-flags"></a>컴파일 플래그
컴파일 플래그를 사용 하면 미리 정의 된 구성을 재정의할 수 있습니다.

### <a name="collectors"></a>To
| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| collector_heartbeat_enabled | 부울 | 켜기 | 하트 비트 수집기 사용 |
| collector_network_activity_enabled | 부울 | 켜기 | 네트워크 활동 수집기 사용 |
| collector_system_information_enabled | 부울 | 켜기 | 시스템 정보 수집기 사용 |

## <a name="supported-security-alerts-and-recommendations"></a>지원 되는 보안 경고 및 권장 사항

Azure RTOS의 보안 모듈은 특정 보안 경고 및 권장 사항을 지원 합니다. 서비스에 대 한 [관련 경고 및 권장 값을 검토 하 고 사용자 지정](concept-rtos-security-alerts-recommendations.md) 해야 합니다.

## <a name="log-analytics-optional"></a>Log Analytics (옵션)

선택 사항 이지만 필수는 아니지만 Log Analytics을 사용 하도록 설정 하 고 구성 하는 것은 장치 이벤트와 활동을 추가로 조사 하려는 경우에 유용할 수 있습니다. 자세한 내용은 [IoT 서비스용 Defender](how-to-security-data-access.md#log-analytics) 를 사용 하 여 Log Analytics를 설정 및 사용 하는 방법을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

- Azure RTOS [보안 경고 및 권장 사항](concept-rtos-security-alerts-recommendations.md) 에 대 한 보안 모듈 검토 및 사용자 지정
- 필요에 따라 [Azure RTOS API에 대 한 보안 모듈](azure-rtos-security-module-api.md) 을 참조 하세요.

