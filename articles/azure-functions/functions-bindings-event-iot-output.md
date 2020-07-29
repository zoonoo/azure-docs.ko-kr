---
title: Azure Functions에 대 한 Azure IoT Hub 출력 바인딩
description: Azure Functions를 사용 하 여 Azure IoT Hub 스트림에 메시지를 작성 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79277428"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure Functions에 대 한 Azure IoT Hub 출력 바인딩

이 문서에서는 IoT Hub에 대 한 Azure Functions 출력 바인딩을 사용 하는 방법을 설명 합니다. IoT Hub 지원은 [Azure Event Hubs 바인딩을](functions-bindings-event-hubs.md)기반으로 합니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-event-iot.md)를 참조하세요.

> [!IMPORTANT]
> 다음 코드 샘플에서는 이벤트 허브 API를 사용 하지만 지정 된 구문은 IoT Hub 함수에 적용 됩니다.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>다음 단계

- [이벤트 허브 이벤트 스트림으로 전송 된 이벤트에 응답 (트리거)](./functions-bindings-event-iot-trigger.md)
