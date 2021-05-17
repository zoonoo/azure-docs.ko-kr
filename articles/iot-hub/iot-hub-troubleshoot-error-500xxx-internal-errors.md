---
title: Azure IoT Hub 500xxx 내부 오류 문제 해결
description: 500xxx 내부 오류를 해결하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1bf3c405f988edc2a75a2b54f664f7cbada7b158
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060995"
---
# <a name="500xxx-internal-errors"></a>500xxx 내부 오류

이 문서에서는 **500xxx 내부 오류** 의 원인과 해결 방법을 설명합니다.

## <a name="symptoms"></a>증상

IoT Hub에 대한 요청이 500 및/또는 일종의 "서버 오류"로 시작하는 오류가 발생합니다. 몇 가지 가능성은 다음과 같습니다.

* **500001 ServerError**: IoT Hub에 서버 쪽 문제가 발생했습니다.

* **500008 GenericTimeout**: IoT Hub가 시간이 초과되기 전에 연결 요청을 완료할 수 없습니다.

* **ServiceUnavailable(오류 코드 없음)** : IoT Hub에서 내부 오류가 발생했습니다.

* **InternalServerError(오류 코드 없음)** : IoT Hub에서 내부 오류가 발생 했습니다.

## <a name="cause"></a>원인

500xxx 오류 응답의 원인에는 여러 가지가 있을 수 있습니다. 모든 경우에서 이 문제는 일시적일 가능성이 높습니다. IoT Hub 팀에서는 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)를 유지하기 위해 열심히 노력하고 있지만, IoT Hub 노드의 작은 하위 집합에 이따금 일시적인 오류가 발생할 수 있습니다. 디바이스에서 문제가 있는 노드에 연결을 시도하면, 이 오류가 나타납니다.

## <a name="solution"></a>해결 방법

500xxx 오류를 줄이려면 디바이스에서 다시 시도하세요. [재시도를 자동으로 관리하려면](./iot-hub-reliability-features-in-sdks.md#connection-and-retry) 최신 버전의 [Azure IoT SDK](./iot-hub-devguide-sdks.md)를 사용해야 합니다. 일시적인 오류 처리 및 재시도에 대한 모범 사례는 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.  문제가 지속되면[Resource Health](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) 및 [Azure 상태](https://status.azure.com/)를 확인하여 IoT Hub에 알려진 문제가 있는지 확인합니다. [수동 장애 조치(failover) 기능](./tutorial-manual-failover.md)을 사용할 수도 있습니다. 알려진 문제가 없고 문제가 계속되면 추가 조사를 위해 [지원팀에 문의](https://azure.microsoft.com/support/options/)하세요.
