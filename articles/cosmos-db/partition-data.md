---
title: "Azure Cosmos DB에서 분할 및 수평적 확장 | Microsoft Docs"
description: "Azure Cosmos DB에서 분할 작동 방법, 분할 및 파티션 키를 구성하는 방법 및 응용 프로그램에 대한 올바른 파티션 키를 선택하는 방법에 대해 알아봅니다."
services: cosmosdb
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 920c6f810e723712b72f642b783f093bb5d4f7d4
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---

# <a name="how-to-partition-and-scale-in-azure-cosmos-db"></a>Azure Cosmos DB에서 분할 및 확장하는 방법

[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)는 전역적으로 배포된 다중 모델 데이터베이스 서비스로써 신속하고 예측 가능한 성능을 얻을 수 있고, 응용 프로그램 증가에 따라 효율적인 확장이 가능하도록 설계되었습니다. 이 문서에서는 Azure Cosmos DB에서 모든 데이터 모델에 분할이 작동하는 방식을 개괄적으로 살펴보고, 응용 프로그램을 효과적으로 확장하도록 Azure Cosmos DB 컨테이너를 구성하는 방법을 설명합니다.

Scott Hanselman과 Azure Cosmos DB 수석 엔지니어링 관리자 Shireesh Thota가 진행하는 이 Azure Friday 비디오에서는 분할 및 파티션 키에 대해서도 다룹니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB에서 분할
Azure Cosmos DB에서는 규모에 상관없이 밀리초 단위의 응답 시간 순으로 스키마 없는 데이터를 저장하고 쿼리할 수 있습니다. Cosmos DB는 **컬렉션(문서의 경우), 그래프 및 표**라는 데이터 저장용 컨테이너를 제공합니다. 컨테이너는 하나 이상의 물리적 파티션 또는 서버에 걸쳐 있을 수 있는 논리적 리소스입니다. 파티션 수는 컨테이너의 프로비전된 처리량 및 저장소 크기에 따라 Cosmos DB에 의해 결정됩니다. Cosmos DB의 모든 파티션은 고정된 양의 SSD 지원 저장소에 연결되며, 고가용성을 위해 복제됩니다. 파티션 관리는 Azure Cosmos DB에 의해 완전히 관리되므로 복잡한 코드를 작성하거나 파티션을 관리할 필요가 없습니다. Cosmos DB 컨테이너는 처리량 및 저장소 면에서 제한되지 않습니다. 

![horizontal](./media/introduction/azure-cosmos-db-partitioning.png) 

분할은 응용 프로그램에 대해 투명합니다. Cosmos DB는 빠른 읽기 및 쓰기, 쿼리, 트랜잭션 논리, 일관성 수준 및 단일 컨테이너 리소스에 대한 메서드/API를 통해 세분화된 액세스 제어를 지원합니다. 이 서비스는 파티션 간의 데이터 분산 및 올바른 파티션에 대한 쿼리 요청을 처리합니다. 

분할은 어떻게 작동하나요? 각 항목에는 파티션 키 및 행 키가 있어서 항목을 고유하게 식별해야 합니다. 파티션 키는 데이터에 대한 논리적 분할의 역할을 하고 파티션 간에 데이터를 배포하는 자연 경계를 가진 Cosmos DB를 제공합니다. 간단히 말해, Azure Cosmos DB에서 분할의 작동 방식은 다음과 같습니다.

* `T` 요청/초 처리량으로 Cosmos DB 컨테이너를 프로비전합니다.
* Cosmos DB는 내부적으로 `T` 요청/초를 제공하는 데 필요한 파티션을 프로비전합니다. `T`이 파티션당 최대 처리량 `t`보다 높은 경우 Cosmos DB는 `N` = `T/t` 파티션을 프로비전합니다.
* Cosmos DB는 `N` 파티션에 걸쳐 균등하게 파티션 키 해시의 주요 공간을 할당합니다. 따라서 각 파티션(실제 파티션)은 1-N 파티션 키 값(논리 파티션)을 호스팅합니다.
* 실제 파티션 `p`이 저장 한도에 도달하는 경우 Cosmos DB는 `p`를 `p1` 및 `p2`라는 두 개의 새 파티션으로 원활하게 나누고 각 파티션에 대해 약 절반의 키에 해당하는 값을 배포합니다. 이 분리 작업은 응용 프로그램에 표시되지 않습니다.
* 마찬가지로 `t*N`보다 높은 처리량을 프로비전할 경우 Cosmos DB는 더 높은 처리량을 지원하기 위해 하나 이상의 파티션을 나눕니다.

