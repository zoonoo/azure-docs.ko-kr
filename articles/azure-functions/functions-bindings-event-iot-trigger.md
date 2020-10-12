---
title: Azure Functions에 대 한 Azure IoT Hub 트리거
description: Azure Functions에서 IoT hub 이벤트 스트림으로 전송 된 이벤트에 응답 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 25396da3fb9a4293633308bf2e9d3c6b3d07265d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87041641"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions에 대 한 Azure IoT Hub 트리거

이 문서에서는 IoT Hub에 대 한 Azure Functions 바인딩을 사용 하는 방법을 설명 합니다. IoT Hub 지원은 [Azure Event Hubs 바인딩을](functions-bindings-event-hubs.md)기반으로 합니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-event-iot.md)를 참조하세요.

> [!IMPORTANT]
> 다음 코드 샘플에서는 이벤트 허브 API를 사용 하지만 지정 된 구문은 IoT Hub 함수에 적용 됩니다.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="next-steps"></a>다음 단계

- [이벤트 스트림에 이벤트 쓰기 (출력 바인딩)](./functions-bindings-event-iot-output.md)
