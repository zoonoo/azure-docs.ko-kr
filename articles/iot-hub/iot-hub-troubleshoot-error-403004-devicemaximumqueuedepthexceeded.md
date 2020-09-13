---
title: 문제 해결 Azure IoT Hub 오류 403004 DeviceMaximumQueueDepthExceeded
description: 403004 오류를 해결 하는 방법 이해 DeviceMaximumQueueDepthExceeded
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
ms.openlocfilehash: fc5029f26e5d615502925c4def4e2973c118f38d
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029990"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

이 문서에서는 **403004 DeviceMaximumQueueDepthExceeded** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

클라우드-장치 메시지를 보내려고 할 때 요청이 실패 하 고 **403004** 또는 **DeviceMaximumQueueDepthExceeded**오류가 발생 합니다.

## <a name="cause"></a>원인

근본적인 원인은 장치에 대해 큐에 넣은 메시지 수가 [큐 제한 (50)](./iot-hub-devguide-quotas-throttling.md#other-limits)을 초과 하기 때문입니다.

이 한도를 실행 하는 가장 큰 이유는 HTTPS를 사용 하 여 메시지를 수신 하는 것입니다 .이 경우는를 사용 하 여 연속 폴링이 발생 하므로 `ReceiveAsync` 요청을 제한 IoT Hub.

## <a name="solution"></a>솔루션

HTTPS에서 클라우드-디바이스 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(25분에 한 번씩보다 적게) 확인하는 디바이스에 간헐적으로 연결됩니다. 큐 제한에 대 한 실행 가능성을 줄이려면 클라우드-장치 메시지에 대해 AMQP 또는 MQTT로 전환 합니다.

또는 큐에 대기 중인 메시지를 신속 하 게 완료, 거부 또는 포기 하도록 장치 측 논리를 개선 하 고, 라이브 시간을 단축 하거나, 더 짧은 메시지를 전송 하는 것이 좋습니다. [C2D 메시지 TTL(Time to Live)](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live)을 참조하세요.

마지막으로, 한도에 도달 하기 전에 [제거 큐 API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) 를 사용 하 여 보류 중인 메시지를 정기적으로 정리 하는 것이 좋습니다.