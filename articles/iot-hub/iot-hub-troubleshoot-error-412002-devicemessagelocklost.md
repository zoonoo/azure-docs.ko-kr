---
title: 문제 해결 Azure IoT Hub 오류 412002 DeviceMessageLockLost
description: 412002 오류를 해결 하는 방법 이해 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7d48474d88a60c73f6094d3b9e65017c23404d8a
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144262"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

이 문서에서는 **412002 DeviceMessageLockLost** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

클라우드-장치 메시지를 보내려고 할 때 요청이 실패 하 고 **412002 DeviceMessageLockLost**오류가 발생 합니다.

## <a name="cause"></a>원인

장치가 큐에서 클라우드-장치 메시지를 수신 하는 경우 (예: 사용 [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet) ), 잠금 제한 시간 (1 분) 동안 IoT Hub에 의해 메시지가 잠깁니다. 잠금 시간 제한이 만료 된 후 장치에서 메시지를 완료 하려고 하면 IoT Hub이 예외를 throw 합니다.

## <a name="solution"></a>솔루션

IoT Hub 1 분 잠금 제한 시간 내에 알림을 받지 못하면 메시지를 다시 *큐* 에 넣은 상태로 설정 합니다. 장치에서 메시지 수신을 다시 시도할 수 있습니다. 이후에 오류가 발생 하지 않도록 하려면 메시지를 받은 후 1 분 이내에 메시지를 완료 하는 장치 측 논리를 구현 합니다. 이 1 분 제한 시간을 변경할 수 없습니다.