<properties
 pageTitle="IoT Hub 진단 메트릭"
 description="사용자가 리소스의 전반적인 상태를 평가할 수 있는 Azure IoT Hub 메트릭의 개요"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/18/2016"
 ms.author="nberdy"/>

# 진단 메트릭 소개

진단 메트릭은 구독의 Azure 리소스 상태에 관한 더 나은 데이터를 제공하는 Azure 전체 메트릭 푸시의 일부입니다. 메트릭을 통해 사용자는 서비스와 연결된 장치의 전반적인 상태를 평가할 수 있습니다. 사용자 측 통계는 Azure 지원 센터에 문의할 필요 없이 사용자가 IoT Hub의 상황을 보고 근본 원인을 해결할 수 있기 때문에 중요합니다.

Azure 포털에서 진단 메트릭을 사용할 수 있습니다.

## 진단 메트릭을 사용하는 방법

1. IoT Hub를 만듭니다. IoT Hub를 만드는 방법에 관한 지침은 [시작][lnk-get-started] 가이드에서 확인할 수 있습니다.

2. IoT Hub의 블레이드를 엽니다. 여기에서 **모든 설정**, **진단**을 차례로 클릭합니다.

    ![][1]

3. 상태를 **On**으로 설정하고 저장소 계정을 선택하여 진단 데이터를 저장함으로써 진단을 구성할 수 있습니다. 메트릭 확인란을 선택한 다음 **저장**을 누릅니다. 저장소 계정은 미리 만들어야 하고 저장소에 대한 요금이 별도로 청구됩니다.

    ![][2]

4. 진단을 설정한 후에 기본 IoT Hub 블레이드로 돌아갑니다. 메트릭 정보는 블레이드의 **모니터링** 섹션에 채워집니다. 차트를 클릭하면 IoT Hub에 대 한 메트릭 정보 요약을 보고 차트에 표시된 메트릭 선택 항목을 편집할 수 있는 메트릭 창이 열립니다. 또한 메트릭 값을 기반으로 경고를 구성할 수 있습니다.

    ![][3]

## 메트릭 및 사용 방법

IoT Hub는 허브의 상태와 연결 된 장치의 총 수에 대한 개요를 제공하는 몇 가지 메트릭을 제공합니다. IoT Hub 상태의 큰 그림을 그리기 위해 여러 가지 메트릭의 정보를 결합할 수 있습니다. 다음 테이블에는 각 IoT Hub가 추적하는 메트릭과 각 메트릭이 IoT hub의 전반적인 상태와 어떤 관련성이 있는지 설명합니다.

| 메트릭 | 메트릭 설명 | 메트릭 용도 |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | 모든 장치에서 전송된 메시지의 수 | 메시지 전송에 대한 개요 데이터 |
| d2c.telemetry.ingress.success | 허브 내 모든 성공한 메시지의 수 | 허브에 성공적으로 수신된 메시지의 개요 |
| c2d.commands.egress.complete.success | 모든 장치의 수신 장치에 의해 완료된 모든 명령 메시지의 수 | 중단 및 거부에 대한 메트릭과 함께 전반적인 C2D 명령 성공률에 관한 개요 제공 |
| c2d.commands.egress.abandon.success | 모든 장치의 수신 장치에 의해 성공적으로 중단된 모든 메시지의 수 | 메시지가 예상보다 자주 중단되는 경우 잠재적인 문제 강조 표시 |
| c2d.commands.egress.reject.success | 모든 장치의 수신 장치에 의해 성공적으로 거부된 모든 메시지의 수 | 메시지가 예상보다 자주 거부되는 경우 잠재적인 문제 강조 표시 |
| devices.totalDevices | IoT Hub에 등록된 장치의 평균, 최소 및 최대수 | 허브에 등록된 장치 수 |
| devices.connectedDevices.allProtocol | 동시에 연결된 장치의 평균, 최소 및 최대수 | 허브에 연결된 장치 수 개요 |

## 다음 단계

지금까지 진단 메트릭의 개요를 살펴보았습니다. 자세한 내용을 보려면 다음 링크를 따라가십시오.

- [IoT Hub 작업 모니터링][lnk-operations-monitoring]
- [IoT Hub 크기 조정][lnk-scaling]
- [IoT Hub 고가용성 및 재해 복구][lnk-dr]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

<!---HONumber=AcomDC_0420_2016-->