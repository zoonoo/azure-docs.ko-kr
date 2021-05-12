---
title: 이벤트 집계(미리 보기)
description: Defender for IoT 보안 에이전트는 로컬 디바이스에서 데이터 및 시스템 이벤트를 수집하고 처리 및 분석을 위해 데이터를 Azure 클라우드에 보냅니다.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779327"
---
# <a name="event-aggregation-preview"></a>이벤트 집계(미리 보기)

Defender for IoT 보안 에이전트는 로컬 디바이스에서 데이터 및 시스템 이벤트를 수집하고 처리 및 분석을 위해 데이터를 Azure 클라우드에 보냅니다. Defender for IoT 마이크로 에이전트는 새 프로세스 및 모든 새 연결 이벤트를 포함하여 다양한 유형의 디바이스 이벤트를 수집합니다. 새 프로세스와 새 연결 이벤트는 디바이스에서 1초 내에 여러 번 발생할 수도 있습니다. 이 기능은 포괄적인 보안을 위해 중요하지만, 보안 에이전트에서 전송하는 메시지 수가 빠르게 충족되거나 IoT Hub 할당량 및 비용 한도를 초과할 수 있습니다. 그럼에도 불구하고 해당 이벤트에는 디바이스를 보호하는 데 매우 중요한 보안 정보가 포함되어 있습니다. 

디바이스를 보호하면서 불필요한 할당량 및 비용을 줄이기 위해 Defender for IoT 에이전트는 해당 유형의 이벤트를 집계합니다. 

- ProcessCreate(Linux만 해당) 

- ConnectionCreate(Azure RTOS만 해당) 

## <a name="how-does-event-aggregation-work"></a>이벤트 집계는 어떻게 작동하나요? 

Defender for IoT 에이전트는 간격 또는 기간에 대한 이벤트를 집계합니다. 간격 지나면 에이전트는 추가 분석을 위해 집계된 이벤트를 Azure 클라우드로 보냅니다. 집계된 이벤트는 Azure 클라우드로 전송될 때까지 메모리에 저장됩니다. 

에이전트의 메모리 공간을 줄이기 위해 에이전트는 이미 메모리에 유지 중인 이벤트와 동일한 이벤트를 수집할 때 에이전트는 이 특정 이벤트의 적중 횟수를 늘립니다. 집계 기간이 지나면 에이전트는 발생한 이벤트 유형 각각에 대한 적중 횟수를 보냅니다. 이벤트 집계는 수집된 각 이벤트 유형의 적중 횟수에 대한 집계일 뿐입니다. 

## <a name="process-events"></a>이벤트 처리 

프로세스 이벤트는 현재 Linux 운영 체제에서만 지원됩니다. 

‘명령줄’과  *userid* 가 동일할 때 프로세스 이벤트는 동일하다고 간주됩니다. 

프로세스 이벤트의 기본 버퍼는 32개 프로세스이며, 이후 버퍼가 순환되고, 가장 오래된 프로세스 이벤트는 새 프로세스 이벤트를 위한 공간을 만들기 위해 무시됩니다.  

## <a name="network-connection-events"></a>네트워크 연결 이벤트 

네트워크 연결 이벤트는 현재 Azure RTOS에서만 지원됩니다. 

네트워크 연결 이벤트는 ‘로컬 포트’, ‘원격 포트’, ‘전송 프로토콜’, ‘로컬 주소’, ‘원격 주소’가 동일할 때 동일한 것으로 간주됩니다. **  **  **  ** 

네트워크 연결 이벤트의 기본 버퍼는 64개입니다. 새 네트워크 이벤트는 다음 수집 주기까지 캐시되지 않으며 캐시 크기를 늘리라는 경고가 기록됩니다.

## <a name="next-steps"></a>다음 단계

[Defender for IoT 보안 경고](concept-security-alerts.md)를 확인합니다.
