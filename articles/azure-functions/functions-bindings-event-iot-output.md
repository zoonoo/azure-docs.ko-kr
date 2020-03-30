---
title: Azure 기능에 대한 Azure IoT 허브 출력 바인딩
description: Azure 함수를 사용하여 Azure IoT Hubs 스트림에 메시지를 작성하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277428"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Azure 기능에 대한 Azure IoT 허브 출력 바인딩

이 문서에서는 IoT Hub에 대한 Azure Functions 출력 바인딩을 사용하여 작업하는 방법을 설명합니다. IoT Hub 지원은 [Azure 이벤트 허브 바인딩을](functions-bindings-event-hubs.md)기반으로 합니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요를](functions-bindings-event-iot.md)참조하십시오.

> [!IMPORTANT]
> 다음 코드 샘플에서는 Event Hub API를 사용하지만 지정된 구문은 IoT Hub 함수에 적용할 수 있습니다.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>다음 단계

- [이벤트 허브 이벤트 스트림으로 전송된 이벤트에 응답(트리거)](./functions-bindings-event-iot-trigger.md)
