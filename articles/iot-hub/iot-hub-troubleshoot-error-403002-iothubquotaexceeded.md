---
title: 문제 해결 Azure IoT Hub 오류 403002 IoTHubQuotaExceeded
description: 403002 오류를 해결 하는 방법 이해 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76961115"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

이 문서에서는 **403002 IoTHubQuotaExceeded** 오류에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

IoT Hub에 대 한 모든 요청은 **403002 IoTHubQuotaExceeded**오류와 함께 실패 합니다. Azure Portal IoT hub 장치 목록이 로드 되지 않습니다.

## <a name="cause"></a>원인

IoT hub에 대 한 일일 메시지 할당량을 초과 했습니다. 

## <a name="solution"></a>솔루션

[IoT hub의 단위 수를 업그레이드 하거나 늘리고](iot-hub-upgrade.md) 매일 할당량을 새로 고칠 때까지 다음 UTC 날짜를 기다립니다.

## <a name="next-steps"></a>다음 단계

* 쌍 쿼리 및 직접 메서드와 같이 작업을 할당량에 계산 하는 방법을 이해 하려면 [IoT Hub 가격 책정 이해](iot-hub-devguide-pricing.md#charges-per-operation) 를 참조 하세요.
* 일일 할당량 사용에 대 한 모니터링을 설정 하려면 *사용 된 총 메시지 수*메트릭에 대 한 경고를 설정 합니다. 단계별 지침은 [IoT Hub를 사용 하 여 메트릭 및 경고 설정](tutorial-use-metrics-and-diags.md#set-up-metrics) 을 참조 하세요.