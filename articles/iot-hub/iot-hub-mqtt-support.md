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
 ms.date="07/19/2016"
 ms.author="dobett"/>

# IoT Hub MQTT 지원

IoT Hub를 사용하면 장치는 8883 포트의 [MQTT v3.1.1][lnk-mqtt-org] 프로토콜을 사용하여 IoT Hub 장치 끝점과 통신할 수 있습니다. IoT Hub는 TLS/SSL을 사용하여 모든 장치 통신이 보호되어야 합니다.

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
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

## MQTT 프로토콜 직접 사용

장치가 장치 클라이언트 SDK를 사용할 수 없는 경우라도 MQTT 프로토콜을 사용하는 공용 장치 끝점에 연결할 수 있습니다. **CONNECT** 패킷에서 장치는 다음 값을 사용해야 합니다.

- **ClientId** 필드에 **deviceId**를 사용합니다.
- **Username** 필드에 `{iothubhostname}/{device_id}`를 사용합니다. 여기서 {iothubhostname}는 IoT Hub의 전체 CName입니다.

    예를 들어, IoT Hub의 이름이 **contoso.azure devices.net**이고 장치의 이름이 **MyDevice01**이면 전체 **Username** 필드에 `contoso.azure-devices.net/MyDevice01`이 포함되어야 합니다.

- **암호** 필드에는 SAS 토큰을 사용합니다. SAS 토큰의 형식은 HTTP 및 AMQP 프로토콜에 대해 동일합니다.<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

    SAS 토큰을 생성하는 방법에 대한 자세한 내용은 [IoT Hub 보안 토큰 사용][lnk-sas-tokens]의 장치 섹션을 참조하세요.
    
    테스트할 때 [장치 탐색기][lnk-device-explorer] 도구를 사용하여 복사한 후 자체 코드에 붙여넣을 수 있는 SAS 토큰을 빠르게 생성할 수도 있습니다.
    
    1. 장치 탐색기의 **관리** 탭으로 이동합니다.
    2. **SAS 토큰**(오른쪽 위)을 클릭합니다.
    3. **SASTokenForm**의 **DeviceID** 드롭다운에서 장치를 선택합니다. **TTL**을 설정합니다.
    4. **생성**을 클릭하여 토큰을 만듭니다.
    
    생성되는 SAS 토큰은 다음과 같습니다. `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

    이 토큰에서 MQTT를 사용하여 연결할 때 **암호** 필드에서와 같이 사용하는 부분은 다음과 같습니다. `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`

MQTT 연결 및 분리 패킷의 경우, IoT Hub는 **작업 모니터링** 채널의 이벤트를 발행합니다.

### IoT Hub에 메시지 보내기

장치가 정상적으로 연결되면 `devices/{device_id}/messages/events/` 또는 `devices/{device_id}/messages/events/{property_bag}`를 **항목 이름**으로 사용하여 IoT Hub에 메시지를 보낼 수 있습니다. `{property_bag}` 요소는 URL 인코딩 형식의 속성을 추가하여 메시지를 보내는 장치를 사용할 수 있습니다. 예:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] 이는 HTTP 프로토콜의 쿼리 문자열에 사용되는 것과 동일한 인코딩입니다.

또한 장치 클라이언트 응용 프로그램은 `devices/{device_id}/messages/events/{property_bag}`을 **Will 항목 이름**으로 사용하여 원격 분석 메시지로서 전달할 *Will 메시지*를 정의할 수 있습니다.

### 메시지 수신

IoT Hub에서 메시지를 수신하려면 장치는 `devices/{device_id}/messages/devicebound/#”`을 **항목 필터**로 사용하여 구독해야 합니다. 메시지 속성이 있는 경우 IoT Hub는 **항목 이름** `devices/{device_id}/messages/devicebound/` 또는 `devices/{device_id}/messages/devicebound/{property_bag}`와 함께 메시지를 전달합니다. `{property_bag}`에는 메시지 속성의 URL 인코딩된 키/값 쌍이 있습니다. 응용 프로그램 속성 및 사용자 설정 가능 시스템 속성(예: **messageId** 또는 **correlationId**)만 속성 모음에 포함됩니다. 시스템 속성 이름에는 접두사 **$**가 있고, 응용 프로그램 속성은 접두사가 없는 원래 속성 이름을 사용합니다.

## 다음 단계

IoT 장치 SDK의 MQTT 지원에 대한 자세한 내용은 Azure IoT Hub 개발자 가이드에서 [MQTT 지원에 대한 참고 사항][lnk-mqtt-devguide]을 참조하세요.

MQTT 프로토콜에 대한 자세한 내용은 [MQTT 설명서][lnk-mqtt-docs]를 참조하세요.

IoT Hub 배포를 계획하는 방법에 대한 자세한 내용은 다음을 참조하세요.

- [지원되는 장치][lnk-devices]
- [추가 프로토콜 지원][lnk-protocols]
- [이벤트 허브와 비교][lnk-compare]
- [크기 조정, HA 및 DR][lnk-scaling]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [개발자 가이드][lnk-devguide]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]
- [Azure 포털을 사용하여 IoT Hub 관리][lnk-portal]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-sas-tokens.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide.md#mqtt-support

[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0720_2016-->