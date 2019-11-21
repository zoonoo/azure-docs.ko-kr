---
title: Durable Functions versions overview - Azure Functions
description: Learn about Durable Functions versions.
author: cgillum
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 93c35eb4f69cc4f9b16f669d96c2df53f50bcf84
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231187"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions versions overview

*지속성 함수*는 서버를 사용하지 않는 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md) 및 [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다. If you are not already familiar with Durable Functions, see the [overview documentation](durable-functions-overview.md).

## <a name="new-features-in-2x"></a>New features in 2.x

This section describes the features of Durable Functions that are added in version 2.x.

### <a name="durable-entities"></a>Durable entities

In Durable Functions 2.x, we introduced a new [entity functions](durable-functions-entities.md) concept.

엔터티 함수는 *지속성 엔터티*라고 하는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용하는 함수입니다. Unlike orchestrator functions, entity functions do not have any specific code constraints. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

To learn more, see the [durable entities](durable-functions-entities.md) article.

### <a name="durable-http"></a>Durable HTTP

In Durable Functions 2.x, we introduced a new [Durable HTTP](durable-functions-http-features.md#consuming-http-apis) feature that allows you to:

* Call HTTP APIs directly from orchestration functions (with some documented limitations).
* Implement automatic client-side HTTP 202 status polling.
* Built-in support for [Azure Managed Identities](../../active-directory/managed-identities-azure-resources/overview.md).

To learn more, see the [HTTP features](durable-functions-http-features.md#consuming-http-apis) article.

## <a name="migrate-from-1x-to-2x"></a>Migrate from 1.x to 2.x

This section describes how to migrate your existing version 1.x Durable Functions to version 2.x to take advantage of the new features.

### <a name="upgrade-the-extension"></a>Upgrade the extension

Install version 2.x of the [Durable Functions bindings extension](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) in your project. See [Register Azure Functions binding extensions](../functions-bindings-register.md) for more information.

### <a name="update-your-code"></a>Update your code

Durable Functions 2.x introduces several breaking changes. Durable Functions 1.x applications are not compatible with Durable Functions 2.x without code changes. This section lists some of the changes you must make when upgrading your version 1.x functions to 2.x.

#### <a name="hostjson-schema"></a>Host.json schema

Durable Functions 2.x uses a new host.json schema. The main changes from 1.x include:

* `"storageProvider"` (and the `"azureStorage"` subsection) for storage-specific configuration.
* `"tracking"` for tracking and logging configuration.
* `"notifications"` (and the `"eventGrid"` subsection) for event grid notification configuration.

See the [Durable Functions host.json reference documentation](durable-functions-bindings.md#durable-functions-2-0-host-json) for details.

#### <a name="public-interface-changes-net-only"></a>Public interface changes (.NET only)

In version 1.x, the various _context_ objects supported by Durable Functions have abstract base classes intended for use in unit testing. As part of Durable Functions 2.x, these abstract base classes are replaced with interfaces.

The following table represents the main changes:

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` 또는 `IDurableClient` |
| `DurableOrchestrationContext` 또는 `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` 또는 `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

In the case where an abstract base class contained virtual methods, these virtual methods have been replaced by extension methods defined in `DurableContextExtensions`.

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json changes (JavaScript and C# Script)

In Durable Functions 1.x, the orchestration client binding uses a `type` of `orchestrationClient`. Version 2.x uses `durableClient` instead.
