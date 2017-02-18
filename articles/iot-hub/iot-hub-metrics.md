---
title: "메트릭을 사용하여 Azure IoT Hub 모니터링 | Microsoft Docs"
description: "Azure IoT Hub 메트릭을 사용하여 IoT Hub의 전반적인 상태를 평가하고 모니터링하는 방법입니다."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>IoT Hub 메트릭
IoT Hub 메트릭은 Azure 구독의 Azure IoT 리소스 상태에 대한 더 나은 데이터를 제공합니다. IoT Hub 메트릭을 통해 IoT Hub 서비스와 연결된 장치의 전반적인 상태를 평가할 수 있습니다. 사용자 측 통계는 Azure 지원 센터에 문의할 필요 없이 IoT Hub의 상황을 파악하고 근본 원인을 해결할 수 있기 때문에 중요합니다.

메트릭은 기본적으로 사용하도록 설정됩니다. Azure Portal에서 IoT Hub 메트릭을 볼 수 있습니다.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub 메트릭을 보는 방법
1. IoT Hub를 만듭니다. IoT Hub를 만드는 방법에 관한 지침은 [시작][lnk-get-started] 가이드에서 확인할 수 있습니다.
2. IoT Hub의 블레이드를 엽니다. 여기서 **메트릭**을 클릭합니다.
   
    ![][1]
3. 메트릭 블레이드에서 IoT Hub에 대한 메트릭을 보고 메트릭의 사용자 지정 뷰를 만들 수 있습니다. **진단 설정**을 클릭하여 Event Hubs 끝점 또는 Azure Storage 계정으로 메트릭 데이터를 보낼 수 있습니다.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub 메트릭 및 사용 방법
IoT Hub는 허브의 상태와 연결된 장치의 총 수에 대한 개요를 제공하는 몇 가지 메트릭을 제공합니다. IoT Hub 상태의 큰 그림을 그리기 위해 여러 가지 메트릭의 정보를 결합할 수 있습니다. 다음 테이블에는 각 IoT Hub가 추적하는 메트릭과 각 메트릭이 IoT hub의 전반적인 상태와 어떤 관련성이 있는지 설명합니다.

| 메트릭 | 메트릭 설명 | 메트릭 용도 |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | 모든 장치에서 전송된 메시지의 수 | 메시지 전송에 대한 개요 데이터 |
| d2c.telemetry.ingress.success | 허브 내 모든 성공한 메시지의 수 | 허브에 성공적으로 수신된 메시지의 개요 |
| d2c.telemetry.egress.success | 끝점에 대한 쓰기가 모두 성공한 수 | 사용자의 경로에 따른 메시지 팬아웃 개요 |
| d2c.telemetry.egress.invalid | 끝점과 호환되지 않아서 배달되지 않은 메시지 수 | 사용자의 끝점 집합에 쓰기가 실패하는 횟수에 대한 개요입니다. 값이 높으면 끝점이 잘못 구성된 것일 수 있습니다. |
| d2c.telemetry.egress.dropped | 끝점이 비정상적이어서 삭제된 메시지 수 | 현재 IoT Hub 구성에서 얼마나 많은 메시지가 삭제되었는지에 대한 개요 |
| d2c.telemetry.egress.fallback | 대체 경로와 일치하는 메시지 수 | 모든 메시지를 기본 제공 끝점이 아닌 다른 끝점으로 보내는 사용자의 경우 이 메트릭은 라우팅 설정의 차이를 보여줍니다. |
| d2c.telemetry.egress.orphaned | 대체 경로를 포함하여 경로가 일치하지 않는 메시지 수 | 현재 IoT Hub 구성에서 얼마나 많은 메시지가 분리되는지에 대한 개요 |
| d2c.endpoints.latency.eventHubs | IoT Hub에 대한 메시지 수신과 이벤트 허브 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초) | 그 차이는 사용자가 잘못된 끝점 구성을 식별하는 데 도움을 줍니다. |
| d2c.endpoints.latency.serviceBusQueues | IoT Hub에 대한 메시지 수신과 Service Bus 큐 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초) | 그 차이는 사용자가 잘못된 끝점 구성을 식별하는 데 도움을 줍니다. |
| d2c.endpoints.latency.serviceBusTopic | IoT Hub에 대한 메시지 수신과 Service Bus 토픽 끝점에 대한 메시지 수신 간의 평균 대기 시간(밀리초) | 그 차이는 사용자가 잘못된 끝점 구성을 식별하는 데 도움을 줍니다. |
| d2c.endpoints.latency.builtIn.events | IoT Hub에 대한 메시지 수신과 기본 제공 끝점(메시지.이벤트)에 대한 메시지 수신 간의 평균 대기 시간(밀리초) | 그 차이는 사용자가 잘못된 끝점 구성을 식별하는 데 도움을 줍니다. |
| c2d.commands.egress.complete.success | 모든 장치의 수신 장치에 의해 완료된 모든 명령 메시지의 수 |중단 및 거부에 대한 메트릭과 함께 전반적인 클라우드-장치 명령 성공률에 관한 개요 제공 |
| c2d.commands.egress.abandon.success | 모든 장치의 수신 장치에 의해 성공적으로 중단된 모든 메시지의 수 |메시지가 예상보다 자주 중단되는 경우 잠재적인 문제 강조 표시 |
| c2d.commands.egress.reject.success | 모든 장치의 수신 장치에 의해 성공적으로 거부된 모든 메시지의 수 |메시지가 예상보다 자주 거부되는 경우 잠재적인 문제 강조 표시 |
| devices.totalDevices | IoT Hub에 등록된 장치 수 |허브에 등록된 장치 수 |
| devices.connectedDevices.allProtocol | 동시에 연결된 장치 수 |허브에 연결된 장치 수 개요 |

## <a name="next-steps"></a>다음 단계
지금까지 IoT Hub 메트릭의 개요를 살펴보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [작업 모니터링][lnk-monitor]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [IoT Gateway SDK를 사용하는 장치 시뮬레이션][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


