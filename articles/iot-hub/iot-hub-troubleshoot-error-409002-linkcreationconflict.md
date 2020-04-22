---
title: Azure IoT Hub 오류 409002 링크 생성충돌 문제 해결
description: 오류 409002 링크 크리에이션충돌을 해결하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 20d39b1f5a11f20eb5d12f34337787b382c820f6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758748"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

이 문서에서는 **409002 LinkCreationConflict** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

**오류 409002 LinkCreationConflict** 장치 연결 끊김 또는 클라우드-장치 메시지 오류와 함께 진단 로그에 로그인 된 참조. 

<!-- When using AMQP? -->

## <a name="cause"></a>원인

일반적으로 이 오류는 IoT Hub가 클라이언트에 두 개 이상의 연결이 있는 것을 감지할 때 발생합니다. 실제로 기존 연결이 있는 장치에 대한 새 연결 요청이 도착하면 IoT Hub는 이 오류로 기존 연결을 닫습니다.

### <a name="cause-1"></a>원인 1

가장 일반적인 경우 별도의 문제(예: [404104 DeviceConnectionClosedRemotely)로](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)인해 장치의 연결이 끊어집니다. 장치가 연결을 즉시 다시 설정하려고 시도하지만 IoT Hub는 연결된 장치를 여전히 고려합니다. IoT Hub는 이전 연결을 닫고 이 오류를 기록합니다.

### <a name="cause-2"></a>원인 2

장치 측 논리에 오류가 있으면 장치가 이미 열려 있을 때 연결이 설정됩니다.

## <a name="solution"></a>솔루션

이 오류는 대부분 다른 일시적인 문제의 부작용으로 나타나므로 추가로 문제를 해결하려면 로그에서 다른 오류를 검색합니다. 그렇지 않으면, 연결이 끊어질 경우만 새 연결 요청을 발급해야 합니다.
