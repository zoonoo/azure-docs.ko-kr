---
title: Azure IoT Hub 오류 404104 장치연결원격으로 문제 해결
description: 오류 404104 장치 연결을 해결하는 방법을 이해폐쇄원격으로
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: c8cb91aa0c7ce1610320d4107db282d3c34407ba
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758717"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

이 문서에서는 **404104 DeviceConnectionClosed원격으로** 오류에 대한 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

### <a name="symptom-1"></a>증상 1

장치는 정기적으로 연결이 끊어지며(예: 65분마다) IoT Hub 진단 로그에 **404104 DeviceConnectionClosedRemotely가** 표시됩니다. 경우에 따라 **401003 IoTHubUnauthorized** 및 1분 이내에 성공적인 장치 연결 이벤트가 표시됩니다.

### <a name="symptom-2"></a>증상 2

장치가 임의로 연결을 끊고 IoT Hub 진단 로그에 **404104 DeviceConnectionClosed원격으로** 표시됩니다.

### <a name="symptom-3"></a>증상 3

많은 장치가 한 번에 연결이 끊어지고 [연결된 장치 메트릭이](iot-hub-metrics.md)급격히 표시되며 평소보다 진단 로그에 **404104 DeviceConnectionClosed원격으로** [500xxx 내부 오류가](iot-hub-troubleshoot-error-500xxx-internal-errors.md) 있습니다.

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 1

[IoT Hub에 연결하는 데 사용되는 SAS 토큰이](iot-hub-devguide-security.md#security-tokens) 만료되어 IoT Hub가 장치의 연결을 끊습니다. 장치에서 토큰을 새로 고칠 때 연결이 다시 설정됩니다. 예를 들어 [SAS 토큰은 C SDK에 대해 기본적으로 매시간 만료되며,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)이로 인해 정기적인 연결이 끊어지는 경우

자세한 내용은 [401003 IoTHub무단 원인을](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)참조하십시오.

### <a name="cause-2"></a>원인 2

몇 가지 가능성은 다음과 같습니다.

- 이 장치는 [MQTT 유지](iot-hub-mqtt-support.md#default-keep-alive-timeout)시간보다 더 오래 기본 네트워크 연결이 끊어져 원격 유휴 시간 초과가 발생했습니다. MQTT 유지-살아 있는 설정은 장치마다 다를 수 있습니다.

- 장치가 TCP/IP 수준 재설정을 보냈지만 응용 프로그램 수준을 `MQTT DISCONNECT`보내지 않았습니다. 기본적으로 장치는 기본 소켓 연결을 갑자기 닫았습니다. 경우에 따라 이 문제는 이전 버전의 Azure IoT SDK의 버그로 인해 발생합니다.

- 장치 측 응용 프로그램이 충돌했습니다.

### <a name="cause-3"></a>원인 3

IoT Hub에 일시적인 문제가 있을 수 있습니다. [IoT Hub 내부 서버 오류 원인을](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)참조하십시오.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>해결 방법 1

[401003 IoTHub무단 솔루션 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1) 참조

### <a name="solution-2"></a>해결 방법 2

- 연결을 테스트하여 장치가 IoT [Hub에](tutorial-connectivity.md)양호한 연결상태를 가지고 있는지 확인합니다. 네트워크가 신뢰할 수 없거나 간헐적인 경우 Azure Monitor 경고를 통해 검색이 오래 걸리므로 유지 값을 늘리는 것이 좋습니다. 

- 최신 버전의 [IoT SDK를](iot-hub-devguide-sdks.md)사용합니다.

### <a name="solution-3"></a>해결 방법 3

[IoT Hub 내부 서버 오류에 대한 솔루션을](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure IoT 장치 SDK를 사용하여 연결을 안정적으로 관리하는 것이 좋습니다. 자세한 내용은 [Azure IoT Hub 장치 SDK를 사용하여 연결 및 안정적인 메시징 관리를 참조하세요.](iot-hub-reliability-features-in-sdks.md)