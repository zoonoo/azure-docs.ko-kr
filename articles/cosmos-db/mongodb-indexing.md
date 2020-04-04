---
title: Azure Cosmos DB의 API for MongoDB에서 인덱싱
description: Azure Cosmos DB의 API for MongoDB의 인덱싱 기능의 개요를 설명합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: f3f369928270c77557337bfdb1037cc5174c39f2
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637954"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB를 사용하는 인덱싱

몽고DB용 Azure 코스모스 DB의 API는 Azure 코스모스 DB의 핵심 인덱스 관리 기능을 활용합니다. 이 문서에서는 MongoDB에 대한 Azure Cosmos DB의 API를 사용하여 인덱스를 추가하는 방법에 대해 중점합니다. 또한 모든 API에서 관련이 있는 [Azure Cosmos DB의 인덱싱 개요를](index-overview.md) 읽을 수도 있습니다.

## <a name="indexing-for-version-36"></a>버전 3.6에 대한 인덱싱

`_id` 필드는 항상 자동으로 인덱싱되며 삭제할 수 없습니다. MongoDB에 대한 Azure Cosmos DB의 API는 샤드 키당 `_id` 필드의 고유성을 자동으로 적용합니다.

추가 필드를 인덱싱하려면 MongoDB 인덱스 관리 명령을 적용해야 합니다. MongoDB에서와 마찬가지로 `_id` 필드만 자동으로 인덱싱됩니다. 이 기본 인덱싱 정책은 기본적으로 모든 필드를 인덱싱하는 Azure Cosmos DB SQL API와 다릅니다.

쿼리에 정렬을 적용하려면 정렬 작업에 사용되는 필드에 인덱스를 만들어야 합니다.

## <a name="index-types"></a>인덱스 형식

### <a name="single-field"></a>단일 필드

단일 필드에 인덱스를 만들 수 있습니다. 단일 필드 인덱스의 정렬 순서는 중요하지 않습니다. 다음 명령은 필드에 `name`인덱스를 만듭니다.

`db.coll.createIndex({name:1})`

한 쿼리는 여러 단일 필드 인덱스(사용 가능한 경우)를 활용합니다. 컨테이너당 최대 500개의 단일 필드 인덱스를 만들 수 있습니다.

### <a name="compound-indexes-36"></a>복합지수 (3.6)

복합 인덱스는 3.6 와이어 프로토콜을 사용하는 계정에 대해 지원됩니다. 복합 인덱스에 최대 8개의 필드를 포함할 수 있습니다. MongoDB와 달리 쿼리가 한 번에 여러 필드에서 효율적으로 정렬해야 하는 경우에만 복합 인덱스를 만들어야 합니다. 정렬할 필요가 없는 필터가 여러 개인 쿼리의 경우 단일 복합 인덱스 대신 여러 개의 단일 필드 인덱스를 만들어야 합니다.

다음 명령은 필드에 복합 인덱스를 `name` 만들고 `age`다음을 수행합니다.

`db.coll.createIndex({name:1,age:1})`

복합 인덱스를 사용하여 다음과 같은 여러 필드에서 한 번에 효율적으로 정렬할 수 있습니다.

`db.coll.find().sort({name:1,age:1})`

위의 복합 인덱스는 모든 필드에서 반대 정렬 순서로 쿼리를 효율적으로 정렬하는 데 사용할 수도 있습니다. 예를 들면 다음과 같습니다.

`db.coll.find().sort({name:-1,age:-1})`

그러나 복합 인덱스의 경로 시퀀스는 쿼리와 정확히 일치해야 합니다. 다음은 추가 복합 인덱스가 필요한 쿼리의 예입니다.

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>멀티키 인덱스

Azure Cosmos DB는 배열에 저장된 콘텐츠를 인덱싱하기 위해 다중 키 인덱스를 만듭니다. 배열 값을 가진 필드를 인덱싱하는 경우 Azure Cosmos DB는 배열의 모든 요소를 자동으로 인덱싱합니다.

### <a name="geospatial-indexes"></a>지리 공간 지수

많은 지리 공간 운영자는 지리 공간 지수의 혜택을 누릴 수 있습니다. 현재 MongoDB용 Azure 코스모스 DB의 `2dsphere` API는 인덱스를 지원합니다. `2d`인덱스는 아직 지원되지 않습니다.

필드에 지리 공간 인덱스를 만드는 예는 `location` 다음과 같습니다.

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>텍스트 인덱스

텍스트 인덱스는 현재 지원되지 않습니다. 문자열에 대한 텍스트 검색 쿼리의 경우 Azure Cosmos DB와 [Azure Cognitive Search의](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) 통합을 사용해야 합니다.

## <a name="index-properties"></a>인덱스 속성

