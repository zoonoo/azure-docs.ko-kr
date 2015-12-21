<properties
 pageTitle="Azure IoT Hub를 Azure 이벤트 허브에 비교 | Microsoft Azure"
 description="기능 차이점 및 사용 사례를 강조 표시하는 Azure IoT Hub 및 Azure 이벤트 허브 서비스 비교입니다."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# IoT Hub 및 이벤트 허브의 비교

Azure IoT Hub의 주요 사용 사례 중 하나는 장치에서 원격 분석을 수집하는 것입니다. 이러한 이유로, IoT Hub는 짧은 대기 시간 및 높은 안정성으로 이벤트 및 원격 분석을 엄청난 규모의 클라우드에 제공하는 이벤트처리 서비스인 [이벤트 허브][]와 자주 비교됩니다.

하지만 서비스에는 다음 섹션에서 자세히 설명하는 많은 차이점이 있습니다.

| 영역 | IoT Hub | 이벤트 허브(영문) |
| ---- | ------- | ---------- |
| 통신 패턴 | 장치-클라우드 이벤트 수신 및 클라우드-장치 메시징. | 이벤트 수신만(일반적으로 장치-클라우드 시나리오에 대해 고려됨). |
| 보안 | 장치 단위 ID 및 취소 가능한 액세스 제어. [IoT Hub 개발자 가이드 - 보안]을 참조하세요. | [게시자의 정책][Event Hub publisher policies]을 사용한 제한된 해지 지원을 포함하는 이벤트 허브 전반의 [공유 액세스 정책][Event Hub - security]입니다. IoT 솔루션의 컨텍스트에서는 보통 장치당 자격 증명 및 스푸핑 방지 측정을 지원하기 위해서는 사용자 지정 솔루션을 구현해야 합니다. |
| 확장 | IoT Hub는 수백만 대의 연결된 장치를 동시에 지원하도록 최적화됩니다. | 이벤트 허브에서는 [서비스 버스 할당량][]당 최대 5,000 AMQP 연결까지 더 제한된 수의 동시 연결을 지원할 수 있습니다. 반면에 이벤트 허브를 통해 사용자는 전송된 각 메시지에 파티션을 지정할 수 있습니다. |
| 장치 SDK | IoT Hub는 다양한 플랫폼 및 언어에 대한 [장치 SDK][Azure IoT Hub SDKs]를 제공합니다. | 이벤트 허브는 .NET, C에서 지원되고 AMQP 및 HTTP 송신 인터페이스를 제공합니다. |

요약하면 유일한 사용 사례가 장치-클라우드 원격 분석 수신인 경우에도 IoT Hub는 IoT 장치 연결을 위해 특별히 설계된 서비스를 제공하며 IoT 특정 기능과 함께 이러한 시나리오에 대한 가치 제안을 지속적으로 확장합니다. 이벤트 허브는 데이터 센터 내부 및 데이터 센터 간 시나리오 컨텍스트에서 대규모 이벤트 수신용으로 설계되었습니다.

IoT Hub 및 이벤트 허브를 함께 사용하는 것이 일반적이며 전자는 장치-클라우드 통신을 처리하고 후자는 실시간 처리 엔진으로 후속 단계 이벤트 수신을 처리합니다.

## 다음 단계

Azure IoT Hub에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [IoT Hub Azure란?][]

[이벤트 허브]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT Hub 개발자 가이드 - 보안]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[서비스 버스 할당량]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Azure란?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1210_2015-->