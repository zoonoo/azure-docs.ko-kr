---
title: "Azure IoT Hub 크기 조정 | Microsoft Docs"
description: "예상된 메시지 처리량을 지원하도록 IoT hub를 확장하는 방법입니다. 분할을 위한 옵션 및 각 계층에 지원되는 처리량에 대한 요약을 포함합니다."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2cb263103da05b10c24aab71d81c43eb25987565
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="scale-your-iot-hub-solution"></a>IoT Hub 솔루션 크기 조정
Azure IoT Hub는 동시에 최대 백만 개의 연결된 장치를 지원할 수 있습니다. 자세한 내용은 [IoT Hub 가격 책정][lnk-pricing]을 참조하세요. 각 IoT Hub 단위는 특정 개수의 일일 메시지를 허용합니다.

솔루션의 규모를 적절히 조정하려면 IoT Hub의 특정 용도를 고려합니다. 특히 다음과 같은 범주의 작업에 필요한 최대 처리량을 고려해야 합니다.

* 장치-클라우드 메시지
* 클라우드-장치 메시지
* ID 레지스트리 작업

이 처리량 정보 외에도 [IoT Hub 할당량 및 제한][IoT Hub quotas and throttles]을 참조하고 솔루션을 적절히 디자인하세요.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>장치-클라우드 및 클라우드-장치 메시지 처리량
IoT Hub 솔루션의 크기를 조정하는 가장 적절한 방법은 장치별로 트래픽을 평가하는 것입니다.

장치-클라우드 메시지는 다음과 같은 지속적인 처리량 지침을 따릅니다.

| 계층 | 지속적인 처리량 | 지속적인 전송 속도 |
| --- | --- | --- |
| S1 |장치당 최대 1111KB/분<br/>(1.5GB/일/장치) |장치당 평균 278메시지/분<br/>(400,000메시지/일/장치당) |
| S2 |장치당 최대 16MB/분<br/>(22.8GB/일/장치) |장치당 평균 4,167개 메시지/분<br/>(6백만 개의 메시지/일/장치당) |
| S3 |장치당 최대 814MB/분<br/>(1144.4GB/일/장치) |장치당 평균 208,333 메시지/분<br/>(3억 개의 메시지/일/장치당) |

## <a name="identity-registry-operation-throughput"></a>ID 레지스트리 작업 처리량
IoT Hub ID 레지스트리 작업은 대부분이 장치 프로비전과 관련되므로 런타임 작업으로 간주되지 않습니다.

관련 버스트 성능 수치는 [IoT Hub 할당량 및 제한][IoT Hub quotas and throttles]을 참조하세요.

## <a name="sharding"></a>분할
단일 IoT Hub를 수백만 대의 장치로 확장할 수 있기는 하지만, 솔루션에서 단일 IoT Hub에서 보장할 수 없는 특정 성능 특성을 필요로 하는 경우가 있습니다. 이 경우 여러 IoT Hub로 장치를 분할하는 것이 좋습니다. 여러 IoT Hub가 갑작스러운 트래픽 증가를 원활하게 처리하고 필요한 처리량 또는 필요한 작업 속도를 얻습니다.

## <a name="next-steps"></a>다음 단계
IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [Azure IoT Edge에서 장치 시뮬레이션][lnk-iotedge]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