파티션 키의 의미 체계는 다음 표에 나와 있는 것처럼 각 API의 의미 체계와 약간 다릅니다.

| API | Partition Key | Row Key |
| --- | --- | --- |
| DocumentDB | custom partition key path | fixed `id` | 
| MongoDB | custom shard key  | fixed `_id` | 
| 그래프 | custom partition key property | fixed `id` | 
| 테이블 | fixed `PartitionKey` | fixed `RowKey` | 

Cosmos DB는 해시 기반 분할을 사용합니다. 항목을 작성하는 경우 Cosmos DB는 파티션 키 값을 해시하고 해시된 결과를 사용하여 항목을 저장할 파티션을 결정합니다. Cosmos DB는 동일한 실제 파티션에 동일한 파티션 키를 가진 모든 항목을 저장합니다. 파티션 키의 선택은 디자인 타임에서 결정해야 하는 중요한 사항입니다. 다양한 범위의 값과 균일한 액세스 패턴을 가진 속성 이름을 선택해야 합니다.

> [!NOTE]
> 많은 고유 값(최소한 수백~수천 개)을 가진 파티션 키를 갖는 것이 좋습니다.
>

Azure Cosmos DB 컨테이너를 "고정" 또는 "무제한"으로 만들 수 있습니다. 고정 크기 컨테이너는 최대 제한 10GB 및 10,000RU/s 처리량을 설정할 수 있습니다. 일부 API를 사용하면 고정된 크기의 컨테이너에서 파티션 키를 생략할 수 있습니다. 무제한으로 컨테이너를 만들려면 최소 처리량 2500RU/s를 지정해야 합니다.

## <a name="partitioning-and-provisioned-throughput"></a>분할 및 프로비전된 처리량
Cosmos DB는 예측 가능한 성능을 위해 설계되었습니다. 컨테이너를 만들 때 **RU/m의 잠재적인 기능으로 초당 [요청 단위](../documentdb/documentdb-request-units.md)(RU)** 측면에서 처리량을 예약합니다. 각 요청에는 작업에 소비된 CPU, 메모리 및 IO 같은 시스템 리소스의 양에 비례하는 요청 단위 요금이 할당됩니다. 세션 일관성에 따라 1KB 문서를 읽을 경우 1RU(요청 단위)가 소비됩니다. 저장된 항목 수 또는 동시에 실행되는 요청 수에 상관없이 읽기당 1RU입니다. 항목의 크기가 클수록 크기에 따라 더 높은 요청 단위가 필요합니다. 응용 프로그램에 지원해야 하는 엔터티 크기 및 읽기 수를 알고 있는 경우 응용 프로그램의 읽기 요구 사항에 필요한 정확한 양의 처리량을 프로비전할 수 있습니다. 

> [!NOTE]
> 컨테이너의 전체 처리량을 달성하려면 다양한 파티션 키 값 간에 요청을 균등하게 분산시킬 수 있는 파티션 키를 선택해야 합니다.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="working-with-the-azure-cosmos-db-apis"></a>Azure Cosmos DB API 사용
Azure Portal 또는 Azure CLI를 사용하여 언제든지 컨테이너를 만들고 확장할 수 있습니다. 이 섹션에서는 컨테이너를 만들고 각 지원되는 API의 처리량 및 파티션 키 정의를 지정하는 방법을 보여 줍니다.

### <a name="documentdb-api"></a>DocumentDB API
다음 샘플에서는 DocumentDB API를 사용하여 컨테이너(컬렉션)를 만드는 방법을 보여 줍니다. [DocumentDB API를 사용하여 분할](../documentdb/documentdb-partition-data.md)에서 자세한 내용을 볼 수 있습니다.

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

REST API에서 `GET` 메서드 또는 SDK 중 하나에서 `ReadDocumentAsync`를 사용하여 항목(문서)을 읽을 수 있습니다.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

### <a name="mongodb-api"></a>MongoDB API
MongoDB API에서 선호하는 도구, 드라이버 또는 SDK를 통해 분할된 컬렉션을 만들 수 있습니다. 이 예에서는 Mongo Shell을 사용하여 컬렉션을 만들겠습니다.

