<properties
 pageTitle="Azure IoT Hub 크기 조정 | Microsoft Azure"
 description="Azure IoT Hub의 크기를 조정하는 방법을 설명합니다."
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

# IoT Hub 크기 조정

Azure IoT Hub는 IoT Hub S1 계층 단위 또는 S2 계층 단위 수를 2,000개까지 늘려 동시 연결 장치 수를 수백만 대까지 지원할 수 있습니다. 자세한 내용은 [IoT Hub 가격 책정][lnk-pricing]을 참조하세요.

각 IoT Hub 단위는 레지스트리에 특정 개수의 장치를 허용하고 이러한 장치는 모두 동시에 연결할 수 있습니다. 또한 각 단위는 여러 개의 일일 메시지를 허용합니다.

솔루션의 규모를 적절히 조정하기 위해 IoT Hub의 특정 용도 고려해야 합니다. 특히 다음과 같은 범주의 작업에 필요한 최대 처리량을 고려해야 합니다.

* 장치-클라우드 메시지
* 클라우드-장치 메시지
* ID 레지스트리 작업

이 처리량 정보 외에도 [IoT Hub 할당량 및 제한][]을 참조하고 솔루션을 적절히 디자인하세요.

## 장치-클라우드 및 클라우드-장치 메시지 처리량

IoT Hub 솔루션의 크기를 조정하는 가장 적절한 방법은 장치별로 트래픽을 평가하는 것입니다.

장치-클라우드 메시지는 다음과 같은 지속적인 처리량 지침을 따릅니다.

| 계층 | 지속적인 처리량 | 지속적인 전송 속도 |
| ---- | -------------------- | ------------------- |
| S1 | 장치당 최대 8KB/시간 | 장치당 평균 4메시지/시간 |
| S2 | 장치당 최대 4KB/분 | 장치당 평균 2메시지/분 |

장치-클라우드 메시지를 수신할 때 응용 프로그램 백 엔드는 다음과 같은 최대 처리량(모든 판독기)을 예상할 수 있습니다.

| 계층 | 지속적인 처리량 |
| ---- | -------------------- |
| S1 | 장치당 최대 120KB/분, 최소 2MB/초 |
| S2 | 장치당 최대 4MB/분, 최소 2MB/초 |

클라우드-장치 메시지 성능은 장치별로 조정되며 각 장치는 분당 최대 5개 메시지를 수신합니다.

## ID 레지스트리 작업 처리량

IoT Hub ID 레지스트리 작업은 대부분이 장치 프로비전과 관련되므로 런타임 작업으로 간주되지 않습니다.

관련 버스트 성능 수치는 [IoT Hub 할당량 및 제한][]을 참조하세요.

## 분할

단일 IoT Hub를 수백만 대의 장치로 확장할 수 있기는 하지만, 솔루션에서 단일 IoT Hub에서 보장할 수 없는 특정 성능 특성을 필요로 하는 경우가 있습니다. 이 경우 여러 IoT Hub로 장치를 분할하는 것이 좋습니다. 여러 IoT Hub가 갑작스러운 트래픽 증가를 원활하게 처리하고 필요한 처리량 또는 필요한 작업 속도를 얻습니다.

## 다음 단계

Azure IoT Hub에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [IoT Hub 시작(자습서)][lnk-get-started]
- [IoT Hub Azure란?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub 할당량 및 제한]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Azure란?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_1223_2015-->