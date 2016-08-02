<properties
 pageTitle="IT 전문가를 위한 Azure IoT Hub 정보 | Microsoft Azure"
 description="포트 요구 사항 및 보안 배경과 같이 IT 전문가가 Azure IoT Hub 작업을 수행하도록 지원하는 정보입니다."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="dobett"/>

# IoT Hub에 대한 액세스 구성 및 관리

이 문서는 IoT 장치가 네트워크 인프라의 IoT Hub와 통신하는 환경을 구성하도록 IT 전문가에게 정보를 제공합니다.

## 네트워크 연결

장치는 다양한 프로토콜을 사용하여 Azure에서 IoT Hub와 통신할 수 있습니다. 일반적으로 프로토콜은 솔루션의 특정 요구 사항에 따라 선택됩니다. 다음 표에는 특정 프로토콜을 사용할 수 있는 장치에 대해 열려 있어야 하는 아웃바운드 포트가 나와 있습니다.

| 프로토콜 | 포트 |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| Websocket 통한 AMQP | 443 |
| MQTT | 8883 |
| LWM2M(장치 관리) | 5684 |

Azure 지역에 IoT Hub를 만들면 허브는 해당 허브의 수명 동안 동일한 IP 주소를 유지합니다. 그러나 서비스 품질을 유지하기 위해 Microsoft가 IoT Hub를 다른 배율 단위로 이동하는 경우에는 새 IP 주소로 할당됩니다.

## IoT Hub 및 보안

IoT Hub에 등록된 장치만 해당 IoT Hub와 통신할 수 있습니다. 등록된 장치에 *DeviceConnect* 권한을 부여해야 합니다. 장치가 각 요청에서 장치의 고유한 ID를 캡슐화하는 토큰을 포함하여 자체적으로 식별하면, 허브는 토큰의 유효성과 장치에 블랙리스트가 있는지 확인합니다(*DeviceConnect* 권한 해지). IoT Hub에서 지원하는 토큰에 대한 정보는 [IoT Hub 보안 토큰 및 X.509 인증서 사용][lnk-tokens]을 참조하세요.

또한 IoT Hub에 있는 다른 관리 끝점에 대한 액세스는 권한 집합(*iothubowner*, *service*, *registryRead* 및 *registryReadWrite*)을 통해 제어합니다. IoT Hub에 연결하는 모든 클라이언트 관리 응용 프로그램은 적절한 권한을 가진 토큰을 포함해야 합니다.

## 다음 단계

이 문서는 IT 전문가 및 개발자를 위해 개발 및 테스트 환경을 구성하는 특정 정보를 포함합니다. [Azure IoT Hub 개발자 가이드의 보안 섹션][lnk-devguide]에서 IoT Hub의 토큰 및 권한 시스템에 대한 추가 정보를 제공합니다.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [솔루션 디자인][lnk-design]
- [개발자 가이드][lnk-devguide]
- [샘플 UI를 사용하여 장치 관리 탐색][lnk-dmui]
- [Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]

[lnk-devguide]: iot-hub-devguide.md#security

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-tokens]: iot-hub-sas-tokens.md

<!---HONumber=AcomDC_0720_2016-->