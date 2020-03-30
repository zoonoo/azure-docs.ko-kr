---
title: Azure 함수 오류 처리 지침
description: 특정 바인딩 오류에 대한 링크가 있는 Azure Functions에서 오류를 처리하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586192"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 오류 처리

Azure Functions에서 오류를 처리하는 것은 손실된 데이터, 누락된 이벤트를 방지하고 응용 프로그램의 상태를 모니터링하는 데 중요합니다.

이 문서에서는 바인딩 관련 오류에 대한 링크와 함께 오류 처리에 대한 일반적인 전략을 설명합니다.

## <a name="handling-errors"></a>오류 처리

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>바인딩 오류 코드

Azure 서비스와 통합할 때 오류가 기본 서비스의 API에서 발생할 수 있습니다. 바인딩 관련 오류와 관련된 정보는 다음 문서의 예외 및 반환 코드 섹션에서 확인할 수 **있습니다.**

+ [Azure 코스모스 DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT 허브](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [큐 스토리지](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
