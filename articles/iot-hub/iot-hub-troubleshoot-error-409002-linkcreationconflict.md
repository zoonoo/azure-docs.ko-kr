---
title: 문제 해결 Azure IoT Hub 오류 409002 LinkCreationConflict
description: 오류 409002 LinkCreationConflict 해결 방법 이해
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5433af10cef6ef4404d61d3b66aedc311d242c71
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515555"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

이 문서에서는 **409002 LinkCreationConflict** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

디바이스 연결 끊김 또는 클라우드 - 디바이스 메시지 오류와 함께 **409002 LinkCreationConflict** 가 로그에 표시됩니다.

<!-- When using AMQP? -->

## <a name="cause"></a>원인

일반적으로 이 오류는 IoT Hub가 클라이언트에 둘 이상의 연결이 있는 것을 탐지했을 때 발생합니다. 실제로 기존 연결 중인 디바이스에 새 연결 요청이 오면 IoT Hub는 이 오류로 기존 연결을 닫습니다.

### <a name="cause-1"></a>원인 1

가장 일반적인 경우 별도의 문제(예: [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md))로 인해 디바이스 연결이 끊어집니다. 디바이스는 즉시 연결을 다시 설정하려고 하지만 IoT Hub는 여전히 이 디바이스가 연결된 것으로 인식합니다. IoT Hub는 이전 연결을 닫고 이 오류를 기록합니다.

### <a name="cause-2"></a>원인 2

잘못된 디바이스 측 논리로 인해 디바이스가 이미 열려 있을 때 디바이스에서 연결을 설정합니다.

## <a name="solution"></a>솔루션

이 오류는 대부분 다른 일시적인 문제의 부작용으로 나타나므로 추가로 문제를 해결하려면 로그에서 다른 오류를 검색합니다. 그렇지 않으면, 연결이 끊어질 경우만 새 연결 요청을 발급해야 합니다.
