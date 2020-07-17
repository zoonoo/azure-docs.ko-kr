---
title: 문제 해결 Azure IoT Hub 오류 429001 ThrottlingException
description: 429001 오류를 해결 하는 방법 이해 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960699"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

이 문서에서는 **429001 ThrottlingException** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

IoT Hub에 대 한 요청은 **429001 ThrottlingException**오류와 함께 실패 합니다.

## <a name="cause"></a>원인

요청 된 작업에 대 한 IoT Hub 제한 [제한을](./iot-hub-devguide-quotas-throttling.md) 초과 했습니다.

## <a name="solution"></a>솔루션

*원격 분석 메시지 보내기 시도* 메트릭을 위에 지정 된 제한과 비교 하 여 제한 한도에 도달 했는지 확인 합니다. 또한 *조정 오류 메트릭의 수* 를 확인할 수 있습니다. IoT Hub에 사용할 수 있는 이러한 메트릭과 기타 메트릭에 대 한 자세한 내용은 [IoT Hub 메트릭 및 사용 방법](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)을 참조 하세요.

IoT Hub는 너무 긴 기간 동안 제한을 위반 하는 경우에만 429 ThrottlingException을 반환 합니다. 이는 IoT hub가 버스트 트래픽을 가져오는 경우 메시지가 삭제 되지 않도록 하기 위한 것입니다. 반면 IoT Hub는 작업 제한 속도로 메시지를 처리합니다. 이 경우에 백로그에 너무 많은 트래픽이 있으면 성능이 저하될 수 있습니다. 자세한 내용은 [IoT Hub 트래픽 셰이핑](./iot-hub-devguide-quotas-throttling.md#traffic-shaping)을 참조하세요.

## <a name="next-steps"></a>다음 단계

할당량 또는 제한 제한을 초과 하 여 실행 하는 경우 [IoT Hub를 확장](./iot-hub-scaling.md) 하는 것이 좋습니다.