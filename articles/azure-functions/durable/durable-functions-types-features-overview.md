---
title: Azure Durable Functions의 함수 형식
description: Azure Functions의 Durable Functions 오케스트레이션에서 함수 간 통신을 지원하는 함수 및 역할 형식에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697710"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions 형식 및 기능

Durable Functions은 [Azure Functions](../functions-overview.md)의 확장입니다. 함수 실행의 상태 저장 오케스트레이션에 대한 Durable Functions를 사용할 수 있습니다. 지속성 함수 앱은 다양한 Azure Functions로 구성된 솔루션입니다. Functions는 지속성 함수 오케스트레이션에서 서로 다른 역할을 수행할 수 있습니다. 

현재 Azure Functions에는 활동, 오케스트레이터, 엔터티 및 클라이언트의 네 가지 지속성 함수 형식이 있습니다. 이 섹션의 나머지 부분에서는 오케스트레이션과 관련된 함수 형식에 대해 자세히 설명합니다.

## <a name="orchestrator-functions"></a>오케스트레이터 함수

오케스트레이터 함수는 작업이 실행되는 방법 및 작업이 실행되는 순서를 설명합니다. 오케스트레이터 함수는 [Durable Functions 애플리케이션 패턴](durable-functions-overview.md#application-patterns)에 표시된 대로 코드(C# 또는 JavaScript)에서 오케스트레이션을 설명합니다. 오케스트레이션에는 [활동 함수](#activity-functions), [하위 오케스트레이션](durable-functions-orchestrations.md#sub-orchestrations), [외부 이벤트 대기](durable-functions-orchestrations.md#external-events), [HTTP](durable-functions-http-features.md) 및 [타이머](durable-functions-orchestrations.md#durable-timers)를 비롯한 다양한 유형의 작업이 포함될 수 있습니다. 오케스트레이터 함수는 [엔터티 함수](#entity-functions)와 상호 작용할 수도 있습니다.

> [!NOTE]
> 오케스트레이터 함수는 일반 코드를 사용하여 작성되지만 코드를 작성하는 방법에 대한 엄격한 요구 사항이 있습니다. 특히, 오케스트레이터 함수 코드는 *결정적* 이어야 합니다. 이러한 결정적 요구 사항을 따르지 않으면 오케스트레이터 함수가 올바르게 실행되지 않을 수 있습니다. 이러한 요구 사항 및 해결 방법에 대한 자세한 내용은 [코드 제약 조건](durable-functions-code-constraints.md) 토픽에서 찾을 수 있습니다.

오케스트레이터 함수 및 해당 기능에 대한 자세한 내용은 [지속성 오케스트레이션](durable-functions-orchestrations.md) 문서를 참조하세요.

## <a name="activity-functions"></a>활동 함수

활동 함수는 지속성 함수 오케스트레이션의 기본 작업 단위입니다. 활동 함수는 프로세스에서 오케스트레이션되는 함수 및 작업입니다. 예를 들어, 순서를 처리하기 위해 오케스트레이터 함수를 만들 수 있습니다. 작업에는 인벤토리 확인, 고객 요금 청구 및 배송물 생성 작업이 포함됩니다. 각 작업은 별도의 활동 함수입니다. 이러한 활동 함수는 직렬, 병렬 또는 둘의 조합으로 실행할 수 있습니다.

오케스트레이터 함수와 달리, 활동 함수는 수행할 수 있는 작업 형식에 제한되지 않습니다. 활동 함수는 네트워크 호출을 수행하거나 CPU를 많이 사용하는 작업을 실행하는 데 자주 사용됩니다. 또한, 활동 함수는 오케스트레이터 함수로 데이터를 다시 반환할 수 있습니다. 지속성 작업 프레임워크는 호출된 각 활동 함수가 오케스트레이션 실행 중에 *한 번 이상* 실행되도록 합니다.

> [!NOTE]
> 활동 함수는 *한 번 이상* 실행되도록 보장하므로 가능하면 활동 함수 논리 *idempotent* 를 만드는 것이 좋습니다.

[활동 트리거](durable-functions-bindings.md#activity-trigger)를 사용하여 활동 함수를 정의합니다. .NET 함수는 `DurableActivityContext`를 매개 변수로 받습니다. 트리거를 다른 JSON 직렬화 개체에 바인딩하여 함수에 입력을 전달할 수도 있습니다. JavaScript에서 [`context.bindings` 개체](../functions-reference-node.md#bindings)의 `<activity trigger binding name>` 속성을 통해 입력에 액세스할 수 있습니다. 활동 함수에는 단일 값만 전달할 수 있습니다. 여러 값을 전달하려면 튜플, 배열 또는 복합 형식을 사용해야 합니다.

> [!NOTE]
> 오케스트레이터 함수에서만 활동 함수를 트리거할 수 있습니다.

## <a name="entity-functions"></a>엔터티 함수

엔터티 함수는 작은 상태 부분을 읽고 업데이트하는 작업을 정의합니다. 이러한 상태 저장 엔터티를 *지속성 엔터티* 로 자주 참조합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거* 를 사용하는 함수입니다. 클라이언트 함수 또는 오케스트레이터 함수에서 호출할 수도 있습니다. 오케스트레이터 함수와 달리 엔터티 함수는 특정 코드 제약 조건을 포함하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

> [!NOTE]
> 엔터티 함수 및 관련 기능은 Durable Functions 2.0 이상에서만 사용할 수 있습니다.

엔터티 함수에 대한 자세한 내용은 [지속성 엔터티](durable-functions-entities.md) 문서를 참조하세요.

## <a name="client-functions"></a>클라이언트 함수

오케스트레이터 함수는 [오케스트레이션 트리거 바인딩](durable-functions-bindings.md#orchestration-trigger)을 통해 트리거되고 엔터티 함수는 [엔터티 트리거 바인딩](durable-functions-bindings.md#entity-trigger)에 의해 트리거됩니다. 이러한 트리거는 모두 [작업 허브](durable-functions-task-hubs.md)로 큐에 삽입되는 메시지에 응답하여 작동합니다. 이러한 메시지를 전달하는 기본적인 방법은 *클라이언트 함수* 내에서 [오케스트레이터 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client) 또는 [엔터티 클라이언트 바인딩](durable-functions-bindings.md#entity-client)을 사용하는 것입니다. 오케스트레이터가 아닌 함수는 *클라이언트 함수* 일 수 있습니다. 예를 들어, HTTP로 트리거되는 함수, Azure Event Hub 트리거 함수 등에서 오케스트레이터를 트리거할 수 있습니다. *클라이언트 함수* 에서 함수를 만드는 것은 내구성이 있는 클라이언트 출력 바인딩을 사용하는 것입니다.

> [!NOTE]
> 다른 함수 형식과 달리, 오케스트레이터와 엔터티 함수는 Azure Portal의 단추를 사용하여 직접 트리거할 수 없습니다. Azure Portal에서 오케스트레이터 또는 엔터티 함수를 테스트하려는 경우 해당 구현의 일부로 오케스트레이터 또는 엔터티 함수를 시작하는 *클라이언트 함수* 를 대신 실행해야 합니다. 가장 간단한 테스트 환경을 위해 *수동 트리거* 함수를 사용하는 것이 좋습니다.

오케스트레이터 또는 엔터티 함수를 트리거하는 것 외에도 실행 중인 오케스트레이션 및 엔터티와 상호 작용하는 데 *내구성이 있는 클라이언트* 바인딩을 사용할 수 있습니다. 예를 들어, 오케스트레이션을 쿼리 및 종료하고 이벤트를 발생시킬 수 있습니다. 오케스트레이션 및 엔터티 관리에 대한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

시작하려면 [C#](durable-functions-create-first-csharp.md) 또는 [JavaScript](quickstart-js-vscode.md)에서 첫 번째 지속성 함수를 만듭니다.

> [!div class="nextstepaction"]
> [Durable Functions 오케스트레이션에 대한 자세한 정보 읽기](durable-functions-orchestrations.md)