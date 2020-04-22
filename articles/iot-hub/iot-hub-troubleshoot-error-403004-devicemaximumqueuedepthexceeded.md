---
title: Azure IoT 허브 오류 403004 장치최적 큐깊이 초과
description: 오류 403004 장치해결 방법 이해최대큐심초과
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
ms.openlocfilehash: 5cc8bae0f0245f5c4b45ca0cd446582b04788c21
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758761"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

이 문서에서는 **403004 DeviceMaximumQueueDepth초과** 오류의 원인과 해결에 대해 설명합니다.

## <a name="symptoms"></a>증상

클라우드-장치 메시지를 보내려고 할 때 오류 **403004** 또는 **DeviceMaximumQueueDepth초과와**함께 요청이 실패합니다.

## <a name="cause"></a>원인

근본 원인은 장치에 대해 큐에 대기된 메시지 수가 [큐 제한(50)을](./iot-hub-devguide-quotas-throttling.md#other-limits)초과하기 때문에 입니다.

이 제한을 실행하는 가장 큰 이유는 HTTPS를 사용하여 메시지를 수신하기 때문에 지속적인 폴링을 사용하여 `ReceiveAsync`IoT Hub가 요청을 제한하기 때문입니다.

## <a name="solution"></a>솔루션

HTTPS에서 클라우드-디바이스 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(25분에 한 번씩보다 적게) 확인하는 디바이스에 간헐적으로 연결됩니다. 큐 제한에 실행될 가능성을 줄이려면 클라우드-장치 메시지의 AMQP 또는 MQTT로 전환합니다.

또는 장치 측 논리를 강화하여 큐에 대기 중인 메시지를 빠르게 완료, 거부 또는 포기하거나, 라이브 시간을 단축하거나, 더 적은 메시지를 보내는 것을 고려합니다. [C2D 메시지 TTL(Time to Live)](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)을 참조하세요.

마지막으로, 제한에 도달하기 전에 [퍼지 큐 API를](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) 사용하여 보류 중인 메시지를 주기적으로 정리하는 것이 좋습니다.