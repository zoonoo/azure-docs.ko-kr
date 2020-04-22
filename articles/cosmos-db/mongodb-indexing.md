---
title: MongoDB에 대한 Azure 코스모스 DB의 API에서 인덱싱 관리
description: 이 문서에서는 MongoDB API를 사용하여 Azure Cosmos DB 인덱싱 기능에 대한 개요를 제공합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732714"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대한 Azure 코스모스 DB의 API에서 인덱싱 관리

몽고DB용 Azure 코스모스 DB의 API는 Azure 코스모스 DB의 핵심 인덱스 관리 기능을 활용합니다. 이 문서에서는 MongoDB에 대한 Azure Cosmos DB의 API를 사용하여 인덱스를 추가하는 방법에 대해 중점합니다. 또한 모든 API에서 관련이 있는 [Azure Cosmos DB의 인덱싱 개요를](index-overview.md) 읽을 수도 있습니다.

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB 서버 버전 에 대한 인덱싱 3.6

MongoDB 서버 버전 3.6에 대한 Azure Cosmos DB의 API는 `_id` 필드를 자동으로 인덱싱하므로 삭제할 수 없습니다. 샤드 키당 `_id` 필드의 고유성을 자동으로 적용합니다.

추가 필드를 인덱싱하려면 MongoDB 인덱스 관리 명령을 적용합니다. MongoDB에서와 마찬가지로 MongoDB용 Azure 코스모스 DB의 API는 `_id` 필드를 자동으로 인덱싱합니다. 이 기본 인덱싱 정책은 기본적으로 모든 필드를 인덱싱하는 Azure Cosmos DB SQL API와 다릅니다.

쿼리에 정렬을 적용하려면 정렬 작업에 사용되는 필드에 인덱스를 만들어야 합니다.

## <a name="index-types"></a>인덱스 형식

### <a name="single-field"></a>단일 필드

단일 필드에 인덱스를 만들 수 있습니다. 단일 필드 인덱스의 정렬 순서는 중요하지 않습니다. 다음 명령은 필드에 `name`인덱스를 만듭니다.

`db.coll.createIndex({name:1})`

한 쿼리는 사용 가능한 경우 여러 단일 필드 인덱스를 사용합니다. 컨테이너당 최대 500개의 단일 필드 인덱스를 만들 수 있습니다.

### <a name="compound-indexes-mongodb-server-version-36"></a>복합 인덱스(몽고DB 서버 버전 3.6)

MongoDB용 Azure Cosmos DB의 API는 버전 3.6 와이어 프로토콜을 사용하는 계정에 대한 복합 인덱스를 지원합니다. 복합 인덱스에 최대 8개의 필드를 포함할 수 있습니다. MongoDB와 달리 쿼리가 한 번에 여러 필드에서 효율적으로 정렬해야 하는 경우에만 복합 인덱스를 만들어야 합니다. 정렬할 필요가 없는 필터가 여러 개인 쿼리의 경우 단일 복합 인덱스 대신 여러 단일 필드 인덱스를 만듭니다.

다음 명령은 필드에 복합 인덱스를 `name` `age`만들고 다음을 수행합니다.

`db.coll.createIndex({name:1,age:1})`

다음 예제와 같이 복합 인덱스를 사용하여 여러 필드에서 한 번에 효율적으로 정렬할 수 있습니다.

`db.coll.find().sort({name:1,age:1})`

앞의 복합 인덱스를 사용하여 모든 필드에서 반대 정렬 순서로 쿼리를 효율적으로 정렬할 수도 있습니다. 예를 들면 다음과 같습니다.

`db.coll.find().sort({name:-1,age:-1})`

그러나 복합 인덱스의 경로 시퀀스는 쿼리와 정확히 일치해야 합니다. 다음은 추가 복합 인덱스가 필요한 쿼리의 예입니다.

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>멀티키 인덱스

Azure Cosmos DB는 배열에 저장된 콘텐츠를 인덱싱하기 위해 다중 키 인덱스를 만듭니다. 배열 값을 가진 필드를 인덱싱하는 경우 Azure Cosmos DB는 배열의 모든 요소를 자동으로 인덱싱합니다.

