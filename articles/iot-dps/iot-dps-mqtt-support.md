---
title: Azure IoT 디바이스 프로비저닝 서비스 MQTT 지원 이해 | Microsoft Docs
description: 개발자 가이드 - MQTT 프로토콜을 사용하여 Azure IoT DPS(디바이스 프로비저닝 서비스) 디바이스 지향 엔드포인트에 연결하는 디바이스에 대한 지원입니다.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: d13bdc5bb98159d5a267a821f0431bed622e5e11
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535749"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>MQTT 프로토콜을 사용하여 DPS와 통신

DPS를 사용하면 디바이스가 다음을 사용하여 DPS 디바이스 엔드포인트와 통신할 수 있습니다.

* 포트 8883에서 [MQTT v3.1.1](https://mqtt.org/)
* 포트 443에서 WebSocket을 통해 [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127)

DPS는 모든 기능을 갖춘 MQTT broker가 아니며 MQTT v3.1.1 표준에 지정된 모든 동작을 지원하지는 않습니다. 이 문서에서는 디바이스가 지원되는 MQTT 동작을 사용하여 DPS와 통신하는 방법을 설명합니다.

DPS와 통신하는 모든 디바이스는 TLS/SSL을 사용하여 보호되어야 합니다. 따라서 DPS는 1883 포트를 통한 비보안 연결을 지원하지 않습니다.

 > [!NOTE] 
 > DPS는 현재 MQTT 프로토콜을 통해 TPM [증명 메커니즘](./concepts-service.md#attestation-mechanism)을 사용하는 디바이스를 지원하지 않습니다.

## <a name="connecting-to-dps"></a>DPS에 연결

디바이스는 다음 옵션을 통해 MQTT 프로토콜을 사용하여 DPS에 연결할 수 있습니다.

* [Azure IoT Provisioning SDK](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks)의 라이브러리.
* 또는 MQTT 프로토콜 직접 사용.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT 프로토콜 직접 사용(디바이스로)

디바이스가 디바이스 SDK를 사용할 수 없는 경우라도 포트 8883에서 MQTT 프로토콜을 사용하는 공용 디바이스 엔드포인트에 연결할 수 있습니다. **CONNECT** 패킷에서 디바이스는 다음 값을 사용해야 합니다.

* **ClientId** 필드에 **registrationId** 를 사용합니다.

* **Username** 필드에 `{idScope}/registrations/{registration_id}/api-version=2019-03-31`을 사용합니다. 여기서 `{idScope}`는 DPS의 [idScope](./concepts-service.md#id-scope)입니다.

* **암호** 필드에는 SAS 토큰을 사용합니다. SAS 토큰의 형식은 HTTPS 및 AMQP 프로토콜에 대해 동일합니다.

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` resourceURI는 `{idScope}/registrations/{registration_id}` 형식이어야 합니다. 정책 이름은 `registration`이어야 합니다.

  > [!NOTE]
  > X.509 인증서 인증을 사용하는 경우 SAS 토큰 암호는 필요하지 않습니다.

  SAS 토큰을 생성하는 방법에 대한 자세한 내용은 [DPS에 대한 액세스 제어](how-to-control-access.md#security-tokens)의 보안 토큰 섹션을 참조하세요.

다음은 DPS 구현 관련 동작의 목록입니다.

 * DPS는 **0** 으로 설정되는 **CleanSession** 플래그의 기능을 지원하지 않습니다.

 * 디바이스 앱이 **QoS 2** 의 토픽을 구독하는 경우, DPS는 **SUBACK** 패킷에서 최대 QoS level 1을 부여합니다. 그런 다음 DPS는 메시지를 QoS 1을 사용하는 디바이스에 전달합니다.

## <a name="tlsssl-configuration"></a>TLS/SSL 구성

MQTT 프로토콜을 직접 사용하려면 클라이언트가 *반드시* TLS 1.2를 통해 연결되어야 합니다. 이 단계를 건너뛰려고 하면 연결 오류가 발생하여 실패합니다.


## <a name="registering-a-device"></a>디바이스 등록

DPS를 통해 디바이스를 등록하려면 디바이스가 `$dps/registrations/res/#`을 **토픽 필터** 로 사용하여 구독해야 합니다. 토픽 필터에 다중 레벨 와일드카드 `#`는 디바이스가 토픽 이름에 추가 속성을 수신하도록 하려는 경우에만 사용됩니다. DPS는 하위 토픽의 필터링을 위한 `#` 또는 `?` 와일드카드의 사용을 허용하지 않습니다. DPS는 범용 발행-구독 메시징 브로커가 아니므로 문서화된 토픽 이름 및 토픽 필터만 지원합니다.

디바이스는 `$dps/registrations/PUT/iotdps-register/?$rid={request_id}`를 **토픽 이름** 으로 사용하여 DPS에 등록 메시지를 게시해야 합니다. 페이로드에는 JSON 형식의 [디바이스 등록](/rest/api/iot-dps/device/runtime-registration/register-device) 개체가 포함되어야 합니다.
성공적인 시나리오라면 디바이스는 `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` 토픽 이름에 대한 응답을 수신합니다. 여기서 x는 초 단위의 retry-after 값입니다. 응답의 페이로드에는 JSON 형식의 [RegistrationOperationStatus](/rest/api/iot-dps/device/runtime-registration/register-device#registrationoperationstatus) 개체가 포함됩니다.

## <a name="polling-for-registration-operation-status"></a>등록 작업 상태 폴링

디바이스는 디바이스 등록 작업의 결과를 수신하기 위해 서비스를 주기적으로 폴링해야 합니다. 디바이스가 위에 나타난 대로 이미 `$dps/registrations/res/#` 토픽을 구독했다고 가정하면 `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` 토픽 이름에 get operationstatus 메시지를 게시할 수 있습니다. 이 메시지의 작업 ID는 이전 단계의 RegistrationOperationStatus 응답 메시지에서 받은 값이어야 합니다. 성공한 경우 서비스는 `$dps/registrations/res/200/?$rid={request_id}` 토픽에 대해 응답합니다. 응답의 페이로드에는 RegistrationOperationStatus 개체가 포함됩니다. 재시도 기간과 동일한 기간의 지연 후에 응답 코드가 202인 경우 디바이스는 서비스를 계속 폴링해야 합니다. 서비스가 200 상태 코드를 반환하면 디바이스 등록 작업이 성공한 것입니다.

## <a name="connecting-over-websocket"></a>Websocket을 통한 연결
Websocket을 통해 연결할 때 하위 프로토콜을 `mqtt`로 지정합니다. [RFC 6455](https://tools.ietf.org/html/rfc6455)를 따릅니다.

## <a name="next-steps"></a>다음 단계

MQTT 프로토콜에 관한 자세한 내용은 [MQTT 설명서](https://mqtt.org/)를 참조하세요.

DPS의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT DPS 정보](about-iot-dps.md)
