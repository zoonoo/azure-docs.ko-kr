---
title: Azure Cosmos DB의 요청 단위 및 처리량
description: Azure Cosmos DB에서 요청 단위 요구 사항을 지정하고 예측하는 방법에 대한 자세한 정보
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a0058bf309e0ff4fbe687731d676e907d1c3fd82
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383103"
---
# <a name="request-units-in-azure-cosmos-db"></a>Azure Cosmos DB의 요청 단위

Azure Cosmos DB를 사용하면 프로비전하는 처리량 및 시간 단위로 사용하는 스토리지의 요금을 지불합니다. Throughput must be provisioned to ensure that sufficient system resources are available for your Azure Cosmos database at all times. You need enough resources to meet or exceed the [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/).

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다. 

The cost of all database operations is normalized by Azure Cosmos DB and is expressed by *Request Units* (or RUs, for short). 초당 RU를 처리량 통화로 간주할 수 있습니다. 초당 RU는 속도 기반 통화로, Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리 등의 시스템 리소스를 추상화합니다. 

The cost to read a 1 KB item is 1 Request Unit (or 1 RU). A minimum of 10 RU/s is required to store each 1 GB of data. All other database operations are similarly assigned a cost using RUs. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 읽기 또는 쿼리든 간에 비용은 항상 RU로 측정됩니다.

The following image shows the high-level idea of RUs:

![데이터베이스 작업은 요청 단위를 사용함](./media/request-units/request-units.png)

용량을 관리하고 계획하려면 Azure Cosmos DB에서는 지정된 데이터 세트를 통해 지정된 데이터베이스 작업에 대한 RU 수가 결정적이 되도록 확인합니다. You can examine the response header to track the number of RUs that are consumed by any database operation. When you understand the [factors that affect RU charges](request-units.md#request-unit-considerations) and your application's throughput requirements, you can run your application cost effectively.

초당 100 RU 증분으로, 애플리케이션에 대한 RU 수를 프로비전합니다. 애플리케이션에 대해 프로비전되는 처리량 크기를 조정하기 위해 언제든지 RU 수를 늘리거나 줄일 수 있습니다. You can scale in increments or decrements of 100 RUs. 프로그래밍 방식으로 또는 Azure Portal을 사용하여 변경할 수 있습니다. You are billed on an hourly basis.

두 가지 고유 단위로 처리량을 프로비전할 수 있습니다. 

* **Containers**: For more information, see [Provision throughput on an Azure Cosmos container](how-to-provision-container-throughput.md).
* **Databases**: For more information, see [Provision throughput on an Azure Cosmos database](how-to-provision-database-throughput.md).

## <a name="request-unit-considerations"></a>요청 단위 고려 사항

프로비전할 초당 RU 수를 예상하는 동안 다음 요소를 고려하세요.

* **Item size**: As the size of an item increases, the number of RUs consumed to read or write the item also increases.

* **Item indexing**: By default, each item is automatically indexed. 컨테이너에서 일부 항목을 인덱싱하지 않도록 선택하면 더 적은 RU가 사용됩니다.

* **Item property count**: Assuming the default indexing is on all properties, the number of RUs consumed to write an item increases as the item property count increases.

* **Indexed properties**: An index policy on each container determines which properties are indexed by default. 쓰기 작업에 대한 RU 사용을 줄이려면 인덱싱되는 속성 수를 제한합니다.

* **Data consistency**: The strong and bounded staleness consistency levels consume approximately two times more RUs while performing read operations when compared to that of other relaxed consistency levels.

* **Query patterns**: The complexity of a query affects how many RUs are consumed for an operation. 쿼리 작업 비용에 영향을 주는 요소는 다음과 같습니다. 
    
    - The number of query results
    - The number of predicates
    - The nature of the predicates
    - The number of user-defined functions
    - The size of the source data
    - The size of the result set
    - 프로젝션

  Azure Cosmos DB는 동일한 데이터의 동일한 쿼리가 항상 반복 실행에서 동일한 RU 수를 사용하도록 보장합니다.

* **Script usage**: As with queries, stored procedures and triggers consume RUs based on the complexity of the operations that are performed. As you develop your application, inspect the [request charge header](optimize-cost-queries.md#evaluate-request-unit-charge-for-a-query) to better understand how much RU capacity each operation consumes.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량을 프로비전](set-throughput.md)하는 방법을 자세히 알아봅니다.
* [논리 파티션](partition-data.md)에 대해 자세히 알아봅니다.
* [프로비전된 처리량 크기를 전역적으로 조정](scaling-throughput.md)하는 방법을 자세히 알아봅니다.
* [Azure Cosmos 컨테이너의 처리량을 프로비전](how-to-provision-container-throughput.md)하는 방법을 알아봅니다.
* [Azure Cosmos 데이터베이스의 처리량을 프로비전](how-to-provision-database-throughput.md)하는 방법을 알아봅니다.
* Learn how to [find the request unit charge for an operation](find-request-unit-charge.md).
* Learn how to [optimize provisioned throughput cost in Azure Cosmos DB](optimize-cost-throughput.md).
* Learn how to [optimize reads and writes cost in Azure Cosmos DB](optimize-cost-reads-writes.md).
* Learn how to [optimize query cost in Azure Cosmos DB](optimize-cost-queries.md).
* Learn how to [use metrics to monitor throughput](use-metrics.md).
