---
title: Azure IoT Hub 500xxx 내부 오류 문제 해결
description: 500xxx 내부 오류를 해결 하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1333a135f3e123757e268513f73e8329537e630b
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538207"
---
# <a name="500xxx-internal-errors"></a>500xxx 내부 오류

이 문서에서는 **500xxx 내부 오류** 에 대 한 원인과 해결 방법을 설명 합니다.

## <a name="symptoms"></a>증상

IoT Hub에 대 한 요청이 500 및/또는 일종의 "서버 오류"로 시작 하는 오류와 함께 실패 합니다. 몇 가지 가능한 원인은 다음과 같습니다.

* **500001 ServerError** : IoT Hub 서버 쪽 문제로 실행 되었습니다.

* **500008 GenericTimeout** : IoT Hub 시간이 초과 되기 전에 연결 요청을 완료할 수 없습니다.

* **Serviceunavailable 수 없음 (오류 코드 없음)** : IoT Hub 내부 오류가 발생 했습니다.

* **Internalservererror (오류 코드 없음)** : IoT Hub에서 내부 오류가 발생 했습니다.

## <a name="cause"></a>원인

500xxx 오류 응답의 원인에는 여러 가지가 있을 수 있습니다. 모든 경우에이 문제는 일시적으로 발생할 수 있습니다. IoT Hub 팀에서는 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)를 유지하기 위해 열심히 노력하고 있지만, IoT Hub 노드의 작은 하위 집합에 이따금 일시적인 오류가 발생할 수 있습니다. 디바이스에서 문제가 있는 노드에 연결을 시도하면, 이 오류가 나타납니다.

## <a name="solution"></a>솔루션

500xxx 오류를 완화 하려면 장치에서 재시도를 실행 합니다. [재시도를 자동으로 관리하려면](./iot-hub-reliability-features-in-sdks.md#connection-and-retry) 최신 버전의 [Azure IoT SDK](./iot-hub-devguide-sdks.md)를 사용해야 합니다. 일시적인 오류 처리 및 재시도에 대한 모범 사례는 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.  문제가 지속 되 면 [Resource Health](./iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) 및 [Azure 상태](https://status.azure.com/) 를 확인 하 여 IoT Hub에 알려진 문제가 있는지 확인 합니다. [수동 장애 조치 (failover) 기능](./tutorial-manual-failover.md)을 사용할 수도 있습니다. 알려진 문제가 없고 문제가 계속 되 면 [지원에 문의](https://azure.microsoft.com/support/options/) 하 여 추가 조사를 수행 합니다.
