---
title: 문제 해결 Azure IoT Hub 오류 412002 DeviceMessageLockLost
description: 오류 412002 DeviceMessageLockLost 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: eb3d7f15109d3b217f651a7d927601ef7fae66c2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061029"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

이 문서에서는 **412002 DeviceMessageLockLost** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

클라우드-디바이스 메시지를 전송하려고 하면 요청이 실패하고 **412002 DeviceMessageLockLost** 오류가 표시됩니다.

## <a name="cause"></a>원인

디바이스가 큐에서 클라우드-디바이스 메시지를 수신하면(예: [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) 사용) 잠금 시간 제한 1분 동안 IoT Hub에 의해 메시지가 잠깁니다. 잠금 시간 제한이 만료된 후 디바이스가 메시지를 완료하려고 하면 IoT Hub가 이 예외를 throw합니다.

## <a name="solution"></a>솔루션

IoT Hub가 1분 잠금 시간 제한 내에 알림을 받지 못하면 메시지를 다시 ‘큐에 넣은’ 상태로 설정합니다. 디바이스에서 메시지를 다시 수신할 수 있습니다. 이후에 오류가 발생하지 않도록 하려면 메시지를 수신한 후 1분 이내에 메시지를 완료할 디바이스 측 논리를 구현합니다. 이 1분 시간 제한은 변경할 수 없습니다.