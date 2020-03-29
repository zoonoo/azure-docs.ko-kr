---
title: Azure IoT 장치 프로비저닝 서비스 MQTT 지원 이해 | 마이크로 소프트 문서
description: 개발자 가이드 - MQTT 프로토콜을 사용하여 Azure IoT 장치 프로비저닝 서비스(DPS) 장치 연결 엔드포인트에 연결하는 장치에 대한 지원입니다.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973376"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>MQTT 프로토콜을 사용하여 DPS와 통신

DPS를 사용하면 다음을 사용하여 장치가 DPS 장치 끝점과 통신할 수 있습니다.

* 포트 8883에서 [MQTT v3.1.1](https://mqtt.org/)
* 포트 443의 WebSocket을 통해 [MQTT v3.1.1.](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127)

DPS는 완전한 기능을 갖춘 MQTT 브로커가 아니며 MQTT v3.1.1 표준에 지정된 모든 동작을 지원하지 않습니다. 이 문서에서는 장치가 지원되는 MQTT 동작을 사용하여 DPS와 통신하는 방법을 설명합니다.

DPS와의 모든 장치 통신은 TLS/SSL을 사용하여 보호되어야 합니다. 따라서 DPS포트 1883을 통해 비보안 연결을 지원하지 않습니다.

 > [!NOTE] 
 > DPS는 현재 MQTT 프로토콜을 통해 TPM [증명 메커니즘을](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) 사용하는 장치를 지원하지 않습니다.

## <a name="connecting-to-dps"></a>DPS에 연결

장치는 MQTT 프로토콜을 사용하여 다음 옵션을 사용하여 DPS에 연결할 수 있습니다.

* [Azure IoT 프로비전 SDK의](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)라이브러리입니다.
* MQTT 프로토콜을 직접.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT 프로토콜 직접 사용(디바이스로)

디바이스가 디바이스 SDK를 사용할 수 없는 경우라도 포트 8883에서 MQTT 프로토콜을 사용하는 공용 디바이스 엔드포인트에 연결할 수 있습니다. **CONNECT** 패킷에서 장치는 다음 값을 사용해야 합니다.

* **ClientId** 필드의 경우 **registerId**를 사용합니다.

* 사용자 **이름** 필드의 경우 `{idScope}/registrations/{registration_id}/api-version=2019-03-31`에서 `{idScope}` [dPS의 idScope는](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) 어디에 사용합니다.

* **암호** 필드에는 SAS 토큰을 사용합니다. SAS 토큰의 형식은 HTTPS 및 AMQP 프로토콜에 대해 동일합니다.

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`resourceURI 형식이어야 `{idScope}/registrations/{registration_id}`합니다. 정책 이름은 이어야 `registration`합니다.

  > [!NOTE]
  > X.509 인증서 인증을 사용하는 경우 SAS 토큰 암호는 필요하지 않습니다.

  SAS 토큰을 생성하는 방법에 대한 자세한 내용은 [DPS에 대한 제어 액세스의](how-to-control-access.md#security-tokens)보안 토큰 섹션을 참조하십시오.

다음은 DPS 구현별 동작 목록입니다.

 * DPS는 **0으로**설정 되는 **CleanSession** 플래그의 기능을 지원 하지 않습니다.

 * 장치 앱이 **QoS 2로**토픽을 구독하는 경우 DPS는 **SUBACK** 패킷에서 최대 QoS 수준 1을 부여합니다. 그런 다음 DPS는 QoS 1을 사용하여 장치에 메시지를 전달합니다.

## <a name="tlsssl-configuration"></a>TLS/SSL 구성

MQTT 프로토콜을 직접 사용하려면 클라이언트가 TLS 1.2를 통해 *연결해야 합니다.* 이 단계를 건너뛰려고 하면 연결 오류가 발생하여 실패합니다.


## <a name="registering-a-device"></a>디바이스 등록

DPS를 통해 장치를 등록하려면 장치가 `$dps/registrations/res/#` **토픽 필터로**구독해야 합니다. 토픽 필터에 다중 레벨 와일드카드 `#`는 디바이스가 토픽 이름에 추가 속성을 수신하도록 하려는 경우에만 사용됩니다. DPS는 하위 주제를 필터링하기 위해 `#` 또는 `?` 와일드카드의 사용을 허용하지 않습니다. DPS는 범용 pub-sub 메시징 브로커가 아니므로 문서화된 토픽 이름 및 토픽 필터만 지원합니다.

장치는 `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` **토픽 이름으로**사용하여 DPS에 레지스터 메시지를 게시해야 합니다. 페이로드에는 [JSON](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) 형식의 장치 등록 개체가 포함되어야 합니다.
성공적인 시나리오에서 장치는 x가 몇 초 `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` 만에 다시 시도 후 값인 토픽 이름에 대한 응답을 받게 됩니다. 응답의 페이로드에는 JSON 형식의 [RegisterOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) 개체가 포함됩니다.

## <a name="polling-for-registration-operation-status"></a>등록 작업 상태에 대한 폴링

장치는 장치 등록 작업의 결과를 수신하기 위해 정기적으로 서비스를 폴링해야 합니다. 장치가 위에 표시된 대로 토픽을 `$dps/registrations/res/#` 이미 구독했다고 가정하면 `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` 토픽 이름에 get operationstatus 메시지를 게시할 수 있습니다. 이 메시지의 작업 ID는 이전 단계에서 등록작업 상태 응답 메시지에서 받은 값이어야 합니다. 성공적인 경우 서비스는 주제에 응답합니다. `$dps/registrations/res/200/?$rid={request_id}` 응답의 페이로드에는 등록작업상태 개체가 포함됩니다. 응답 코드가 재시도 후 기간과 동일한 지연 후 202인 경우 장치는 서비스를 폴링해야 합니다. 서비스가 200 상태 코드를 반환하는 경우 장치 등록 작업이 성공합니다.

## <a name="connecting-over-websocket"></a>웹 소켓을 통해 연결
Websocket을 통해 연결할 때 하위 `mqtt`프로토콜을 로 지정합니다. [RFC 6455를](https://tools.ietf.org/html/rfc6455)따르십시오.

## <a name="next-steps"></a>다음 단계

MQTT 프로토콜에 대한 자세한 내용은 [MQTT 설명서](https://mqtt.org/documentation)를 참조하세요.

DPS의 기능을 자세히 알아보려면 다음을 참조하십시오.

* [IoT DPS 소개](about-iot-dps.md)
