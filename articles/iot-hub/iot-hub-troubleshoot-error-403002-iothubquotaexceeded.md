---
title: 문제 해결 Azure IoT Hub 오류 403002 IoTHubQuotaExceeded
description: 오류 403002 IoTHubQuotaExceeded 해결 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061318"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

이 문서에서는 **403002 IoTHubQuotaExceeded** 오류에 대한 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에 대한 모든 요청이 실패하고 **403002 IoTHubQuotaExceeded** 오류가 표시됩니다. Azure Portal에서 IoT 허브 디바이스 목록이 로드되지 않습니다.

## <a name="cause"></a>원인

IoT 허브의 일일 메시지 할당량을 초과했습니다. 

## <a name="solution"></a>솔루션

[IoT 허브의 단위 수를 업그레이드하거나 늘리고](iot-hub-upgrade.md) 일일 할당량이 새로 고쳐질 때까지 다음 UTC 일을 기다립니다.

## <a name="next-steps"></a>다음 단계

* 쌍 쿼리 및 직접 메서드와 같이 할당량으로 작업이 계산되는 방식을 이해하려면 [IoT Hub 가격 책정 이해](iot-hub-devguide-pricing.md#charges-per-operation)를 참조하세요.
* 일일 할당량 사용에 대한 모니터링을 설정하려면 ‘사용된 총 메시지 수’ 메트릭을 사용하여 알림을 설정합니다. 단계별 지침은 [IoT Hub를 사용하여 메트릭 및 알림 설정](tutorial-use-metrics-and-diags.md#set-up-metrics)을 참조하세요.