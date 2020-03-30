---
title: Azure IoT 허브 오류 401003 IoTHub무단 해결
description: 오류 401003 IoTHub무단 수정 하는 방법을 이해 합니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284409"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

이 문서에서는 **401003 IoTHub무단** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

### <a name="symptom-1"></a>증상 1

진단 로그에서 **401003 IoTHubUnauthorized로**연결이 끊어지는 장치 패턴이 표시되고 **404104 DeviceConnectionClosed원격으로**연결한 다음 곧 성공적으로 연결됩니다.

### <a name="symptom-2"></a>증상 2

IoT Hub에 대한 요청은 다음 오류 메시지 중 하나로 실패합니다.

* 권한 부여 헤더가 누락되었습니다.
* IotHub\*' ' 지정 된\*장치를 포함 하지 않습니다 ' '
* 권한 부여\*규칙 ' '\*에 대 한 액세스를 허용 하지 않습니다.
* 이 장치에 대한 인증이 실패하고 토큰 또는 인증서를 갱신하고 다시 연결합니다.
* 지문이 구성과 일치하지 않음: 지문: SHA1해시=\*,\*SHA2Hash= ; 구성: 기본 지문\*= , 보조 지문 =\*

## <a name="cause"></a>원인

### <a name="cause-1"></a>원인 1

MQTT의 경우 일부 SDK는 IoT Hub를 사용하여 SAS 토큰이 만료되면 연결을 끊어 언제 새로 고쳐야 하는지 알 수 있습니다. 따라서 

1. SAS 토큰이 만료됩니다.
1. IoT 허브 만료를 통지 하 고 **401003 IoTHub무단으로** 장치를 연결 합니다.
1. 장치가 **404104 장치** 연결해제를 완료합니다.원격으로
1. IoT SDK는 새로운 SAS 토큰을 생성합니다.
1. 장치가 IoT Hub와 성공적으로 다시 연결됨

### <a name="cause-2"></a>원인 2

IoT Hub는 인증 헤더, 규칙 또는 키를 인증할 수 없습니다.

## <a name="solution"></a>해결 방법

### <a name="solution-1"></a>해결 방법 1

장치 연결 문자열을 사용하여 연결에 IoT SDK를 사용하는 경우 작업이 필요하지 않습니다. IoT SDK는 SAS 토큰 만료 시 다시 연결하기 위해 새 토큰을 다시 생성합니다. 

오류 볼륨이 우려되는 경우 만료되기 전에 SAS 토큰을 갱신하는 C SDK로 전환합니다. 또한 AMQP의 경우 SAS 토큰은 연결 끊김 없이 새로 고칠 수 있습니다.

### <a name="solution-2"></a>해결 방법 2

일반적으로 제시된 오류 메시지는 오류를 수정하는 방법을 설명해야 합니다. 어떤 이유로 오류 메시지 세부 정보에 액세스할 수 없는 경우 다음을 확인하십시오.

- 사용하는 SAS 또는 기타 보안 토큰은 만료되지 않습니다. 
- 권한 부여 자격 증명은 사용하는 프로토콜에 대해 잘 형성되어 있습니다. 자세한 내용은 [IoT Hub 액세스 제어](iot-hub-devguide-security.md)를 참조하십시오.
- 사용된 권한 부여 규칙에는 요청된 작업에 대한 권한이 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub에 대한 인증을 더 쉽게 하려면 [Azure IoT SDK를](iot-hub-devguide-sdks.md)사용하는 것이 좋습니다.