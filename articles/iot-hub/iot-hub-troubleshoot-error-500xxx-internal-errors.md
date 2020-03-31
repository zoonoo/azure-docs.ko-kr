---
title: Azure IoT Hub 500xxx 내부 오류 문제 해결
description: 500xxx 내부 오류를 수정하는 방법 이해
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271045"
---
# <a name="500xxx-internal-errors"></a>500xxx 내부 오류

이 문서에서는 **500xxx 내부 오류의**원인과 해결 에 대한 설명입니다.

## <a name="symptoms"></a>증상

IoT Hub에 대한 요청은 500 및/또는 일종의 "서버 오류"로 시작하는 오류와 함께 실패합니다. 몇 가지 가능성은 다음과 같습니다.

* **500001 ServerError**: IoT Hub가 서버 측 에 문제가 발생했습니다.

* **500008 GenericTimeout**: IoT Hub가 시간 중지 전에 연결 요청을 완료할 수 없습니다.

* **서비스 사용할 수 없음(오류 코드 없음)**: IoT Hub에서 내부 오류가 발생했습니다.

* **InternalServerError(오류 코드 없음)**: IoT Hub에서 내부 오류가 발생했습니다.

## <a name="cause"></a>원인

500xxx 오류 응답에는 여러 가지 원인이 있을 수 있습니다. 모든 경우에 이 문제는 일시적일 가능성이 높습니다. IoT Hub 팀에서는 [SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/)를 유지하기 위해 열심히 노력하고 있지만, IoT Hub 노드의 작은 하위 집합에 이따금 일시적인 오류가 발생할 수 있습니다. 디바이스에서 문제가 있는 노드에 연결을 시도하면, 이 오류가 나타납니다.

## <a name="solution"></a>해결 방법

500xxx 오류를 완화하려면 장치에서 다시 시도합니다. [재시도를 자동으로 관리하려면](./iot-hub-reliability-features-in-sdks.md#connection-and-retry) 최신 버전의 [Azure IoT SDK](./iot-hub-devguide-sdks.md)를 사용해야 합니다. 일시적인 오류 처리 및 재시도에 대한 모범 사례는 [일시적인 오류 처리](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults)를 참조하세요.  문제가 지속되면 리소스 [상태](./iot-hub-monitor-resource-health.md#use-azure-resource-health) 및 [Azure 상태를](https://status.azure.com/) 확인하여 IoT Hub에 알려진 문제가 있는지 확인합니다. [수동 장애 조치 기능을](./tutorial-manual-failover.md)사용할 수도 있습니다. 알려진 문제가 없고 문제가 계속되면 [지원팀에 문의하여](https://azure.microsoft.com/support/options/) 추가 조사를 요청하십시오.