다음 작업은 와이어 프로토콜 버전 3.6을 제공하는 계정과 이전 와이어 프로토콜 버전을 제공하는 계정에 공통적입니다. [지원되는 인덱스 및 인덱싱된 속성에](mongodb-feature-support-36.md#indexes-and-index-properties)대해 자세히 알아볼 수도 있습니다.

### <a name="unique-indexes"></a>고유 인덱스

[고유 인덱스](unique-keys.md)는 두 개 이상의 문서에 인덱싱된 필드에 대해 동일한 값을 포함하지 않도록 하는 데 유용합니다.

>[!Important]
> 고유한 인덱스는 컬렉션이 비어 있는 경우에만 만들 수 있습니다(문서가 없음).

다음 명령은 “student_id” 필드에 고유 인덱스를 만듭니다.

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

다음 명령은 student_id 및 university 필드에서 고유 인덱스로 분할된 컬렉션 ```coll```(분할 키는 ```university```)을 만듭니다.

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

위의 예에서 ```"university":1``` 절이 생략되면 다음 메시지와 함께 오류가 반환됩니다.

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 인덱스

특정 컬렉션에서 문서 만료를 활성화하려면 ["TTL index"(Time to Live 인덱스)](../cosmos-db/time-to-live.md)를 만들어야 합니다. TTL 인덱스는 "expireAfterSeconds" 값을 가진 _ts 필드의 인덱스입니다.

예제:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

위의 명령은 마지막 10초에서 수정되지 않은 ```db.coll``` 컬렉션의 문서를 삭제합니다.

> [!NOTE]
> **_ts** Azure 코스모스 DB 관련 필드이며 MongoDB 클라이언트에서 액세스할 수 없습니다. 문서의 마지막 수정의 타임스탬프를 포함하는 예약된(시스템) 속성입니다.

## <a name="track-the-index-progress"></a>인덱스 진행률 추적

MongoDB 계정에 대한 Azure Cosmos DB의 API의 3.6 버전은 데이터베이스 인스턴스에서 `currentOp()` 인덱스 진행률을 추적하는 명령을 지원합니다. 이 명령은 데이터베이스 인스턴스에서 진행 중인 작업에 대한 정보가 포함된 문서를 반환합니다. 이 `currentOp` 명령은 네이티브 MongoDB의 모든 진행 중인 작업을 추적하는 데 사용되지만 MongoDB에 대한 Azure Cosmos DB의 API에서는 이 명령은 인덱스 작업 추적만 지원합니다.

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

인덱스 진행 률 세부 정보에는 현재 인덱스 작업에 대한 진행률비율이 포함됩니다. 다음 예제에서는 인덱스 진행의 여러 단계에 대한 출력 문서 형식을 보여 주었습니다.

1. 인덱싱이 완료된 'foo' 컬렉션과 'bar' 데이터베이스의 인덱스 작업이 완료되면 다음 출력 문서가 표시됩니다. `Inprog[0].progress.total`100을 대상 완료로 표시합니다.

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

2. 'foo' 컬렉션 및 'bar' 데이터베이스에서 방금 시작된 인덱스 작업의 경우 측정 가능한 수준에 도달할 때까지 출력 문서가 0% 진행률을 표시할 수 있습니다.

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

3. 진행 중인 인덱스 작업이 완료되면 출력 문서에 빈 inprog 작업이 표시됩니다.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>배경 인덱스 업데이트

**Background** 인덱스 속성에 대해 지정된 값에 관계없이 인덱스 업데이트는 항상 백그라운드에서 수행됩니다. 인덱스 업데이트는 다른 데이터베이스 작업보다 RU를 우선 순위가 낮게 사용합니다. 따라서 인덱스 변경으로 인해 쓰기, 업데이트 또는 삭제에 대한 가동 중지 시간이 발생하지 않습니다.

새 인덱스를 추가할 때 쿼리는 즉시 인덱스를 활용합니다. 즉, 쿼리가 일치하는 모든 결과를 반환하지 않을 수 있으며 오류를 반환하지 않고 반환합니다. 인덱스 변환이 완료되면 쿼리 결과가 일관되게 표시됩니다. 인덱스 [진행률을 추적할](#track-the-index-progress)수 있습니다.

## <a name="migrating-collections-with-indexes"></a>인덱스를 사용하여 컬렉션 마이그레이션

현재 컬렉션에 문서가 포함되지 않은 경우에만 고유 인덱스를 만들 수 있습니다. 인기 있는 MongoDB 마이그레이션 도구는 데이터를 가져온 후 고유 인덱스를 만들도록 시도합니다. 이 문제를 우회하려면 사용자가 마이그레이션 도구를 허용하는 대신 해당 컬렉션과 고유 인덱스를 수동으로 만드는 것이 ```mongorestore``` 좋습니다(명령줄의 `--noIndexRestore` 플래그를 사용하여 이 동작이 수행됩니다).

## <a name="indexing-for-version-32"></a>버전 3.2에 대한 인덱싱

MongoDB 와이어 프로토콜의 3.2 버전과 호환되는 Azure Cosmos DB 계정의 경우 사용 가능한 인덱싱 기능 및 기본값이 다릅니다. [계정의 버전을 확인할](mongodb-feature-support-36.md#protocol-support)수 있습니다. [지원 요청을](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)제출하여 3.6 버전으로 업그레이드할 수 있습니다.

버전 3.2를 사용하는 경우 이 섹션에서는 버전 3.6의 주요 차이점을 간략하게 설명합니다.

### <a name="dropping-the-default-indexes-32"></a>기본 인덱스 삭제(3.2)

MongoDB용 Azure Cosmos DB의 API의 3.6 버전과 달리 3.2 버전은 기본적으로 모든 속성을 인덱싱합니다. 다음 명령을 사용하여 컬렉션에 ```coll```대한 이러한 기본 인덱스를 삭제할 수 있습니다.

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

기본 인덱스를 삭제한 후 버전 3.6에서 수행한 대로 추가 인덱스를 추가할 수 있습니다.

### <a name="compound-indexes-32"></a>복합지수 (3.2)

복합 인덱스는 문서의 여러 필드에 대한 참조를 유지합니다. 복합 인덱스를 만들려면 [지원 요청을](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)제출하여 3.6 버전으로 업그레이드하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
