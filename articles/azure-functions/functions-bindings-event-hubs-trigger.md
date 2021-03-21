---
title: Azure Functions에 대 한 Azure Event Hubs 트리거
description: Azure Functions에서 Azure Event Hubs 트리거를 사용 하는 방법을 알아봅니다.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: c2b8302e64f7dcc657fd20ed5d918ed6816d750d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608917"
---
# <a name="azure-event-hubs-trigger-for-azure-functions"></a>Azure Functions에 대 한 Azure Event Hubs 트리거

이 문서에서는 Azure Functions에 대해 [Azure Event Hubs](../event-hubs/event-hubs-about.md) 트리거를 사용 하는 방법을 설명 합니다. Azure Functions는 Event Hubs에 대 한 트리거 및 [출력 바인딩을](functions-bindings-event-hubs-output.md) 지원 합니다.

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-event-hubs.md)를 참조하세요.

[!INCLUDE [functions-bindings-event-hubs-trigger](../../includes/functions-bindings-event-hubs-trigger.md)]

## <a name="hostjson-settings"></a>host.json 설정

파일 [ 의host.js](functions-host-json.md#eventhub) 에는 이벤트 허브 트리거 동작을 제어 하는 설정이 포함 되어 있습니다. 사용 가능한 설정에 대 한 자세한 내용은 [ 설정에](functions-bindings-event-hubs.md#hostjson-settings) 대 한host.js섹션을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [이벤트 스트림에 이벤트 쓰기 (출력 바인딩)](./functions-bindings-event-hubs-output.md)
