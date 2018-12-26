---
title: Azure Cosmos DB에서 컨테이너 쿼리
description: Azure Cosmos DB에서 컨테이너를 쿼리하는 방법 알아보기
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 11c68b61802f6c7b3755da71c176ea777f171e4c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409839"
---
# <a name="query-containers-in-azure-cosmos-db"></a>Azure Cosmos DB에서 컨테이너 쿼리

이 문서에서는 Azure Cosmos DB에서 컨테이너(컬렉션, 그래프, 테이블)를 쿼리하는 방법을 설명합니다.

## <a name="in-partition-query"></a>파티션 내 쿼리

컨테이너에서 데이터를 쿼리할 때 파티션 키 필터가 쿼리에 지정되어 있으면 Azure Cosmos DB에서 자동으로 쿼리를 필터에 지정된 파티션 키 값에 해당하는 파티션으로 라우팅합니다. 예를 들어 다음 쿼리는 "XMS-0001" 파티션 키 값에 해당하는 모든 문서를 포함하는 DeviceId 파티션으로 라우팅됩니다.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>파티션 간 쿼리

다음 쿼리는 파티션 키(DeviceId)에 대한 필터가 없으므로 파티션의 인덱스에 대해 실행되는 모든 파티션으로 팬아웃됩니다. 파티션 간 쿼리를 실행하려면 **EnableCrossPartitionQuery**를 true(또는 REST API에서 x-ms-documentdb-query-enablecrosspartition)로 설정합니다.

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB는 SQL을 사용하여 컨테이너에 대해 집계 함수 COUNT, MIN, MAX 및 AVG를 지원합니다. 컨테이너에 대한 집계 함수는 SDK 버전 1.12.0 이상부터 지원됩니다. 쿼리는 단일 집계 연산자를 포함해야 하고 프로젝션에 단일 값을 포함해야 합니다.

## <a name="parallel-cross-partition-query"></a>병렬 파티션 간 쿼리

Cosmos DB SDK 1.9.0 이상은 병렬 쿼리 실행 옵션을 지원합니다.  병렬 파티션 간 쿼리를 사용하면 대기 시간이 짧아지고 파티션 간 쿼리를 수행할 수 있습니다. 예를 들어 다음 쿼리는 파티션에 걸쳐 병렬로 실행되도록 구성되어 있습니다.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

다음 매개 변수를 조정하여 병렬 쿼리 실행을 관리할 수 있습니다.

- **MaxDegreeOfParallelism**: 컨테이너의 파티션에 대한 최대 동시 네트워크 연결 수를 설정합니다. 이 속성을 -1로 설정하는 경우 병렬 처리 수준이 SDK에서 관리됩니다. the MaxDegreeOfParallelism is가 지정되지 않았거나 기본값인 0으로 설정된 경우 컨테이너의 파티션에 단일 네트워크 연결이 생성됩니다.

- **MaxBufferedItemCount**: 쿼리 대기 시간과 클라이언트 쪽 메모리 사용률을 조정합니다. 이 매개 변수를 생략하거나 -1로 설정하는 경우 병렬 쿼리 실행 중에 버퍼링되는 항목의 수가 SDK에서 관리됩니다.

컬렉션의 상태가 동일한 경우 병렬 쿼리는 직렬 실행과 동일한 순서로 결과를 반환합니다. 정렬 연산자(ORDER BY 및/또는 TOP)를 포함하는 파티션 간 쿼리를 수행할 경우 Azure Cosmos DB SDK는 파티션에 걸쳐 병렬로 쿼리를 실행하고, 클라이언트 쪽에서 부분적으로 정렬된 결과를 병합하여 전역적으로 정렬된 결과를 생성합니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 Cosmos DB에서 분할하는 방법을 알아보세요.

- [Azure Cosmos DB에서 분할](partitioning-overview.md)
- [Azure Cosmos DB의 가상 파티션 키](synthetic-partition-keys.md)