Mongo Shell에서:

```
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
결과:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

### <a name="table-api"></a>테이블 API

테이블 API에서 응용 프로그램의 appSettings 구성에 있는 테이블의 처리량을 지정합니다.

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

그런 다음 Azure Table Storage SDK를 사용하여 테이블을 만듭니다. 파티션 키는 암시적으로 `PartitionKey` 값으로 만들어집니다. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

다음 코드 조각을 사용하여 단일 엔터티를 검색할 수 있습니다.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
자세한 내용은 [테이블 API를 사용하여 개발](tutorial-develop-table-dotnet.md)을 참조하세요.

### <a name="graph-api"></a>그래프 API

Graph API에서 Azure Portal 또는 CLI를 사용하여 컨테이너를 만들어야 합니다. 또는 Azure Cosmos DB가 다중 모델이므로 다른 모델 중 하나를 사용하여 그래프 컨테이너를 만들고 확장할 수 있습니다.

Gremlin에서 파티션 키 및 ID를 사용하여 꼭짓점 또는 에지를 읽을 수 있습니다. 예를 들어, 지역("미국")의 그래프가 파티션 키이고 "시애틀"이 행 키인 경우 다음 구문을 사용하여 꼭짓점을 찾을 수 있습니다.

```
g.V(['USA', 'Seattle'])
```

에지와 동일하게 파티션 키와 행 키를 사용하는 에지를 참조할 수 있습니다.

```
g.E(['USA', 'I5'])
```

자세한 내용은 [Cosmos DB에 대한 Gremlin 지원](gremlin-support.md)을 참조하세요.


<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>분할 디자인
Azure Cosmos DB를 효과적으로 확장하려면 컨테이너를 만들 때 적절한 파티션 키를 선택해야 합니다. 파티션 키를 선택하는 두 가지 주요 고려 사항이 있습니다.

* **쿼리 및 트랜잭션의 경계**: 파티션 키를 선택할 때는 트랜잭션 사용에 대한 요구 사항과 여러 파티션 키에 엔터티를 분산하는 데 대한 요구 사항 간에 균형을 유지하여 솔루션의 확장성을 보장해야 합니다. 한 쪽으로 치우치면 모든 항목에 동일한 파티션 키를 설정할 수 있지만 솔루션의 확장성이 제한될 수 있습니다. 다른 쪽으로 치우치면 각 항목에 고유한 파티션 키를 할당할 수 있으므로 확장성이 매우 높지만 저장 프로시저 및 트리거를 통해 문서 트랜잭션 간에 사용할 수 없습니다. 이상적인 파티션 키는 효율적인 쿼리를 사용할 수 있도록 하고 솔루션을 확장할 수 있는 충분한 카디널리티가 있는 키입니다. 
* **저장소 및 성능 병목 현상 없음**: 쓰기를 다양한 고유 값에 분산할 수 있는 속성을 선택해야 합니다. 동일한 파티션 키에 대한 요청은 단일 파티션의 처리량을 초과할 수 없으며 제한됩니다. 따라서 응용 프로그램 내에서 "핫 스폿"을 초래하지 않는 파티션 키를 선택해야 합니다. 단일 파티션 키에 대한 모든 데이터는 파티션 내에 저장해야 하므로 같은 가격에 데이터 볼륨이 많은 파티션 키는 피하는 것이 좋습니다. 

몇 가지 실제 시나리오 및 적절한 파티션 키를 각각 살펴보겠습니다.
* 사용자 프로필 백 엔드를 구현하는 경우 사용자 ID를 파티션 키로 선택하는 것이 좋습니다.
* IoT 데이터(예: 장치 상태)를 저장하는 경우 장치 ID를 파티션 키로 선택하는 것이 좋습니다.
* 시계열 데이터를 기록하는 데 DocumentDB를 사용하는 경우 호스트 이름 또는 프로세스 ID를 파티션 키로 선택하는 것이 좋습니다.
* 다중 테넌트 아키텍처가 있는 경우 테넌트 ID를 파티션 키로 선택하는 것이 좋습니다.

일부 사례(예: IoT 및 사용자 프로필)에서는 파티션 키가 사용자 ID(문서 키)와 동일할 수 있습니다. 시계열 데이터와 같은 다른 사례에서는 id와 다른 파티션 키를 선택할 수 있습니다.

### <a name="partitioning-and-loggingtime-series-data"></a>분할 및 로깅/시계열 데이터
Cosmos DB의 일반적인 사용 사례 중 하나는 로깅 및 원격 분석입니다. 방대한 양의 데이터를 읽기/쓰기 해야 할 수 있으므로 적절한 파티션 키를 선택해야 합니다. 사용자의 읽기 및 쓰기 비율 및 실행할 쿼리의 종류에 따라 선택이 달라집니다. 다음은 적절한 파티션 키를 선택하는 방법에 대한 몇 가지 팁입니다.

* 사용 사례가 오랜 기간 동안 축적하는 작은 비율의 쓰기를 포함하고 타임스탬프 및 다른 필터의 범위를 기준으로 쿼리해야 하는 경우 파티션 키로 타임스탬프(예: 날짜)의 롤업을 사용하는 것이 좋은 방법입니다. 이렇게 하면 단일 파티션에서 날짜에 대한 모든 데이터를 쿼리할 수 있습니다. 
* 일반적으로 워크로드에 쓰기 작업이 많은 경우 Cosmos DB가 다양한 파티션에 쓰기를 균등하게 분산할 수 있도록 타임스탬프에 기반하지 않는 파티션 키를 사용해야 합니다. 여기서 높은 카디널리티의 호스트 이름, 프로세스 ID, 작업 ID 또는 다른 속성을 선택하는 것이 좋습니다. 
* 세 번째 방법은 여러 컨테이너가 있는(각 일/월에 대해 하나) 하이브리드이며 파티션 키는 호스트 이름처럼 세부적인 속성입니다. 시간 창에 기반한 다양한 처리량을 설정할 수 있다는 장점이 있습니다. 예를 들어 현재 월에 대한 컨테이너는 읽기 및 쓰기를 제공하므로 높은 처리량으로 프로비전되는 반면 이전 달은 읽기만을 제공하므로 낮은 처리량으로 프로비전됩니다.

### <a name="partitioning-and-multi-tenancy"></a>분할 및 다중 테넌트
Cosmos DB를 사용하여 다중 테넌트 응용 프로그램을 구현하는 경우 테넌트당 하나의 파티션 키 및 테넌트당 하나의 컨테이너라는 두 가지 인기 있는 패턴이 있습니다. 각각의 장단점은 다음과 같습니다.

* 테넌트당 하나의 파티션 키: 이 모델에서는 테넌트가 단일 컨테이너 내에 함께 배치됩니다. 그러나 단일 파티션 내의 항목에 대한 쿼리 및 삽입을 단일 파티션에 대해 수행할 수 있습니다. 또한 테넌트 내의 모든 항목에서 트랜잭션 논리를 구현할 수 있습니다. 다중 테넌트는 컨테이너를 공유하기 때문에 각 테넌트에 대한 추가 헤드룸을 프로비전하는 대신 테넌트에 대한 리소스를 풀링하여 저장소 및 처리량을 단일 컨테이너 내에 저장할 수 있습니다. 단점은 테넌트당 성능 격리가 없다는 점입니다. 성능/처리량 증가가 대상 테넌트에 대한 증가가 아니라 전체 컨테이너에 적용됩니다.
* 테넌트당 하나의 컨테이너: 각 테넌트에 고유한 컨테이너가 있습니다. 이 모델에서는 테넌트당 성능을 예약할 수 있습니다. Cosmos DB의 새로운 프로비전 가격 책정 모델을 사용하는 이 모델은 몇몇 테넌트가 있는 다중 테넌트 응용 프로그램에 보다 비용 효율적입니다.

소수의 테넌트를 함께 배치하고 더 큰 테넌트를 고유한 컨테이너로 마이그레이션하는 조합/계층형 접근 방식을 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Cosmos DB API를 사용하여 분할하기 위한 개념 및 모범 사례의 개요를 제공했습니다. 

* [Azure Cosmos DB에서 프로비전된 처리량](../documentdb/documentdb-request-units.md)에 대한 자세한 정보
* [Azure Cosmos DB에서 글로벌 배포](../documentdb/documentdb-distribute-data-globally.md)에 대한 자세한 정보




