---
title: Azure IoT 허브 오류 412002 장치메시지잠금 손실
description: 오류 412002 장치메시지잠금 을 수정하는 방법을 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960764"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

이 문서에서는 **412002 DeviceMessageLockLost** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

클라우드-장치 메시지를 보내려고 할 때 오류 **412002 DeviceMessageLockLost**.

## <a name="cause"></a>원인

디바이스가 큐에서 클라우드-디바이스 메시지를 수신하는 경우(예: [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)사용) 메시지는 1분의 잠금 시간 시간 동안 IoT Hub에 의해 잠깁니다. 장치가 잠금 시간 시간이 만료된 후 메시지를 완료하려고 하면 IoT Hub에서 이 예외를 throw합니다.

## <a name="solution"></a>해결 방법

IoT Hub가 1분 잠금 시간 시간 내에 알림을 받지 못하면 메시지를 *큐에 대기된* 상태로 다시 설정합니다. 장치에서 메시지를 다시 수신하려고 시도할 수 있습니다. 나중에 오류가 발생하지 않도록 하려면 장치 측 논리를 구현하여 메시지를 받은 후 1분 이내에 메시지를 완료합니다. 이 1분 시간 제정은 변경할 수 없습니다.