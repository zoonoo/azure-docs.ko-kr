---
title: Azure Durable Functions의 함수 형식
description: Azure Functions의 Durable Functions 오케스트레이션에 서 함수 간 통신을 지 원하는 함수 및 역할 형식에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84697710"
---
# <a name="durable-functions-types-and-features"></a>Durable Functions 형식 및 기능

Durable Functions은 [Azure Functions](../functions-overview.md)의 확장입니다. 함수 실행의 상태 저장 오케스트레이션에 Durable Functions를 사용할 수 있습니다. 지 속성 함수 앱은 다양 한 Azure 함수로 구성 된 솔루션입니다. 함수는 지 속성 함수 오케스트레이션에 서 다른 역할을 수행할 수 있습니다. 

현재 Azure Functions에는 작업, orchestrator, entity 및 client의 네 가지 지 속성 함수 형식이 있습니다. 이 섹션의 나머지 부분에서는 오케스트레이션과 관련 된 함수 유형에 대해 자세히 설명 합니다.

## <a name="orchestrator-functions"></a>오케스트레이터 함수

오 케 스트레이 터 함수는 작업이 실행 되는 방법 및 작업이 실행 되는 순서를 설명 합니다. Orchestrator 함수는 [응용 프로그램 패턴 Durable Functions](durable-functions-overview.md#application-patterns)같이 코드에서 오케스트레이션을 설명 합니다 (c # 또는 JavaScript). 오케스트레이션은 [활동 함수](#activity-functions), [하위 오케스트레이션](durable-functions-orchestrations.md#sub-orchestrations), [외부 이벤트 대기](durable-functions-orchestrations.md#external-events), [HTTP](durable-functions-http-features.md)및 [타이머](durable-functions-orchestrations.md#durable-timers)를 비롯 한 다양 한 유형의 작업을 포함할 수 있습니다. Orchestrator 함수도 [엔터티 함수와](#entity-functions)상호 작용할 수도 있습니다.

> [!NOTE]
> Orchestrator 함수는 일반 코드를 사용 하 여 작성 되지만 코드를 작성 하는 방법에 대 한 엄격한 요구 사항이 있습니다. 특히 오 케 스트레이 터 함수 코드는 *결정적*이어야 합니다. 이러한 확정 요구 사항을 따르지 않으면 오 케 스트레이 터 함수가 올바르게 실행 되지 않을 수 있습니다. 이러한 요구 사항 및 해결 방법에 대 한 자세한 내용은 [코드 제약 조건](durable-functions-code-constraints.md) 항목에서 찾을 수 있습니다.

Orchestrator 함수 및 해당 기능에 대 한 자세한 내용은 지 [속성 오케스트레이션](durable-functions-orchestrations.md) 문서를 참조 하세요.

## <a name="activity-functions"></a>활동 함수

작업 함수는 영 속 함수 오케스트레이션의 기본 작업 단위입니다. 작업 함수는 프로세스에서 오케스트레이션 되는 함수 및 작업입니다. 예를 들어 주문을 처리 하기 위해 오 케 스트레이 터 함수를 만들 수 있습니다. 작업에는 인벤토리 확인, 고객 요금 청구 및 배송 생성이 포함 됩니다. 각 작업은 별도의 작업 함수입니다. 이러한 활동 함수는 직렬, 병렬 또는 둘의 조합으로 실행할 수 있습니다.

오 케 스트레이 터 함수와 달리 작업 함수는 작업의 형식에서 수행할 수 있는 작업 형식으로 제한 되지 않습니다. 작업 함수는 네트워크 호출을 수행 하거나 CPU를 많이 사용 하는 작업을 실행 하는 데 자주 사용 됩니다. 또한 작업 함수는 orchestrator 함수로 데이터를 다시 반환할 수 있습니다. 지 속성 작업 프레임 워크는 호출 된 각 작업 함수가 오케스트레이션의 실행 중 *에 한 번* 이상 실행 되도록 보장 합니다.

> [!NOTE]
> 활동 함수는 *적어도 한 번* 실행 되도록 보장 하므로 가능 하면 활동 함수 논리를 *idempotent* 하는 것이 좋습니다.

활동 [트리거](durable-functions-bindings.md#activity-trigger) 를 사용 하 여 활동 함수를 정의 합니다. .NET 함수는를 `DurableActivityContext` 매개 변수로 받습니다. 트리거를 다른 serializeable 개체에 바인딩하여 함수에 입력을 전달할 수도 있습니다. JavaScript에서 `<activity trigger binding name>` [ `context.bindings` 개체](../functions-reference-node.md#bindings)의 속성을 통해 입력에 액세스할 수 있습니다. 작업 함수에는 단일 값만 전달할 수 있습니다. 여러 값을 전달 하려면 튜플, 배열 또는 복합 형식을 사용 해야 합니다.

> [!NOTE]
> Orchestrator 함수 에서만 작업 함수를 트리거할 수 있습니다.

## <a name="entity-functions"></a>엔터티 함수

엔터티 함수는 작은 상태를 읽고 업데이트 하기 위한 작업을 정의 합니다. 이러한 상태 저장 엔터티를 *내구성이 있는 엔터티로*자주 참조 합니다. 오케스트레이터 함수와 마찬가지로 엔터티 함수는 특수 트리거 유형인 *엔터티 트리거*를 사용하는 함수입니다. 클라이언트 함수 또는 orchestrator 함수에서 호출할 수도 있습니다. Orchestrator 함수와 달리 entity 함수는 특정 코드 제약 조건을 포함 하지 않습니다. 또한 엔터티 함수는 제어 흐름을 통해 상태를 암시적으로 나타내지 않고 명시적으로 관리합니다.

> [!NOTE]
> 엔터티 함수 및 관련 기능은 Durable Functions 2.0 이상에서만 사용할 수 있습니다.

엔터티 함수에 대 한 자세한 내용은 지 [속성 엔터티](durable-functions-entities.md) 문서를 참조 하세요.

## <a name="client-functions"></a>클라이언트 함수

Orchestrator 함수는 [오케스트레이션 트리거 바인딩을](durable-functions-bindings.md#orchestration-trigger) 통해 트리거되고 엔터티 함수는 [엔터티 트리거 바인딩에](durable-functions-bindings.md#entity-trigger)의해 트리거됩니다. 이러한 트리거는 모두 [작업 허브](durable-functions-task-hubs.md)로 큐에 배치 되는 메시지에 응답 하 여 작동 합니다. 이러한 메시지를 배달 하는 기본적인 방법은 *클라이언트 함수*내에서 [orchestrator 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client) 또는 [엔터티 클라이언트 바인딩을](durable-functions-bindings.md#entity-client) 사용 하는 것입니다. 모든 오 케 스트레이 터 함수는 *클라이언트 함수*일 수 있습니다. 예를 들어 HTTP로 트리거되는 함수, Azure 이벤트 허브 트리거 함수 등에서 orchestrator를 트리거할 수 있습니다. *클라이언트 함수* 에서 함수를 만드는 것은 내구성이 있는 클라이언트 출력 바인딩을 사용 하는 것입니다.

> [!NOTE]
> 다른 함수 형식과 달리 orchestrator 및 entity 함수는 Azure Portal의 단추를 사용 하 여 직접 트리거할 수 없습니다. Azure Portal에서 orchestrator 또는 entity 함수를 테스트 하려는 경우에는 해당 구현의 일부로 orchestrator 또는 entity 함수를 시작 하는 *클라이언트 함수* 를 대신 실행 해야 합니다. 가장 간단한 테스트 환경을 위해 *수동 트리거* 함수를 권장 합니다.

Orchestrator 또는 entity 함수를 트리거하는 것 외에도 *내구성이 있는 클라이언트* 바인딩을 사용 하 여 오케스트레이션 및 엔터티를 실행 하는 것과 상호 작용할 수 있습니다. 예를 들어 오케스트레이션을 쿼리, 종료 하 고 이벤트를 발생 시킬 수 있습니다. 오케스트레이션 및 엔터티 관리에 대 한 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

시작 하려면 [c #](durable-functions-create-first-csharp.md) 또는 [JavaScript](quickstart-js-vscode.md)로 첫 번째 내구성이 있는 함수를 만듭니다.

> [!div class="nextstepaction"]
> [Durable Functions 오케스트레이션에 대 한 자세한 정보](durable-functions-orchestrations.md)