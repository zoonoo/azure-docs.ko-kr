---
title: Azure IoT Hub 오류 401003 IoTHubUnauthorized 문제 해결
description: 401003 IoTHubUnauthorized 오류 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 23bd1ce63e3d697a40125c79e802ad3e01915f49
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109487496"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

이 문서에서는 **401003 IoTHubUnauthorized** 오류에 대한 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

### <a name="symptom-1"></a>증상 1

로그에는 디바이스 연결 해제 패턴이 표시되고 **401003 IoTHubUnauthorized**, **404104 DeviceConnectionClosedRemotely** 가 차례로 표시된 다음, 바로 성공적으로 연결하는 디바이스 패턴이 표시됩니다.

### <a name="symptom-2"></a>증상 2

IoT Hub 요청이 실패하고 다음 오류 메시지 중 하나가 표시됩니다.

* 권한 부여 헤더 누락
* IotHub '\*'에 지정된 디바이스 '\*'가 없음
* 권한 부여 규칙 ‘\*’에서 '\*’에 대한 액세스를 허용하지 않음
* 이 디바이스에 대한 권한 부여 실패, 토큰 또는 인증서 갱신 및 다시 연결
* 지문이 구성과 일치하지 않음. 지문: SHA1Hash=\*, SHA2Hash=\*, 구성: PrimaryThumbprint=\*, SecondaryThumbprint=\*
* 보안 주체 user@example.com은 할당된 권한이 없기 때문에 /exampleOperation에서 GET에 대한 권한이 없음

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

MQTT의 경우 일부 SDK는 새로 고칠 시기를 파악하기 위해 IoT Hub를 사용하여 SAS 토큰이 만료될 때 연결을 해제합니다. 따라서

1. SAS 토큰이 만료됩니다.
1. IoT Hub가 만료를 알리고 디바이스 연결을 해제하고 **401003 IoTHubUnauthorized** 를 표시합니다.
1. 디바이스가 연결 해제를 완료하고 **404104 DeviceConnectionClosedRemotely** 를 표시합니다.
1. IoT SDK가 새 SAS 토큰을 생성합니다.
1. 디바이스가 IoT Hub와 성공적으로 다시 연결됩니다.

### <a name="cause-2"></a>원인 2

IoT Hub가 권한 부여 헤더, 규칙 또는 키를 인증할 수 없습니다. 증상에 언급된 이유 중 어느 것이라도 원인일 수 있습니다.

## <a name="solution"></a>해결 방법

### <a name="solution-1"></a>해결 방법 1

디바이스 연결 문자열을 사용하는 연결에 IoT SDK를 사용하는 경우 아무 작업도 필요하지 않습니다. IoT SDK는 새 토큰을 다시 생성하여 SAS 토큰 만료 시 다시 연결합니다.

기본 토큰 수명은 SDK 전체에서 60분입니다. 그러나 일부 SDK의 경우 토큰 수명 및 토큰 갱신 임계값을 구성할 수 있습니다. 또한 디바이스가 연결 해제되고 토큰 갱신 시 다시 연결하는 경우 생성되는 오류는 SDK마다 다릅니다. 자세한 내용과 디바이스에서 로그에 사용 중인 SDK를 확인하는 방법은 [Azure IoT SDK를 사용하여 MQTT 디바이스 연결 해제 동작](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks)을 참조하세요.

디바이스 개발자의 경우 오류 볼륨이 문제가 되면 C SDK로 전환합니다. 그러면 만료되기 전에 SAS 토큰을 갱신합니다. AMQP의 경우 SAS 토큰은 연결을 해제하지 않고 새로 고칠 수 있습니다.

### <a name="solution-2"></a>해결 방법 2

일반적으로 오류를 해결하는 방법을 설명하는 오류 메시지가 표시됩니다. 어떤 이유로든 오류 메시지 세부 정보에 액세스할 수 없는 경우 다음을 확인합니다.

- SAS 또는 사용하는 기타 보안 토큰이 만료되지 않았는지 확인합니다.
- X.509 인증서 인증의 경우 디바이스와 연결된 디바이스 인증서 또는 CA 인증서가 만료되지 않았는지 확인합니다. X.509 CA 인증서를 IoT Hub에 등록하는 방법을 알아보려면 [Azure IoT Hub에서 X.509 보안 설정](./tutorial-x509-scripts.md)을 참조하세요.
- X.509 인증서 지문 인증의 경우 디바이스 인증서의 지문이 IoT Hub에 등록되었는지 확인합니다.
- 권한 부여 자격 증명이 사용하는 프로토콜에 맞게 구성되었는지 확인합니다. 자세히 알아보려면 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)를 참조하세요.
- 사용된 권한 부여 규칙에 요청된 작업에 대한 권한이 있는지 확인합니다.
- "principal..."로 시작하는 마지막 오류 메시지의 경우 사용자에게 올바른 수준의 Azure RBAC 권한을 할당하여 이 오류를 해결할 수 있는지 확인합니다. 예를 들어 IoT Hub 소유자는 모든 권한을 부여하는 "IoT Hub 데이터 소유자" 역할을 할당할 수 있습니다. 권한 부족 문제를 해결하려면 이 역할을 시도하세요.

## <a name="next-steps"></a>다음 단계

- IoT Hub에 대한 인증을 용이하게 하려면 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 사용하는 것이 좋습니다.
- IoT Hub에 인증하는 방법에 대한 자세한 내용은 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)를 참조하세요.