### <a name="geospatial-indexes"></a>지리 공간 지수

많은 지리 공간 운영자는 지리 공간 지수의 혜택을 누릴 수 있습니다. 현재 MongoDB용 Azure 코스모스 DB의 `2dsphere` API는 인덱스를 지원합니다. API는 아직 인덱스를 지원하지 `2d` 않습니다.

필드에 지리 공간 인덱스를 만드는 예는 `location` 다음과 같습니다.

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>텍스트 인덱스

MongoDB에 대한 Azure 코스모스 DB의 API는 현재 텍스트 인덱스를 지원하지 않습니다. 문자열에 대한 텍스트 검색 쿼리의 경우 Azure Cosmos DB와 [Azure 인지 검색](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) 통합을 사용해야 합니다.

## <a name="index-properties"></a>인덱스 속성

다음 작업은 유선 프로토콜 버전 3.6을 서비스하는 계정과 이전 버전을 제공하는 계정에 일반적입니다. [지원되는 인덱스 및 인덱싱된 속성에](mongodb-feature-support-36.md#indexes-and-index-properties)대해 자세히 알아볼 수 있습니다.

### <a name="unique-indexes"></a>고유 인덱스

[고유 인덱스는](unique-keys.md) 두 개 이상의 문서가 인덱싱된 필드에 대해 동일한 값을 포함하지 않도록 적용하는 데 유용합니다.

> [!IMPORTANT]
> 고유한 인덱스는 컬렉션이 비어 있는 경우에만 만들 수 있습니다(문서가 없음).

다음 명령은 필드에 `student_id`고유한 인덱스를 만듭니다.

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

샤드 컬렉션의 경우 고유한 인덱스를 만들려면 샤드(파티션) 키를 제공해야 합니다. 즉, 분할된 컬렉션의 모든 고유 인덱스는 필드 중 하나가 파티션 키인 복합 인덱스입니다.

```coll``` 다음 명령은 필드에 ```university``` `student_id` `university`고유한 인덱스가 있는 샤드 컬렉션(샤드 키)을 만듭니다.

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

위의 예제에서 절을 ```"university":1``` 생략하면 다음 메시지와 함께 오류가 반환됩니다.

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 인덱스

특정 컬렉션에서 문서 만료를 활성화하려면 [TTL(Time-to-Live) 인덱스를](../cosmos-db/time-to-live.md)만들어야 합니다. TTL 인덱스는 값이 있는 `_ts` 필드의 `expireAfterSeconds` 인덱스입니다.

예:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

앞의 명령은 지난 10초 ```db.coll``` 동안 수정되지 않은 컬렉션의 모든 문서를 삭제합니다.

> [!NOTE]
> **_ts** 필드는 Azure 코스모스 DB에만 해당되며 MongoDB 클라이언트에서 액세스할 수 없습니다. 문서의 마지막 수정 에 대한 타임스탬프가 포함된 예약된(시스템) 속성입니다.

## <a name="track-index-progress"></a>인덱스 진행률 추적

MongoDB용 Azure Cosmos DB의 API 버전 3.6은 데이터베이스 인스턴스에서 인덱스 진행률을 추적하는 `currentOp()` 명령을 지원합니다. 이 명령은 데이터베이스 인스턴스에서 진행 중인 작업에 대한 정보가 포함된 문서를 반환합니다. 명령을 `currentOp` 사용하여 네이티브 MongoDB의 진행 중인 모든 작업을 추적합니다. MongoDB에 대한 Azure Cosmos DB의 API에서 이 명령은 인덱스 작업 추적만 지원합니다.

다음은 `currentOp` 명령을 사용하여 인덱스 진행률을 추적하는 방법을 보여 주는 몇 가지 예입니다.

* 컬렉션에 대한 인덱스 진행률을 가져옵니다.

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 데이터베이스의 모든 컬렉션에 대한 인덱스 진행률을 가져옵니다.

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Azure Cosmos 계정의 모든 데이터베이스 및 컬렉션에 대한 인덱스 진행률을 가져옵니다.

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>인덱스 진행률 출력의 예

인덱스 진행 률 세부 정보는 현재 인덱스 작업에 대한 진행률을 표시합니다. 다음은 인덱스 진행의 여러 단계에 대한 출력 문서 형식을 보여 주는 예제입니다.

- "foo" 컬렉션및 60% 완료된 "막대" 데이터베이스의 인덱스 작업에는 다음 출력 문서가 있습니다. 필드에는 `Inprog[0].progress.total` 목표 완료 율로 100이 표시됩니다.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

- 인덱스 작업이 "foo" 컬렉션 및 "막대" 데이터베이스에서 방금 시작된 경우 측정 가능한 수준에 도달할 때까지 출력 문서에 0% 진행률을 표시할 수 있습니다.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

- 진행 중 인덱스 작업이 완료되면 출력 문서에 `inprog` 빈 작업이 표시됩니다.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>배경 인덱스 업데이트

**Background** 인덱스 속성에 대해 지정된 값에 관계없이 인덱스 업데이트는 항상 백그라운드에서 수행됩니다. 인덱스 업데이트는 다른 데이터베이스 작업보다 낮은 우선 순위로 요청 단위(RUs)를 사용하므로 인덱스 변경으로 인해 쓰기, 업데이트 또는 삭제에 대한 가동 중지 시간이 발생하지 않습니다.

새 인덱스를 추가하면 쿼리가 즉시 인덱스를 사용합니다. 즉, 쿼리가 일치하는 모든 결과를 반환하지 않을 수 있으며 오류를 반환하지 않고 반환합니다. 인덱스 변환이 완료되면 쿼리 결과가 일관되게 표시됩니다. [색인 진행률을 추적할](#track-index-progress)수 있습니다.

## <a name="migrate-collections-with-indexes"></a>인덱스를 통해 컬렉션 마이그레이션

현재 컬렉션에 문서가 없는 경우에만 고유한 인덱스를 만들 수 있습니다. 인기있는 MongoDB 마이그레이션 도구는 데이터를 가져온 후 고유한 인덱스를 만들려고 합니다. 이 문제를 해결하려면 마이그레이션 도구를 사용해 두지 않고 해당 컬렉션과 고유한 인덱스를 수동으로 만들 수 있습니다. 명령줄에 플래그를 `--noIndexRestore` ```mongorestore``` 사용하여 이 동작을 수행할 수 있습니다.

## <a name="indexing-for-mongodb-version-32"></a>몽고DB 버전 에 대한 인덱싱 3.2

사용 가능한 인덱싱 기능 및 기본값은 MongoDB 와이어 프로토콜의 버전 3.2와 호환되는 Azure Cosmos 계정에 대해 다릅니다. [계정의 버전을 확인할](mongodb-feature-support-36.md#protocol-support)수 있습니다. [지원 요청을](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)제출하여 3.6 버전으로 업그레이드할 수 있습니다.

버전 3.2를 사용하는 경우 이 섹션에서는 버전 3.6의 주요 차이점을 간략하게 설명합니다.

### <a name="dropping-default-indexes-version-32"></a>기본 인덱스 삭제(버전 3.2)

MongoDB용 Azure Cosmos DB의 API의 3.6 버전과 달리 버전 3.2는 기본적으로 모든 속성을 인덱스합니다. 다음 명령을 사용하여 컬렉션에 대한 이러한 기본 인덱스를 삭제할 수 있습니다(```coll```)

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

기본 인덱스를 삭제한 후 버전 3.6에서와 마찬가지로 인덱스를 더 추가할 수 있습니다.

### <a name="compound-indexes-version-32"></a>복합 지수(버전 3.2)

복합 인덱스는 문서의 여러 필드에 대한 참조를 유지합니다. 복합 인덱스를 만들려면 [지원 요청을](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)제출하여 버전 3.6으로 업그레이드합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
