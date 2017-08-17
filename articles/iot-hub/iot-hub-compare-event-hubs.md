---
title: "Azure IoT Hub와 Azure Event Hubs 비교 | Microsoft 문서"
description: "IoT Hub 및 Event Hubs Azure의 비교는 강조 표시된 기능 차이 및 사용 사례를 제공합니다. 비교는 지원되는 프로토콜, 장치 관리, 모니터링 및 파일 업로드를 포함합니다."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: elioda
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 12389b4be03b714d99edcf3fd621bd2d9c586540
ms.contentlocale: ko-kr
ms.lasthandoff: 08/10/2017

---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT Hub 및 Azure Event Hubs의 비교
IoT Hub의 주요 사용 사례 중 하나는 장치에서 원격 분석을 수집하는 것입니다. 이러한 이유로 IoT Hub를 [Azure Event Hubs][Azure Event Hubs]와 자주 비교합니다. IoT Hub와 마찬가지로 Event Hubs는 짧은 대기 시간 및 높은 안정성으로 엄청난 규모의 클라우드에 이벤트 및 원격 분석을 가능하게 하는 이벤트 처리 서비스입니다.

하지만 서비스에는 다음 표에서 자세히 설명하는 많은 차이점이 있습니다.

| 영역 | IoT 허브 | Event Hubs |
| --- | --- | --- |
| 통신 패턴 | [장치-클라우드 통신][lnk-d2c-guidance](메시징, 파일 업로드 및 reported 속성) 및 [클라우드-장치 통신][lnk-c2d-guidance](직접 메서드, desired 속성, 메시징)을 사용합니다. |이벤트 수신만(일반적으로 장치-클라우드 시나리오에 대해 고려됨)만 가능합니다. |
| 장치 상태 정보 | [장치 쌍][lnk-twins]은 장치 상태 정보를 저장하고 쿼리할 수 있습니다. | 어떤 장치 상태 정보도 저장할 수 없습니다. |
| 장치 프로토콜 지원 |MQTT, WebSocket을 통한 MQTT, AMQP, WebSocket을 통한 AMQP 및 HTTP를 지원합니다. 또한 IoT Hub는 사용자 지정 가능한 프로토콜 게이트웨이 구현인 [Azure IoT 프로토콜 게이트웨이][lnk-azure-protocol-gateway]에서 작동하여 사용자 지정 프로토콜을 지원합니다. |AMQP, WebSocket을 통한 AMQP 및 HTTP를 지원합니다. |
| 보안 |장치 단위 ID 및 취소 가능한 액세스 제어를 제공합니다. [IoT Hub 개발자 가이드의 보안 섹션]을 참조하세요. |[게시자의 정책][Event Hubs publisher policies]을 통해 제한된 해지 지원을 포함하는 Event Hubs 전반의 [공유 액세스 정책][Event Hubs - security]을 제공합니다. IoT 솔루션은 종종 장치당 자격 증명 및 스푸핑 방지 측정을 지원하기 위해 사용자 지정 솔루션을 구현해야 합니다. |
| 작업 모니터링 |IoT 솔루션을 사용하여 다양한 장치 ID 관리와 개별 인증 오류, 제한 및 잘못된 형식 예외 등의 연결 이벤트를 구독할 수 있습니다. 이러한 이벤트를 통해 개별 장치 수준에서 연결 문제를 신속 하게 식별할 수 있습니다. |집계 메트릭만 표시합니다. |
| 확장 |수백만 대의 연결된 장치를 동시에 지원하도록 최적화됩니다. |[Azure Event Hubs 할당량][Azure Event Hubs quotas]에 따라 연결을 계측합니다. 반면에 이벤트 허브를 통해 사용자는 전송된 각 메시지에 파티션을 지정할 수 있습니다. |
| 장치 SDK |직접 MQTT, AMQP 및 HTTP API 외에도 다양한 플랫폼 및 언어에 대해 [장치 SDK][Azure IoT SDKs]를 제공합니다. |AMQP 및 HTTP 전송 인터페이스 외에도 .NET, Java 및 C에서 지원됩니다. |
| 파일 업로드 |IoT 솔루션을 사용하여 장치에서 클라우드로 파일을 업로드합니다. 워크플로 통합에 대한 파일 알림 끝점과 디버깅 지원에 대한 작업 모니터링 범주를 포함합니다. | 지원되지 않습니다. |
| 메시지를 여러 끝점으로 라우팅 | 최대 10개의 사용자 지정 끝점이 지원됩니다. 규칙은 메시지가 사용자 지정 끝점으로 라우팅되는 방식을 결정합니다. 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]를 참조하세요. | 메시지 디스패치를 위해서는 추가 코드를 쓰고 호스트해야 합니다. |

요약하면 유일한 사용 사례가 장치-클라우드 원격 분석 수신인 경우에도 IoT Hub는 IoT 장치 연결을 위해 설계된 서비스를 제공합니다. IoT 특정 기능을 통해 이러한 시나리오에 대한 가치 제안을 지속적으로 확장합니다. 이벤트 허브는 데이터 센터 내부 및 데이터 센터 간 시나리오 컨텍스트에서 대규모 이벤트 수신용으로 설계되었습니다.

동일한 솔루션에서 IoT Hub와 Event Hubs를 모두 사용하는 경우는 드뭅니다. IoT Hub는 장치-클라우드 통신을 처리하고 Event Hubs는 후속 단계 이벤트 수신을 실시간 처리 엔진으로 처리합니다.

### <a name="next-steps"></a>다음 단계
IoT Hub 배포를 계획하는 방법에 대한 자세한 내용은 [크기 조정, HA 및 DR][lnk-scaling]을 참조하세요.

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [IoT Edge에서 장치 시뮬레이션][lnk-iotedge]

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT Hub 개발자 가이드의 보안 섹션]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-features.md#event-publishers
[Azure Event Hubs quotas]: ../event-hubs/event-hubs-quotas.md
[Azure IoT SDKs]: https://github.com/Azure/azure-iot-sdks
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md

