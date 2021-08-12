---
title: Azure Functions 오류 처리 및 다시 시도 지침
description: 특정 바인딩 오류에 대한 링크를 사용하여 Azure Functions에서 오류 및 재시도 이벤트를 처리하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: e52babd5b0612b4590f1422a941b25ba30c3f169
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93284451"
---
# <a name="azure-functions-error-handling-and-retries"></a>Azure Functions 오류 처리 및 다시 시도

Azure Functions의 오류를 처리하는 작업은 손실된 데이터, 누락된 이벤트를 방지하고 애플리케이션의 상태를 모니터링하는 데 중요합니다.

이 문서에서는 바인딩 관련 오류에 대한 링크와 함께 오류 처리에 대한 일반적인 전략을 설명합니다.

## <a name="handling-errors"></a>오류 처리

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-retries.md)]

## <a name="binding-error-codes"></a>바인딩 오류 코드

Azure 서비스와 통합할 경우 기본 서비스의 API에서 오류가 발생할 수 있습니다. 바인딩 관련 오류에 대한 정보는 다음 문서의 **예외 및 반환 코드** 섹션에서 사용할 수 있습니다.

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT Hub](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table-output.md#exceptions-and-return-codes)
