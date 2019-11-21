---
title: Azure Functions error handling guidance
description: Learn to handle errors in Azure Functions with links to specific binding errors.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 06dc4c5c1713ee10f263e573a698e9ea36ca2662
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227334"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 오류 처리

Handling errors in Azure Functions is important to avoid lost data, missed events, and to monitor the health of your application.

This article describes general strategies for error handling along with links to binding-specific errors.

## <a name="handling-errors"></a>오류 처리

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>바인딩 오류 코드

When integrating with Azure services, errors may originate from the APIs of the underlying services. Information relating to binding-specific errors is available in the **Exceptions and return codes** section of the following articles:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
