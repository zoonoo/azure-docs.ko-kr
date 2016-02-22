<properties
 pageTitle="IoT Hub MQTT 지원 | Microsoft Azure"
 description="MQTT 설명은 IoT Hub 수준에서 지원합니다."
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# IoT Hub MQTT 지원

IoT Hub를 사용하면 장치는 [MQTT v3.1.1][lnk-mqtt-org] 프로토콜을 사용하여 IoT Hub 장치 끝점과 통신할 수 있습니다.

## IoT Hub에 연결

장치는 [Microsoft Azure IoT SDK][lnk-device-sdks]의 라이브러리를 사용하거나 MQTT 프로토콜을 직접 사용하여 MQTT 프로토콜을 사용하는 IoT Hub에 연결할 수 있습니다.

## 장치 클라이언트 SDK 사용

Java, Node.js, C 및 C#에 사용할 수 있는 MQTT 프로토콜을 지원하는 [장치 클라이언트 SDK][lnk-device-sdks]입니다. 장치 클라이언트 SDK는 표준 IoT Hub 연결 문자열을 사용하여 IoT Hub에 연결합니다. MQTT 프로토콜을 사용하려면 클라이언트 프로토콜 매개 변수를 **MQTT**에 설정해야 합니다. 기본적으로는 장치 클라이언트 SDK는 **CleanSession** 플래그가 **0**에 설정된 IoT Hub에 연결되고 **QoS 1**을 사용하여 IoT Hub와 메시지를 교환합니다.

장치가 IoT Hub에 연결된 경우 장치 클라이언트 SDK는 IoT Hub와 메시지를 주고 받는 장치를 사용할 수 있는 메서드를 제공합니다.

다음 테이블에는 지원되는 각 언어에 대한 코드 샘플에 대 한 링크가 있고 MQTT 프로토콜을 사용하여 IoT Hub에 연결하는 데 사용하는 매개 변수를 지정합니다.

| 언어 | 프로토콜 매개 변수 |
| -------------------------- | ------------------------- |
| [Node.JS][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## MQTT 프로토콜 직접 사용

장치가 장치 클라이언트 SDK를 사용할 수 없는 경우라도 MQTT 프로토콜을 사용하는 공용 장치 끝점에 연결할 수 있습니다. **CONNECT** 패킷에서 장치는 다음 값을 사용해야 합니다.

- **ClientId**에**deviceId** 사용.
- **Username** 필드의 `{iothubhostname}/{device_id}`, 여기서 {iothubhostname}는 IoT Hub의 전체 CName입니다(예: contoso.azure-devices.net).
- **암호** 필드에 SAS 토큰 사용. [SAS 토큰의 형식][lnk-iothub-security]은 HTTP 및 AMQP 프로토콜(<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`)에서 설명된 것과 동일합니다.

MQTT 연결 및 분리 패킷의 경우, IoT Hub는 **작업 모니터링** 채널의 이벤트를 발행합니다.

### IoT Hub에 메시지 보내기

장치가 성공적으로 연결되면 **항목 이름**으로 `devices/{did}/messages/events/` 또는 `devices/{did}/messages/events/{property_bag}`을(를) 사용하는 IoT Hub에 메시지를 보낼 수 있습니다 `{property_bag}` 요소는 URL 인코딩 형식의 속성을 추가하여 메시지를 보내는 장치를 사용할 수 있습니다. 예:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```
 
> [AZURE.NOTE] 이는 HTTP 프로토콜의 쿼리 문자열에 사용되는 것과 동일한 인코딩입니다.

장치 클라이언트 응용 프로그램은 또한 `devices/{did}/messages/events/{property_bag}`을(를) **Will 항목 이름**으로 사용하여 원격 분석 메시지로서 전달할 *Will 메시지*를 정의할 수 있습니다.

### 메시지 수신

IoT Hub에서 메시지를 수신하려면 장치는 **항목 필터**로서 `devices/{did}/messages/devicebound/#”`(을)를 사용하여 구독해야 합니다. 메시지 속성이 있는 경우 IoT Hub는 **항목 이름** `devices/{did}/messages/devicebound/` 또는 `devices/{did}/messages/devicebound/{property_bag}`와(과) 함께 메시지를 전달합니다. `{property_bag}`에는 메시지 속성의 URL 인코딩된 키/값 쌍이 있습니다. 응용 프로그램 속성 및 사용자 설정 가능 시스템 속성만(예: **messageId** 또는 **correlationId**) 속성 모음에 포함됩니다. 시스템 속성 이름에는 접두사 **$**가 있고, 응용 프로그램 속성은 접두사가 없는 원래 속성 이름을 사용합니다.

## 다음 단계

IoT Hub와 통신하는 장치 클라이언트 SDK를 사용하는 방법에 대한 자세한 내용은 [Azure IoT Hub 시작][lnk-iot-get-stated]을 참조하세요.

MQTT 프로토콜에 대한 자세한 내용은 [MQTT 설명서][lnk-mqtt-docs]를 참조하세요.

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples

<!---HONumber=AcomDC_0211_2016-->