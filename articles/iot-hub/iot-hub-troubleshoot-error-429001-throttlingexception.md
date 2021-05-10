---
title: 문제 해결 Azure IoT Hub 오류 429001 ThrottlingException
description: 오류 429001 ThrottlingException 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 9619a3d2ba24e806f6c684a5576bce03d7e6b793
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060978"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

이 문서에서는 **429001 ThrottlingException** 오류의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에 대한 요청이 실패하고 **429001 ThrottlingException** 오류가 표시됩니다.

## <a name="cause"></a>원인

요청한 작업에 대해 IoT Hub [제한 한도](./iot-hub-devguide-quotas-throttling.md)를 초과했습니다.

## <a name="solution"></a>솔루션

*원격 분석 메시지 보내기 시도* 메트릭을 위에서 지정한 한도와 비교하여 제한 한도에 도달하는지 확인합니다. ‘제한 오류 수’ 메트릭도 확인할 수 있습니다. 이러한 메트릭에 대한 자세한 내용은 [디바이스 원격 분석 메트릭](monitor-iot-hub-reference.md#device-telemetry-metrics)을 참조하세요. 메트릭을 사용하여 IoT 허브를 모니터링하는 방법에 대한 자세한 내용은 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요.

IoT Hub는 너무 오랜 기간 한도를 위반한 경우에만 429 ThrottlingException을 반환합니다. 이렇게 하면 IoT 허브에 트래픽 버스트가 발생할 때 메시지가 삭제되지 않습니다. 반면 IoT Hub는 작업 제한 속도로 메시지를 처리합니다. 이 경우에 백로그에 너무 많은 트래픽이 있으면 성능이 저하될 수 있습니다. 자세한 내용은 [IoT Hub 트래픽 셰이핑](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)을 참조하세요.

## <a name="next-steps"></a>다음 단계

할당량 또는 제한 한도에 도달하는 경우 [IoT Hub를 스케일 업](./iot-hub-scaling.md)하는 것이 좋습니다.