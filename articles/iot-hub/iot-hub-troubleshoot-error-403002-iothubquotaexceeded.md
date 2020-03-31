---
title: Azure IoT Hub 오류 403002 IoTHubQuota초과 문제 해결
description: 오류 403002 IoTHubQuota초과 수정 하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961115"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

이 문서에서는 **403002 IoTHubQuotaExceeded** 오류의 원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

IoT Hub에 대한 모든 요청은 **오류 403002 IoTHubQuotaExceed .를 초과하여 실패합니다.** Azure 포털에서 IoT 허브 장치 목록이 로드되지 않습니다.

## <a name="cause"></a>원인

IoT 허브의 일별 메시지 할당량을 초과했습니다. 

## <a name="solution"></a>해결 방법

[IoT 허브의 단위 수를 업그레이드하거나 늘리거나](iot-hub-upgrade.md) 일일 할당량이 새로 고칠 때까지 다음 UTC 일을 기다립니다.

## <a name="next-steps"></a>다음 단계

* 쌍일 쿼리 및 직접 메서드와 같은 할당량에 대한 운영 계산 방법을 이해하려면 [IoT Hub 가격 조정 을](iot-hub-devguide-pricing.md#charges-per-operation) 참조하세요.
* 일일 할당량 사용량에 대한 모니터링을 설정하려면 *사용된 총 메시지 수를*메트릭으로 설정합니다. 단계별 지침은 [IoT Hub를 사용 하](tutorial-use-metrics-and-diags.md#set-up-metrics) 여 메트릭 및 경고 설정 을 참조 합니다.