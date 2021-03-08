---
title: Azure RTOS의 보안 모듈 구성 및 사용자 지정
description: Azure RTOS의 보안 모듈을 구성 하 고 사용자 지정 하는 방법에 대해 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2021
ms.author: shhazam
ms.openlocfilehash: 524286fa7a923485d0085fb63f3ef9669db1a4d5
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449818"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-ga"></a>Azure RTOS GA에 대해 Defender-마이크로 에이전트 구성 및 사용자 지정

이 문서에서는 네트워크, 대역폭 및 메모리 요구 사항을 충족 하기 위해 Azure RTOS 장치에 대해 Defender-IoT-마이크로 에이전트를 구성 하는 방법을 설명 합니다.

디렉터리에서 확장명이 있는 대상 배포 파일을 선택 해야 합니다 `*.dist` `netxduo/addons/azure_iot/azure_iot_security_module/configs` .  

CMake 컴파일 환경을 사용 하는 경우 `IOT_SECURITY_MODULE_DIST_TARGET` 선택한 값에 대 한 명령줄 매개 변수를로 설정 해야 합니다. 예들 들어 `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`입니다.

IAR 또는 다른 비 CMake 컴파일 환경에서는 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` 알려진 포함 경로에 대 한 경로를 추가 해야 합니다. 예들 들어 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`입니다.

다음 파일을 사용 하 여 장치 동작을 구성 합니다.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/ \<target distribution> /asc_config. h**

CMake 컴파일 환경에서는 파일을 편집 하 여 기본 구성을 변경 해야 합니다 `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` . `set(ASC_XXX ON)` `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` 다른 모든 환경에 대해 다음의 ctoformat 또는 다음 파일을 사용 합니다. 예들 들어 `#define ASC_XXX`입니다.

각 구성의 기본 동작은 다음 표에 나와 있습니다. 

## <a name="general"></a>일반

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | defender-마이크로 에이전트 | 디바이스의 고유 식별자입니다.  |
| SECURITY_MODULE_VERSION_ (주) (부) (패치)  | 숫자 | 3.2.1 | 버전입니다. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | 숫자  | 3 | 실패 후에 Defender-마이크로 에이전트가 보안 메시지를 전송 하는 데 걸리는 시간입니다. (초) |
| ASC_SECURITY_MODULE_PENDING_TIME  | 숫자 | 300 | Defender-마이크로 에이전트 보류 시간 (초)입니다. 시간이 초과 되 면 상태가 일시 중단으로 변경 됩니다. |

## <a name="collection"></a>컬렉션

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | 숫자  | 30  | 수집기의 시작 컬렉션 간격 오프셋입니다. 시작 하는 동안 여러 장치에서 동시에 메시지를 보내지 않도록 하기 위해 시스템의 컬렉션에 값이 추가 됩니다.  |
| ASC_HIGH_PRIORITY_INTERVAL | 숫자 | 10 | 수집기의 높은 우선 순위 그룹 간격 (초)입니다. |
| ASC_MEDIUM_PRIORITY_INTERVAL | 숫자 | 30 | 수집기의 중간 우선 순위 그룹 간격 (초)입니다. |
| ASC_LOW_PRIORITY_INTERVAL | 숫자 | 145440  | 수집기의 낮은 우선 순위 그룹 간격 (초)입니다. |

#### <a name="collector-network-activity"></a>수집기 네트워크 활동

수집기 네트워크 활동 구성을 사용자 지정 하려면 다음을 사용 합니다.

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | 부울 | false | `TCP`네트워크 활동을 필터링 합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | 부울 | false | `UDP`네트워크 활동 이벤트를 필터링 합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | 부울 | false | `ICMP`네트워크 활동 이벤트를 필터링 합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | 부울 | true | 유니캐스트 들어오는 패킷만 캡처합니다. False로 설정 하면 브로드캐스트 및 멀티 캐스트도 모두 캡처합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | 부울  | false  | 수집기의 빈 이벤트를 보냅니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | 숫자 | 64 | 메모리에 저장할 최대 IPv4 네트워크 이벤트 수입니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | 숫자 | 64  | 메모리에 저장할 최대 IPv6 네트워크 이벤트 수입니다. |

### <a name="collectors"></a>To
| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | 부울 | 켜기 | 하트 비트 수집기를 사용 하도록 설정 합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | 부울 | 켜기 | 네트워크 활동 수집기를 사용 하도록 설정 합니다. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | 부울 | 켜기 | 시스템 정보 수집기를 사용 하도록 설정 합니다.  |

다른 구성 플래그는 고급 이며 지원 되지 않는 기능을 포함 합니다. 지원에 문의 하 여이를 변경 하거나 자세한 내용을 문의 하세요.
 
## <a name="supported-security-alerts-and-recommendations"></a>지원 되는 보안 경고 및 권장 사항

Azure RTOS 용 Defender-IoT-마이크로 에이전트는 특정 보안 경고 및 권장 사항을 지원 합니다. 서비스에 대 한 [관련 경고 및 권장 값을 검토 하 고 사용자 지정](concept-rtos-security-alerts-recommendations.md) 해야 합니다.

## <a name="log-analytics-optional"></a>Log Analytics (옵션)

Log Analytics를 사용 하도록 설정 하 고 구성 하 여 장치 이벤트와 활동을 조사할 수 있습니다. 자세한 내용은을 참조 하 여 설치 하는 방법 및 [IoT 서비스용 Defender 서비스와 Log Analytics](how-to-security-data-access.md#log-analytics) 사용을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

- Azure RTOS [보안 경고 및 권장 사항](concept-rtos-security-alerts-recommendations.md) 에 대 한 보안 모듈 검토 및 사용자 지정
- 필요에 따라 [Azure RTOS API에 대 한 보안 모듈](azure-rtos-security-module-api.md) 을 참조 하세요.
