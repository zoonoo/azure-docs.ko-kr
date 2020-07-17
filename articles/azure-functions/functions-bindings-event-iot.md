---
title: Azure Functions에 대 한 Azure IoT Hub 바인딩
description: Azure Functions에서 IoT Hub 트리거 및 바인딩을 사용 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77586135"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions에 대 한 Azure IoT Hub 바인딩

이 문서 집합에서는 IoT Hub에 대 한 Azure Functions 바인딩으로 작업 하는 방법을 설명 합니다. IoT Hub 지원은 [Azure Event Hubs 바인딩을](functions-bindings-event-hubs.md)기반으로 합니다.

> [!IMPORTANT]
> 다음 코드 샘플에서는 이벤트 허브 API를 사용 하지만 지정 된 구문은 IoT Hub 함수에 적용 됩니다.

| 작업 | 형식 |
|--------|------|
| IoT hub 이벤트 스트림으로 전송 된 이벤트에 응답 합니다. | [트리거](./functions-bindings-event-iot-trigger.md) |
| IoT 이벤트 스트림에 이벤트를 씁니다. | [출력 바인딩](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>다음 단계

- [이벤트 허브 이벤트 스트림으로 전송 된 이벤트에 응답 (트리거)](./functions-bindings-event-iot-trigger.md)
- [이벤트 스트림에 이벤트 쓰기 (출력 바인딩)](./functions-bindings-event-iot-output.md)
