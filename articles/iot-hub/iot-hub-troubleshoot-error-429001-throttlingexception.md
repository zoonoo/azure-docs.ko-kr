---
title: Azure IoT Hub 오류 429001 제한 예외 해결
description: 오류 429001 제한 예외를 수정하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960699"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

이 문서에서는 **429001 제한예외** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

IoT Hub에 대한 요청은 **오류 429001 제한 예외**.

## <a name="cause"></a>원인

요청된 작업에 대해 IoT Hub [제한 제한이](./iot-hub-devguide-quotas-throttling.md) 초과되었습니다.

## <a name="solution"></a>해결 방법

*원격 분석 메시지 전송 시도* 메트릭을 위에 지정된 제한과 비교하여 제한 제한에 도달하면 확인합니다. *제한 오류 메트릭을* 확인할 수도 있습니다. IoT Hub에서 사용할 수 있는 이러한 메트릭 및 기타 메트릭에 대한 자세한 내용은 [IoT Hub 메트릭 및 해당 메트릭을 사용하는 방법을 참조하세요.](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)

IoT Hub는 제한이 너무 오랜 기간 동안 위반된 후에만 429 제한 예외를 반환합니다. 이는 IoT Hub가 트래픽이 급증하는 경우 메시지가 삭제되지 않도록 하기 위해 수행됩니다. 반면 IoT Hub는 작업 제한 속도로 메시지를 처리합니다. 이 경우에 백로그에 너무 많은 트래픽이 있으면 성능이 저하될 수 있습니다. 자세한 내용은 [IoT Hub 트래픽 셰이핑](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)을 참조하세요.

## <a name="next-steps"></a>다음 단계

할당량 또는 제한 한도에 진입하는 경우 [IoT Hub를 확장하는](./iot-hub-scaling.md) 것이 좋습니다.