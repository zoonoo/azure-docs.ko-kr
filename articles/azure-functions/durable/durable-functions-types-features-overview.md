---
title: Azure 지속 형 함수의 함수 유형
description: Azure Functions의 지속적 함수 오케스트레이션에서 함수 간 통신을 지원하는 함수 및 역할 유형에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277883"
---
# <a name="durable-functions-types-and-features"></a>내구성이 뛰어난 기능 유형 및 기능

Durable Functions은 [Azure Functions](../functions-overview.md)의 확장입니다. 기능 실행의 상태 풀게스트레이션에 내구성 함수를 사용할 수 있습니다. 지속형 함수 앱은 서로 다른 Azure 함수로 구성된 솔루션입니다. 함수는 지속형 함수 오케스트레이션에서 서로 다른 역할을 할 수 있습니다. 

현재 Azure Function에는 활동, 오케스트레이터, 엔터티 및 클라이언트의 네 가지 지속형 함수 유형이 있습니다. 이 섹션의 나머지 부분에서는 오케스트레이션과 관련된 함수 유형에 대해 자세히 설명합니다.

## <a name="orchestrator-functions"></a>오케스트레이터 함수

Orchestrator 함수는 작업이 실행되는 방법과 작업이 실행되는 순서를 설명합니다. 오케스트레이터 함수는 [내구함수 응용 프로그램 패턴에](durable-functions-overview.md#application-patterns)표시된 대로 코드(C# 또는 JavaScript)의 오케스트레이션을 설명합니다. 오케스트레이션에는 [활동 함수,](#activity-functions) [하위 오케스트레이션,](durable-functions-orchestrations.md#sub-orchestrations)외부 이벤트 대기, [HTTP](durable-functions-http-features.md)및 [타이머를](durable-functions-orchestrations.md#durable-timers) [비롯한](durable-functions-orchestrations.md#external-events)다양한 유형의 작업이 있을 수 있습니다. 오케스트레이터 함수는 [엔터티 함수와](#entity-functions)상호 작용할 수도 있습니다.

> [!NOTE]
> 오케스트레이터 함수는 일반 코드를 사용하여 작성되지만 코드를 작성하는 방법에 대한 엄격한 요구 사항이 있습니다. 특히 오케스트레이터 함수 코드는 *결정적이어야*합니다. 이러한 결정성 요구 사항을 따르지 않으면 오케스트레이터 함수가 올바르게 실행되지 않을 수 있습니다. 이러한 요구 사항에 대한 자세한 정보와 이러한 요구 사항을 해결 하는 방법은 [코드 제약 조건](durable-functions-code-constraints.md) 항목에서 찾을 수 있습니다.

오케스트레이터 함수 및 기능에 대한 자세한 내용은 [지속성 오케스트레이션](durable-functions-orchestrations.md) 문서를 참조하십시오.

## <a name="activity-functions"></a>활동 함수

활동 함수는 지속형 함수 오케스트레이션의 기본 작업 단위입니다. 활동 함수는 프로세스에서 오케스트레이션되는 함수 및 작업입니다. 예를 들어 오케스트레이터 함수를 만들어 주문을 처리할 수 있습니다. 업무에는 재고 확인, 고객에게 충전 및 발송물 생성이 포함됩니다. 각 작업은 별도의 활동 함수입니다. 이러한 활동 함수는 직렬로, 병렬로 또는 둘 다의 일부 조합을 실행할 수 있습니다.

오케스트레이터 함수와 달리 활동 함수는 작업 유형에서 수행할 수 있는 작업에 제한되지 않습니다. 활동 함수는 네트워크 호출을 하거나 CPU 집약적 작업을 실행하는 데 자주 사용됩니다. 활동 함수는 데이터를 오케스트레이터 함수로 되돌릴 수도 있습니다. 지속적 작업 프레임워크는 오케스트레이션을 실행하는 동안 각 호출된 활동 함수가 *적어도 한 번* 실행되도록 보장합니다.

> [!NOTE]
> 활동 함수는 *적어도 한 번만* 실행을 보장하기 때문에 가능하면 활동 함수 *논리를 idempotent로* 만드는 것이 좋습니다.

활동 [트리거를](durable-functions-bindings.md#activity-trigger) 사용하여 활동 함수를 정의합니다. .NET 함수는 `DurableActivityContext` 매개 변수로 수신됩니다. 트리거를 다른 JSON 직렬화 가능한 개체에 바인딩하여 입력을 함수에 전달할 수도 있습니다. JavaScript에서 `<activity trigger binding name>` [ `context.bindings` 개체의](../functions-reference-node.md#bindings)속성을 통해 입력에 액세스할 수 있습니다. 활동 함수는 단일 값만 전달할 수 있습니다. 여러 값을 전달하려면 튜플, 배열 또는 복잡한 형식을 사용해야 합니다.

> [!NOTE]
> 오케스트레이터 함수에서만 활동 함수를 트리거할 수 있습니다.

## <a name="entity-functions"></a>엔터티 함수

엔터티 함수는 작은 상태 조각을 읽고 업데이트하기 위한 작업을 정의합니다. 이러한 상태 충실 한 엔터티를 *지속 적 엔터티로*참조 하는 경우가 많습니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용하는 함수입니다. 클라이언트 함수 또는 오케스트레이터 함수에서 호출할 수도 있습니다. 오케스트레이터 함수와 달리 엔터티 함수에는 특정 코드 제약 조건이 없습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

> [!NOTE]
> 엔터티 함수 및 관련 기능은 Durable Functions 2.0 이상에서만 사용할 수 있습니다.

엔터티 함수에 대한 자세한 내용은 [지속 성 엔터티](durable-functions-entities.md) 문서를 참조하십시오.

## <a name="client-functions"></a>클라이언트 함수

오케스트레이터 함수는 [오케스트레이션 트리거 바인딩에](durable-functions-bindings.md#orchestration-trigger) 의해 트리거되고 엔터티 함수는 [엔터티 트리거 바인딩에](durable-functions-bindings.md#entity-trigger)의해 트리거됩니다. 이러한 트리거는 모두 [작업 허브에](durable-functions-task-hubs.md)큐에 들어 있는 메시지에 반응하여 작동합니다. 이러한 메시지를 배달하는 기본 방법은 [오케스트레이터 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client) 또는 *클라이언트 함수*내에서 엔터티 [클라이언트 바인딩을](durable-functions-bindings.md#entity-client) 사용하는 것입니다. 모든 비오케터 함수는 *클라이언트 함수일*수 있습니다. 예를 들어 HTTP 트리거 함수, Azure 이벤트 허브 트리거 함수 등에서 오케스트레이터를 트리거할 수 있습니다. 함수를 클라이언트 *함수로* 만드는 것은 지속형 클라이언트 출력 바인딩을 사용하는 것입니다.

> [!NOTE]
> 다른 함수 형식과 달리 오케스트레이터 및 엔터티 함수는 Azure Portal의 단추를 사용하여 직접 트리거할 수 없습니다. Azure Portal에서 오케스트레이터 또는 엔터티 함수를 테스트하려면 대신 오케스트레이터 또는 엔터티 *함수를* 구현의 일부로 시작하는 클라이언트 함수를 실행해야 합니다. 가장 간단한 테스트 환경을 위해 *수동 트리거* 기능을 권장합니다.

오케스트레이터 또는 엔터티 함수를 트리거하는 것 외에도 내구성 있는 *클라이언트* 바인딩을 사용하여 실행 중인 오케스트레이션 및 엔터티와 상호 작용할 수 있습니다. 예를 들어 오케스트레이션은 쿼리, 종료 및 이벤트 발생이 있을 수 있습니다. 오케스트레이션 및 엔터티 관리에 대한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

시작하려면 [C#](durable-functions-create-first-csharp.md) 또는 [JavaScript에서](quickstart-js-vscode.md)첫 번째 지속 형 함수를 만듭니다.

> [!div class="nextstepaction"]
> [내구성 함수 오케스트레이션에 대해 자세히 알아보기](durable-functions-orchestrations.md)