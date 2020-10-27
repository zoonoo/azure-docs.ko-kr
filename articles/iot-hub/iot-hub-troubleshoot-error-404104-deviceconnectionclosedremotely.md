---
title: 문제 해결 Azure IoT Hub 오류 404104 DeviceConnectionClosedRemotely
description: 404104 오류를 해결 하는 방법 이해 DeviceConnectionClosedRemotely
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538258"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

이 문서에서는 **404104 DeviceConnectionClosedRemotely** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

### <a name="symptom-1"></a>증상 1

장치는 일정 한 간격 (예: 65 분 마다)에서 연결을 끊고 IoT Hub 리소스 로그에 **404104 DeviceConnectionClosedRemotely** 이 표시 됩니다. 경우에 따라 **401003 IoTHubUnauthorized** 및 성공한 장치 연결 이벤트는 나중에 1 분 이내에 표시 됩니다.

### <a name="symptom-2"></a>증상 2

장치는 임의로 연결을 끊고 IoT Hub 리소스 로그에 **404104 DeviceConnectionClosedRemotely** 이 표시 됩니다.

### <a name="symptom-3"></a>증상 3

여러 장치에서 한 번에 연결을 끊으면 [연결 된 장치 (connectedDeviceCount) 메트릭에](monitor-iot-hub-reference.md)dip가 표시 되 고, Azure Monitor 로그에 **404104 DeviceConnectionClosedRemotely** 및 [500xxx 내부 오류가](iot-hub-troubleshoot-error-500xxx-internal-errors.md) 있습니다.

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 1

[IoT Hub에 연결 하는 데 사용 되는 SAS 토큰이](iot-hub-devguide-security.md#security-tokens) 만료 되어 IoT Hub에서 장치의 연결을 끊습니다. 장치에서 토큰을 새로 고칠 때 연결이 다시 설정 됩니다. 예를 들어 [SAS 토큰은 기본적으로 C SDK에 대해 매시간 만료](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)되며이로 인해 일반 연결이 끊어질 수 있습니다.

자세히 알아보려면 [401003 IoTHubUnauthorized 원인](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)을 참조 하세요.

### <a name="cause-2"></a>원인 2

몇 가지 가능한 원인은 다음과 같습니다.

- 장치가 [Mqtt keep-alive](iot-hub-mqtt-support.md#default-keep-alive-timeout)보다 오래 된 기본 네트워크 연결을 끊어 원격 유휴 시간 제한이 발생 했습니다. MQTT keep-alive 설정은 장치별로 다를 수 있습니다.

- 장치에서 TCP/IP 수준 재설정을 보냈지만 응용 프로그램 수준을 보내지 않았습니다 `MQTT DISCONNECT` . 기본적으로 장치는 기본 소켓 연결을 갑자기 닫았습니다. 경우에 따라이 문제는 이전 버전의 Azure IoT SDK에서 버그가 발생 한 경우에 발생 합니다.

- 장치 쪽 응용 프로그램이 충돌 합니다.

### <a name="cause-3"></a>원인 3

IoT Hub 일시적인 문제가 발생할 수 있습니다. [IoT Hub 내부 서버 오류 원인](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)을 참조 하세요.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>해결 방법 1

[401003 IoTHubUnauthorized solution 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1) 을 참조 하세요.

### <a name="solution-2"></a>해결 방법 2

- [연결을 테스트](tutorial-connectivity.md)하 여 장치가 IoT Hub에 올바르게 연결 되어 있는지 확인 합니다. 네트워크를 안정적이 지 않거나 간헐적으로 검색 하는 경우 (예: Azure Monitor 경고를 통해) 더 오래 걸리고 있으므로 keep-alive 값을 늘리지 않는 것이 좋습니다. 

- 최신 버전의 [IoT sdk](iot-hub-devguide-sdks.md)를 사용 합니다.

### <a name="solution-3"></a>해결 방법 3

[IoT Hub 내부 서버 오류에 대 한 해결 방법을](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure IoT 장치 Sdk를 사용 하 여 연결을 안정적으로 관리 하는 것이 좋습니다. 자세히 알아보려면 [Azure IoT Hub 장치 sdk를 사용 하 여 연결 및 신뢰할 수 있는 메시징 관리](iot-hub-reliability-features-in-sdks.md) 를 참조 하세요.