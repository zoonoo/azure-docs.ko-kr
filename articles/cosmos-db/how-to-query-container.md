---
title: Azure Cosmos DB에서 컨테이너 쿼리
description: Azure Cosmos DB에서 컨테이너를 쿼리하는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: cf14e005de3710f26bfdbab7cc0dac87e0cf000e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243746"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos 컨테이너 쿼리

이 문서에서는 Azure Cosmos DB에서 컨테이너(컬렉션, 그래프, 테이블)를 쿼리하는 방법을 설명합니다.

## <a name="in-partition-query"></a>파티션 내 쿼리

컨테이너에서 데이터를 쿼리 할 때 쿼리에 파티션 키 필터가 지정되어 있으면 Azure Cosmos DB가 쿼리를 자동으로 처리합니다. 필터에 지정된 파티션 키 값에 해당하는 파티션으로 쿼리를 라우팅합니다. 예를 들어 다음 쿼리는 파티션 키 값 `XMS-0001`에 해당하는 모든 문서를 포함하는 `DeviceId` 파티션으로 라우팅됩니다.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>파티션 간 쿼리

다음 쿼리는 파티션 키(`DeviceId`)에 대한 필터가 없으므로 파티션의 인덱스에 대해 실행되는 모든 파티션으로 팬아웃됩니다. 파티션 간에 쿼리를 실행 하려면 `EnableCrossPartitionQuery`를 true로(또는 REST API의 경우 `x-ms-documentdb-query-enablecrosspartition` )로 설정합니다.

EnableCrossPartitionQuery 속성은 부울 값을 허용합니다. true로 설정된 경우 쿼리에 파티션 키가 없으면 Azure Cosmos DB가 쿼리를 파티션 전반에 팬 아웃합니다. 팬 아웃은 모든 파티션에 개별 쿼리를 발급하여 수행됩니다. 쿼리 결과를 읽으려면 클라이언트 애플리케이션에서 FeedResponse의 결과를 사용하고 ContinuationToken 속성을 확인해야 합니다. 모든 결과를 읽으려면 ContinuationToken이 null이 될 때까지 데이터를 계속 반복합니다. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB는 SQL을 사용하여 컨테이너에 대해 집계 함수 COUNT, MIN, MAX 및 AVG를 지원합니다. 컨테이너에 대한 집계 함수는 SDK 버전 1.12.0 이상부터 지원됩니다. 쿼리는 단일 집계 연산자를 포함해야 하고 프로젝션에 단일 값을 포함해야 합니다.

## <a name="parallel-cross-partition-query"></a>병렬 파티션 간 쿼리

Azure Cosmos DB SDK 1.9.0 이상은 병렬 쿼리 실행 옵션을 지원합니다. 병렬 파티션 간 쿼리를 사용하면 대기 시간이 짧아지고 파티션 간 쿼리를 수행할 수 있습니다. 예를 들어 다음 쿼리는 파티션에 걸쳐 병렬로 실행되도록 구성되어 있습니다.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

다음 매개 변수를 조정하여 병렬 쿼리 실행을 관리할 수 있습니다.

- **MaxDegreeOfParallelism**: 컨테이너의 파티션에 대한 최대 동시 네트워크 연결 수를 설정합니다. 이 속성을 -1로 설정하는 경우 SDK에서 병렬 처리 수준이 관리됩니다.  `MaxDegreeOfParallelism` 이 지정되지 않았거나 기본값인 0으로 설정된 경우 컨테이너의 파티션에 단일 네트워크 연결이 있습니다.

- **MaxBufferedItemCount**: 쿼리 대기 시간과 클라이언트 쪽 메모리 사용률을 조정합니다. 이 매개 변수를 생략하거나 -1로 설정하는 경우 SDK는 병렬 쿼리 실행 중에 버퍼링된 항목 수를 관리합니다.

컬렉션의 상태가 동일한 경우 병렬 쿼리는 직렬 실행과 동일한 순서로 결과를 반환합니다. 정렬 연산자(ORDER BY, TOP)를 포함하는 교차 파티션 쿼리를 수행할 때, Azure Cosmos DB SDK는 여러 파티션에서 병렬로 쿼리를 실행합니다. 클라이언트 쪽에서 부분적으로 정렬된 결과를 병합하여 전역적으로 정렬된 결과를 생성합니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Azure Cosmos DB에서 분할하는 방법을 알아보세요.

- [Azure Cosmos DB에서 분할](partitioning-overview.md)
- [Azure Cosmos DB의 가상 파티션 키](synthetic-partition-keys.md)
