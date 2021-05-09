---
title: Azure Functions에 대한 Azure IoT Hub 트리거
description: Azure Functions에서 IoT Hub 이벤트 스트림으로 전송된 이벤트에 응답하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 5c9309834b407ee56d29e38afd965ac947fc8a4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612289"
---
# <a name="azure-iot-hub-trigger-for-azure-functions"></a>Azure Functions에 대한 Azure IoT Hub 트리거

이 문서에서는 IoT Hub에 Azure Functions 바인딩을 사용하는 방법을 설명합니다. IoT Hub 지원은 [Azure Event Hubs 바인딩](functions-bindings-event-hubs.md)을 기반으로 합니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-event-iot.md)를 참조하세요.

> [!IMPORTANT]
> 다음 코드 샘플에서는 Event Hub API를 사용하지만 지정된 구문은 IoT Hub 함수에 적용할 수 있습니다.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-properties"></a>host.json 속성

[host.json](functions-host-json.md#eventhub) 파일에는 이벤트 허브 트리거 동작을 제어하는 설정이 포함되어 있습니다. 사용 가능한 설정에 대한 자세한 내용은 [host.json 설정](functions-bindings-event-iot.md#hostjson-settings) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [이벤트 스트림에 이벤트 쓰기(출력 바인딩)](./functions-bindings-event-iot-output.md)
