---
title: 이벤트 집계
titleSuffix: Azure Defender for IoT
description: IoT 용 Defender 보안 에이전트는 로컬 장치에서 데이터 및 시스템 이벤트를 수집 하 고 처리 및 분석을 위해 데이터를 Azure 클라우드에 보냅니다.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 53811c20330d96b279eeb24e1aa90248b60fcb32
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120048"
---
# <a name="event-aggregation"></a>이벤트 집계 

IoT 용 Defender 보안 에이전트는 로컬 장치에서 데이터 및 시스템 이벤트를 수집 하 고 처리 및 분석을 위해 데이터를 Azure 클라우드에 보냅니다. IoT 마이크로 에이전트의 Defender는 새 프로세스 및 모든 새 연결 이벤트를 포함 하 여 다양 한 유형의 장치 이벤트를 수집 합니다. 새 프로세스와 새 연결 이벤트는 장치에서 1 초 이내에 자주 발생할 수 있습니다. 이 기능은 포괄적인 보안을 위해 중요 하지만, 보안 에이전트에서 전송 하는 메시지 수가 빠르게 충족 되거나 IoT Hub 할당량 및 비용 제한을 초과할 수 있습니다. 그럼에도 불구 하 고 이러한 이벤트는 장치를 보호 하는 데 중요 한 매우 중요 한 보안 정보를 포함 합니다. 

장치를 보호 하는 동안 추가 할당량 및 비용을 줄이기 위해 Defender for IoT 에이전트는 다음과 같은 유형의 이벤트를 집계 합니다. 

- ProcessCreate (Linux만 해당) 

- ConnectionCreate (Azure RTOS 전용) 

## <a name="how-does-event-aggregation-work"></a>이벤트 집계는 어떻게 작동 하나요? 

IoT 용 Defender 에이전트는 기간 또는 기간에 대 한 이벤트를 집계 합니다. 간격 기간이 지나면 에이전트는 추가 분석을 위해 집계 된 이벤트를 Azure 클라우드로 보냅니다. 집계 된 이벤트는 Azure 클라우드로 전송 될 때까지 메모리에 저장 됩니다. 

에이전트가 이미 메모리에 보관 되어 있는 것과 동일한 이벤트를 수집 하는 경우 에이전트는 에이전트의 메모리 사용 공간을 줄이기 위해이 특정 이벤트의 적중 횟수를 늘립니다. 집계 기간이 지나면 에이전트는 발생 한 각 이벤트 유형의 적중 횟수를 보냅니다. 이벤트 집계는 수집 된 각 이벤트 형식의 적중 횟수에 대 한 집계 일 뿐입니다. 

## <a name="process-events"></a>이벤트 처리 

프로세스 이벤트는 현재 Linux 운영 체제 에서만 지원 됩니다. 

*명령줄* 과  *userid* 가 동일할 때 프로세스 이벤트는 동일 하다 고 간주 됩니다   . 

프로세스 이벤트의 기본 버퍼는 32 프로세스 이며, 그 후에는 버퍼가 순환 되 고 가장 오래 된 프로세스 이벤트는 새 프로세스 이벤트를 위한 공간을 만들기 위해 무시 됩니다.  

## <a name="network-connection-events"></a>네트워크 연결 이벤트 

네트워크 연결 이벤트는 현재 Azure RTOS 에서만 지원 됩니다. 

네트워크 연결 이벤트는 *로컬 포트*, *원격 포트*, *전송 프로토콜*, *로컬 주소* 및  *원격 주소가* 동일할 때 동일 하 게 간주 됩니다. 

네트워크 연결 이벤트의 기본 버퍼는 64입니다. 새 네트워크 이벤트는 다음 수집 주기까지 캐시 되지 않습니다. 캐시 크기를 늘리는 경고가 기록 됩니다.

## <a name="next-steps"></a>다음 단계

Defender에서 [IoT 보안 경고를](concept-security-alerts.md)확인 합니다.
