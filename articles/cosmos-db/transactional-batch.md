---
title: .NET SDK를 사용 하 Azure Cosmos DB의 트랜잭션 일괄 처리 작업
description: Azure Cosmos DB .NET SDK에서 TransactionalBatch를 사용 하 여 성공 또는 실패 한 지점 작업 그룹을 수행 하는 방법에 대해 알아봅니다.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: c27eb2b4e0dc0d269e1cffc9e213cebeb93443e2
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328703"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>.NET SDK를 사용 하 Azure Cosmos DB의 트랜잭션 일괄 처리 작업

트랜잭션 일괄 처리는 컨테이너에서 동일한 파티션 키와 함께 성공 하거나 실패 해야 하는 시점 작업 그룹을 설명 합니다. .NET SDK에서 `TranscationalBatch` 클래스는 이러한 작업 일괄 처리를 정의 하는 데 사용 됩니다. 트랜잭션 일괄 처리 작업 내에 설명 된 순서 대로 모든 작업이 성공 하면 트랜잭션이 커밋됩니다. 그러나 작업이 실패 하면 전체 트랜잭션이 롤백됩니다.

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Azure Cosmos DB의 트랜잭션

일반적인 데이터베이스의 트랜잭션은 단일 논리적 작업 단위로 수행 되는 일련의 작업으로 정의할 수 있습니다. 각 트랜잭션은 ACID (원자성, 일관성, 격리, 내구성) 속성을 보장 합니다.

* **원자성** 은 트랜잭션 내에서 수행 되는 모든 작업이 단일 단위로 처리 되도록 하 고 모든 작업이 커밋되거나 모두 커밋되지 않도록 보장 합니다.
* **일관성을 유지** 하면 데이터가 항상 트랜잭션 간에 유효한 상태가 됩니다.
* **격리** 는 두 트랜잭션이 서로 방해 하지 않도록 보장 합니다. 많은 상용 시스템은 응용 프로그램 요구 사항에 따라 사용할 수 있는 여러 격리 수준을 제공 합니다.
* **내구성** 은 데이터베이스에서 커밋된 변경 내용이 항상 표시 되도록 합니다.
Azure Cosmos DB는 동일한 [논리 파티션 키](partitioning-overview.md)내에서 작업에 대 한 [스냅숏 격리를 사용 하는 완전 한 ACID 규격 트랜잭션을](database-transactions-optimistic-concurrency.md) 지원 합니다.

## <a name="transactional-batch-operations-vs-stored-procedures"></a>트랜잭션 일괄 처리 작업 및 저장 프로시저

Azure Cosmos DB는 현재 작업에 대 한 트랜잭션 범위도 제공 하는 저장 프로시저를 지원 합니다. 그러나 트랜잭션 일괄 처리 작업은 다음과 같은 이점을 제공 합니다.

* **언어 옵션** – 트랜잭션 일괄 처리는 이미 사용 하는 SDK 및 언어에서 지원 되지만 저장 프로시저는 JavaScript로 작성 해야 합니다.
* **코드 버전** 관리-응용 프로그램 코드의 버전을 관리 하 고 CI/CD 파이프라인에 등록 하는 것은 저장 프로시저의 업데이트를 오케스트레이션 하 고 적절 한 시간에 롤오버를 수행 하는 것 보다 훨씬 자연스럽 게 수행 됩니다. 또한 변경 내용을 보다 쉽게 롤백할 수 있습니다.
* **성능** – 저장 프로시저 실행과 비교 하 여 해당 작업에 대 한 대기 시간을 최대 30% 줄입니다.
* **콘텐츠 serialization** – 트랜잭션 일괄 처리 내의 각 작업은 해당 페이로드에 대 한 사용자 지정 serialization 옵션을 활용할 수 있습니다.

## <a name="how-to-create-a-transactional-batch-operation"></a>트랜잭션 일괄 처리 작업을 만드는 방법

트랜잭션 일괄 처리 작업을 만들 때 컨테이너 인스턴스에서 시작 하 고를 호출 합니다 `CreateTransactionalBatch` .

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

다음에는를 호출 해야 합니다 `ExecuteAsync` .

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

응답이 수신 되 면 성공 여부를 검사 하 고 결과를 추출 해야 합니다.

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

오류가 발생 하는 경우 실패 한 작업에 해당 오류에 대 한 상태 코드가 포함 됩니다. 반면 다른 모든 작업에는 424 상태 코드 (실패 한 종속성)가 포함 됩니다. 아래 예제에서 작업은 이미 존재 하는 항목 (409 HttpStatusCode)을 만들려고 하기 때문에 실패 합니다. 상태 코드를 사용 하면 트랜잭션 오류의 원인을 쉽게 파악할 수 있습니다.

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>트랜잭션 일괄 처리 작업을 실행 하는 방법

`ExecuteAsync`메서드가 호출 되 면 개체의 모든 작업이 `TransactionalBatch` 그룹화 되 고 단일 페이로드로 serialize 되며 단일 요청으로 Azure Cosmos DB 서비스에 전송 됩니다.

서비스는 요청을 수신 하 고 트랜잭션 범위 내에서 모든 작업을 실행 하며 동일한 serialization 프로토콜을 사용 하 여 응답을 반환 합니다. 이 응답은 성공 또는 실패 이며 내부적으로 모든 개별 작업 응답을 포함 합니다.

SDK는 결과를 확인 하 고 필요에 따라 각 내부 작업 결과를 추출 하는 응답을 제공 합니다.

## <a name="limitations"></a>제한 사항

현재는 두 가지 알려진 제한 사항이 있습니다.

* Azure Cosmos DB 요청 크기 제한은 페이로드의 크기를 `TransactionalBatch` 2mb를 초과할 수 없으며 최대 실행 시간은 5 초입니다.
* `TransactionalBatch`성능이 예상 대로 그리고 sla 내에 있는지 확인 하기 위한 최대 100 작업 한도가 있습니다.

## <a name="next-steps"></a>다음 단계

* [TransactionalBatch로 수행할 수 있는 작업](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch) 에 대 한 자세한 정보

* Cosmos DB .NET SDK를 사용 하는 방법에 대 한 자세한 내용은 [샘플](sql-api-dotnet-v3sdk-samples.md) 을 참조 하세요.
