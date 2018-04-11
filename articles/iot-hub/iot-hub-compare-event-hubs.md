---
title: Azure IoT Hub와 Azure Event Hubs 비교 | Microsoft 문서
description: IoT Hub 및 Event Hubs Azure의 비교는 강조 표시된 기능 차이 및 사용 사례를 제공합니다. 비교는 지원되는 프로토콜, 장치 관리, 모니터링 및 파일 업로드를 포함합니다.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Azure IoT Hub 및 Azure Event Hubs의 비교

Azure IoT Hub와 Azure Event Hubs는 대량의 데이터를 수집하고 비즈니스 관련 정보를 얻기 위해 해당 데이터를 처리 또는 저장할 수 있는 클라우드 서비스입니다. 두 서비스 모두 낮은 대기 시간과 높은 안정성으로 이벤트 및 원격 분석 데이터 처리를 지원한다는 점에서 비슷합니다. 그러나 IoT Hub만이 다양한 규모의 사물 인터넷 시나리오를 지원하는 데 필요한 특정 기능을 사용하여 개발되었습니다. 

Azure IoT Hub는 장치를 연결하고 비즈니스 관련 정보 및 자동화에 대한 데이터를 수집하는 클라우드 게이트웨이입니다. 손쉽게 클라우드로 데이터를 스트리밍하고 규모에 따라 장치를 관리할 수 있습니다. IoT Hub와 다른 데이터 수집 서비스의 중요한 차이점으로 IoT Hub는 장치와 백 엔드 시스템 사이의 관계를 풍부하게 만들어주는 기능을 제공합니다. 양방향 통신 기능을 제공하므로 장치에서 데이터를 수신하는 동안 장치로 메시지를 보내 속성을 업데이트하거나 작업을 호출할 수 있습니다. 장치 수준 ID는 시스템 보안을 도와줍니다. 분산 컴퓨팅은 클라우드 서비스 논리를 에지 장치로 이동합니다.

[Azure Event Hubs][Azure Event Hubs]는 대량의 데이터와 원격 분석을 처리하고 저장할 수 있는 이벤트 수집 서비스입니다. Event Hubs는 데이터 센터 간 및 내부 데이터 센터 시나리오의 맥락에서 대규모로 이벤트를 수집하기 위해 설계되었지만, IoT Hub에서 제공하는 풍부한 IoT 관련 기능을 제공하지 않습니다. 이러한 이유로, IoT 솔루션에는 Event Hubs를 권장하지 않습니다. 

다음 표는 IoT 기능을 평가할 때 IoT Hub의 두 계층을 Event Hubs와 비교한 상세 정보를 제공합니다. IoT Hub 기본 및 표준 계층에 대한 자세한 내용은 [적합한 IoT Hub 계층 선택 방법][lnk-scaling]을 참조하세요.

| IoT 기능 | IoT Hub 표준 계층 | IoT Hub 기본 계층 | Event Hubs |
| --- | --- | --- | --- |
| 장치-클라우드 메시징 | ![확인][1] | ![확인][1] | ![확인][1] |
| 프로토콜: websocket을 통한 HTTPS, AMQP, AMQP | ![확인][1] | ![확인][1] | ![확인][1] |
| 프로토콜: websocket을 통한 MQTT, MQTT | ![확인][1] | ![확인][1] |  |
| 장치 단위 ID | ![확인][1] | ![확인][1] |  |
| 장치에서 파일 업로드 | ![확인][1] | ![확인][1] |  |
| Device Provisioning Service | ![확인][1] | ![확인][1] |  |
| 클라우드-장치 메시징 | ![확인][1] |  |  |
| 장치 쌍 및 장치 관리 | ![확인][1] |  |  |
| IoT Edge | ![확인][1] |  |  |

유일한 사용 사례가 장치-클라우드 데이터 수집인 경우에도 IoT 장치 연결을 위해 설계된 서비스를 제공하는 IoT Hub를 사용하실 것을 강력하게 권장합니다. 

### <a name="next-steps"></a>다음 단계

IoT Hub의 기능에 대해 더 알아보려면 [IoT Hub 개발자 가이드][lnk-devguide]를 참조하세요.


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png