---
title: 문제 해결 Azure IoT Hub 오류 404104 DeviceConnectionClosedRemotely
description: 오류 404104 DeviceConnectionClosedRemotely 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: f1acd7b309a31dfe437f1f82cb9bbf73f4a5cca5
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061080"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

이 문서에서는 **404104 DeviceConnectionClosedRemotely** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

### <a name="symptom-1"></a>증상 1

디바이스의 연결이 정기적으로 끊어지고(예: 65분마다) IoT Hub 리소스 로그에 **404104 DeviceConnectionClosedRemotely** 가 표시됩니다. 경우에 따라 **401003 IoTHubUnauthorized** 가 표시되고 1분도 지나지 않아 디바이스의 성공적인 연결 이벤트도 확인할 수 있습니다.

### <a name="symptom-2"></a>증상 2

디바이스의 연결이 임의로 끊어지고 IoT Hub 리소스 로그에 **404104 DeviceConnectionClosedRemotely** 가 표시됩니다.

### <a name="symptom-3"></a>증상 3

여러 디바이스에서 한 번에 연결이 끊기면 [연결된 디바이스(connectedDeviceCount) 메트릭](monitor-iot-hub-reference.md)에 DIP가 표시되고, Azure Monitor 로그에는 평소보다 많은 **404104 DeviceConnectionClosedRemotely** 및 [500xxx 내부 오류](iot-hub-troubleshoot-error-500xxx-internal-errors.md)가 나타납니다.

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 1

[IoT Hub 연결에 사용되는 SAS 토큰](iot-hub-devguide-security.md#security-tokens)이 만료되어 IoT Hub에서 디바이스의 연결을 끊습니다. 디바이스에서 토큰을 새로 고칠 때 연결이 다시 설정됩니다. 예를 들어 [기본적으로 C SDK에 대해 SAS 토큰이 매시간 만료](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)되므로 정기적으로 연결이 끊어질 수 있습니다.

자세히 알아보려면 [401003 IoTHubUnauthorized 원인](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)을 참조하세요.

### <a name="cause-2"></a>원인 2

다음과 같은 몇 가지 가능한 원인이 있습니다.

- 디바이스에서 [MQTT keep-alive](iot-hub-mqtt-support.md#default-keep-alive-timeout)보다 오래된 기본 네트워크 연결이 끊어져 원격 유휴 시간 제한이 발생했습니다. MQTT keep-alive 설정은 디바이스별로 다를 수 있습니다.

- 디바이스가 TCP/IP 수준 다시 설정을 전송했지만 애플리케이션 수준 `MQTT DISCONNECT`를 전송하지 않았습니다. 기본적으로 디바이스는 기본 소켓 연결을 갑자기 닫았습니다. 경우에 따라 이 문제는 이전 버전의 Azure IoT SDK의 버그로 인해 발생합니다.

- 디바이스 측 애플리케이션 크래시가 발생했습니다.

### <a name="cause-3"></a>원인 3

IoT Hub에서 일시적인 문제가 발생할 수 있습니다. [IoT Hub 내부 서버 오류 원인](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)을 참조하세요.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>해결 방법 1

[401003 IoTHubUnauthorized 해결 방법 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)을 참조하세요.

### <a name="solution-2"></a>해결 방법 2

- [연결 테스트](tutorial-connectivity.md)를 통해 디바이스가 IoT Hub에 제대로 연결되었는지 확인합니다. 네트워크가 불안정하거나 일시적인 연결인 경우 탐지(예: Azure Monitor 경고를 통함) 시간이 더 오래 걸릴 수 있기 때문에 keep-alive 값을 늘리지 않는 것이 좋습니다. 

- 최신 버전의 [IoT SDK](iot-hub-devguide-sdks.md)를 사용합니다.

### <a name="solution-3"></a>해결 방법 3

[IoT Hub 내부 서버 오류에 대한 해결 방법](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure IoT 디바이스 SDK를 사용하여 연결을 신뢰할 수 있도록 관리하는 것이 좋습니다. 자세한 내용은 [Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시지 관리](iot-hub-reliability-features-in-sdks.md)에서 확인할 수 있습니다.