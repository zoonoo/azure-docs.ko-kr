---
title: Durable Functions 청구- Azure Functions
description: Durable Functions의 내부 동작과 이로 인해 Azure Functions 청구에 미치는 영향을 알아봅니다.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233005"
---
# <a name="durable-functions-billing"></a>Durable Functions 청구

[Durable Functions](durable-functions-overview.md)에 대한 요금은 Azure Functions와 동일한 방식으로 청구됩니다. 자세한 내용은 [Azure Functions 가격 책정](https://azure.microsoft.com/pricing/details/functions/)을 참조하세요.

Azure Functions [소비 계획](../functions-scale.md#consumption-plan)에서 오케스트레이터 함수를 실행할 때 알고 있어야 할 몇 가지 청구 동작이 있습니다. 다음 섹션에서는 이러한 동작과 그 영향에 대해 자세히 설명합니다.

## <a name="orchestrator-function-replay-billing"></a>오케스트레이터 함수 재생 청구

[오케스트레이션 함수](durable-functions-orchestrations.md)는 오케스트레이션의 전체 수명 동안 여러 번 재생할 수 있습니다. 각 재생은 Azure Functions 런타임에서 고유한 함수 호출로 나타납니다. 따라서 Azure Functions 소비 계획에서는 오케스트레이터 함수의 각 재생에 대한 요금이 청구됩니다. 다른 계획 유형에서는 오케스트레이터 함수 재생에 대해 요금이 청구되지 않습니다.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>오케스트레이터 함수 대기 및 생성

오케스트레이터 함수에서 **await**(C#) 또는 **yield**(JavaScript)를 사용하여 비동기 작업이 완료될 때까지 대기하는 경우 런타임은 해당 특정 실행이 완료된 것으로 간주합니다. 오케스트레이터 함수에 대한 청구는 해당 지점에서 중지됩니다. 다음 오케스트레이터 함수 재생까지 다시 시작되지 않습니다. 오케스트레이터 함수에서 대기하거나 생성하는 데 걸린 시간에 대해서는 요금이 청구되지 않습니다.

> [!NOTE]
> 다른 함수를 호출하는 함수 중 일부는 이러한 패턴이 아닌 것으로 간주됩니다. 이는 _이중 요금 청구_로 알려진 문제이기 때문입니다. 함수에서 다른 함수를 직접 호출하면 두 함수가 동시에 실행됩니다. 호출하는 함수가 응답을 대기하는 동안 호출된 함수가 능동적으로 코드를 실행하고 있습니다. 이 경우 호출된 함수가 실행할 때까지 호출 함수가 대기한 시간에 대한 비용을 지불해야 합니다.
>
> 오케스트레이터 함수에는 이중 청구가 없습니다. 활동 함수 또는 하위 오케스트레이션의 결과를 기다리는 동안 오케스트레이터 함수에 대한 요금 청구가 중지됩니다.

## <a name="durable-http-polling"></a>지속성 HTTP 폴링

[HTTP 기능 문서](durable-functions-http-features.md)에서 설명한 대로 오케스트레이터 함수는 외부 엔드포인트에 대한 장기 실행 HTTP 호출을 수행할 수 있습니다. [비동기 202 패턴](durable-functions-http-features.md#http-202-handling)을 따르는 동안 C# **CallHttpAsync** 메서드와 JavaScript의 **callHttp** 메서드는 내부적으로 HTTP 엔드포인트를 폴링할 수 있습니다.

현재 내부 HTTP 폴링 작업에 대해서는 직접적으로 청구하지 않습니다. 단, 내부 폴링으로 인해 오케스트레이터 함수가 정기적으로 재생될 수 있습니다. 이러한 내부 함수 재생에 대한 표준 요금은 청구됩니다.

## <a name="azure-storage-transactions"></a>Azure Storage 트랜잭션

Durable Functions는 기본적으로 Azure Storage를 사용하여 Blob 임대를 통해 상태를 유지하고, 메시지를 처리하며, 파티션을 관리합니다. 이 스토리지 계정은 사용자가 소유하므로 모든 트랜잭션 비용이 Azure 구독에 청구됩니다. Durable Functions에서 사용하는 Azure Storage 아티팩트에 대한 자세한 내용은 [작업 허브 문서](durable-functions-task-hubs.md)를 참조하세요.

Durable Functions 앱에서 발생하는 실제 Azure Storage 비용에 영향을 주는 몇 가지 요인이 있습니다.

* 단일 함수 앱은 Azure Storage 리소스 세트를 공유하는 단일 작업 허브와 연결됩니다. 이러한 리소스는 함수 앱의 모든 지속성 함수에서 사용합니다. 함수 앱의 실제 함수 수는 Azure Storage 트랜잭션 비용에 영향을 주지 않습니다.
* 각 함수 앱 인스턴스는 지수 백오프 폴링 알고리즘을 사용하여 스토리지 계정에서 여러 큐를 내부적으로 폴링합니다. 유휴 앱 인스턴스는 활성 앱보다 큐를 덜 폴링하므로 트랜잭션 비용이 적게 발생합니다. Durable Functions 큐 폴링 동작에 대한 자세한 내용은 [성능 및 크기 조정 문서의 큐 폴링 섹션](durable-functions-perf-and-scale.md#queue-polling)을 참조하세요.
* Azure Functions 소비 또는 프리미엄 계획에서 실행하는 경우 [Azure Functions 크기 조정 컨트롤러](../functions-scale.md#how-the-consumption-and-premium-plans-work)는 백그라운드에서 모든 작업 허브 큐를 정기적으로 폴링합니다. 함수 앱이 작은 크기에서 중간 크기인 경우 단일 크기 조정 컨트롤러 인스턴스만 이러한 큐를 폴링합니다. 함수 앱이 많은 인스턴스로 확장되면 더 많은 크기 조정 컨트롤러 인스턴스가 추가될 수 있습니다. 이러한 추가 크기 조정 컨트롤러 인스턴스로 인해 총 큐 트랜잭션 비용이 늘어날 수 있습니다.
* 각 함수 앱 인스턴스는 Blob 임대 세트에 대해 경합합니다. 이러한 인스턴스는 정기적으로 Azure Blob service를 호출하여 보류 중인 임대를 갱신하거나 새 임대를 획득하려고 합니다. Blob 임대 수는 작업 허브에 구성된 파티션 수에 따라 결정됩니다. 더 많은 함수 앱 인스턴스로 확장하면 이러한 임대 작업과 관련된 Azure Storage 트랜잭션 비용이 늘어날 수 있습니다.

Azure Storage 가격 책정에 대한 자세한 내용은 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage/) 설명서에서 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 가격 책정에 대해 자세히 알아보기](https://azure.microsoft.com/pricing/details/functions/)
