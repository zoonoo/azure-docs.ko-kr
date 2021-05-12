---
title: 문제 해결 Azure IoT Hub 오류 403004 DeviceMaximumQueueDepthExceeded
description: 오류 403004 DeviceMaximumQueueDepthExceeded 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3a8a63837617bc03ced475ae514f748e85d9b237
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061335"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

이 문서에서는 **403004 DeviceMaximumQueueDepthExceeded** 오류에 대한 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

클라우드-디바이스 메시지를 보내려고 하면 요청이 실패하고 **403004** 또는 **DeviceMaximumQueueDepthExceeded** 오류가 표시됩니다.

## <a name="cause"></a>원인

근본 원인은 디바이스의 큐에 넣은 메시지 수가 [큐 제한(50)](./iot-hub-devguide-quotas-throttling.md#other-limits)을 초과하기 때문입니다.

이 제한에 도달하는 가장 큰 이유는 HTTPS를 사용하여 메시지를 수신하고 있기 때문입니다. 이로 인해 `ReceiveAsync`를 사용하는 지속적인 폴링이 발생하여 IoT Hub가 요청을 제한합니다.

## <a name="solution"></a>솔루션

HTTPS에서 클라우드-디바이스 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(25분에 한 번씩보다 적게) 확인하는 디바이스에 간헐적으로 연결됩니다. 큐 제한에 도달 가능성을 줄이려면 클라우드-디바이스 메시지에 대해 AMQP 또는 MQTT로 전환합니다.

또는 큐에 넣은 메시지를 빠르게 완료, 거부 또는 폐기하도록 디바이스 측 논리를 강화하거나 TTL(Time to Live)을 단축시키고 보내는 메시지 수를 줄이는 것이 좋습니다. [C2D 메시지 TTL(Time to Live)](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)을 참조하세요.

마지막으로, 제한에 도달하기 전에 [큐 API 제거](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice)를 사용하여 보류 중인 메시지를 정기적으로 정리하는 것이 좋습니다.