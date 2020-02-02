---
title: 문제 해결 Azure IoT Hub 오류 401003 IoTHubUnauthorized
description: 401003 오류를 해결 하는 방법 이해 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960634"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

이 문서에서는 **401003 IoTHubUnauthorized** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

### <a name="symptom-1"></a>증상 1

진단 로그에 **401003 IoTHubUnauthorized**와의 연결을 끊은 후 **404104 DeviceConnectionClosedRemotely**을 연결한 후 잠시 후에 성공적으로 연결 하는 장치 패턴이 표시 됩니다.

### <a name="symptom-2"></a>증상 2

다음 오류 메시지 중 하나를 사용 하 여 IoT Hub에 대 한 요청이 실패 합니다.

* 권한 부여 헤더 누락
* IotHub '\*'에 지정 된 장치 '\*'이 (가) 포함 되어 있지 않습니다.
* 권한 부여 규칙 '\*'은 (는) '\*'에 대 한 액세스를 허용 하지 않습니다.
* 이 장치에 대 한 인증에 실패 했습니다. 토큰 또는 인증서를 갱신 하 고 다시 연결 하세요.
* 지문이 구성과 일치 하지 않음: Thumbprint: SHA1Hash =\*, SHA2Hash =\*; 구성: PrimaryThumbprint =\*, SecondaryThumbprint =\*

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

MQTT의 경우 일부 Sdk는 IoT Hub를 사용 하 여 SAS 토큰이 만료 될 때이를 새로 고칠 시기를 알 수 없을 때 연결을 발급 합니다. 그러한 

1. SAS 토큰이 만료 됩니다.
1. IoT Hub 만료를 통지 하 고 **401003 IoTHubUnauthorized** 장치 연결을 끊습니다.
1. 장치가 **404104 DeviceConnectionClosedRemotely** 연결을 완료 합니다.
1. IoT SDK는 새 SAS 토큰을 생성 합니다.
1. 장치가 IoT Hub에 다시 연결 되었습니다.

### <a name="cause-2"></a>원인 2

IoT Hub 인증 헤더, 규칙 또는 키를 인증할 수 없습니다.

## <a name="solution"></a>솔루션

### <a name="solution-1"></a>해결 방법 1

장치 연결 문자열을 사용 하 여 연결 하는 데 IoT SDK를 사용 하는 경우 아무 작업도 필요 하지 않습니다. IoT SDK는 SAS 토큰 만료 시 다시 연결 하기 위해 새 토큰을 다시 생성 합니다. 

오류가 발생 한 경우에는 C SDK로 전환 합니다. 그러면 만료 되기 전에 SAS 토큰을 갱신 합니다. 또한 AMQP의 경우 SAS 토큰은 연결을 끊지 않고 새로 고칠 수 있습니다.

### <a name="solution-2"></a>해결 방법 2

일반적으로 오류를 해결 하는 방법을 설명 하는 오류 메시지가 표시 됩니다. 어떤 이유로 인해 오류 메시지 세부 정보에 액세스할 수 없는 경우 다음을 확인 합니다.

- 사용 하는 SAS 또는 기타 보안 토큰이 만료 되지 않았습니다. 
- 권한 부여 자격 증명은 사용 하는 프로토콜에 대해 잘 구성 됩니다. 자세히 알아보려면 [IoT Hub access control](iot-hub-devguide-security.md)을 참조 하세요.
- 사용 된 권한 부여 규칙에 요청 된 작업에 대 한 권한이 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 대 한 인증을 용이 하 게 하려면 [Azure IoT sdk](iot-hub-devguide-sdks.md)를 사용 하는 것이 좋습니다.