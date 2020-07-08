---
title: MongoDB에 대 한 Azure Cosmos DB의 API에서 인덱싱 관리
description: 이 문서에서는 MongoDB API를 사용 하 여 Azure Cosmos DB 인덱싱 기능에 대 한 개요를 제공 합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 06/16/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e0b14eefcc0b484c92faf1148ae2972f51b04d31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260698"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB의 API에서 인덱싱 관리

Azure Cosmos DB의 MongoDB API는 Azure Cosmos DB의 핵심 인덱스 관리 기능을 활용 합니다. 이 문서에서는 Azure Cosmos DB의 MongoDB API를 사용 하 여 인덱스를 추가 하는 방법을 중점적으로 설명 합니다. 모든 Api에서 관련 된 [Azure Cosmos DB의 인덱싱 개요](index-overview.md) 를 읽을 수도 있습니다.

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB 서버 버전 3.6에 대 한 인덱싱

MongoDB server 버전 3.6에 대 한 Azure Cosmos DB API `_id` 는 필드를 자동으로 인덱싱하고 삭제할 수 없습니다. 분할 된 키 당 필드의 고유성을 자동으로 적용 합니다 `_id` . MongoDB에 대 한 Azure Cosmos DB API에서는 분할 및 인덱싱이 별도의 개념입니다. 분할 키를 인덱싱할 필요가 없습니다. 그러나 문서의 다른 속성과 마찬가지로이 속성이 쿼리에서 일반 필터 이면 분할 키를 인덱싱하는 것이 좋습니다.

추가 필드를 인덱싱 하려면 MongoDB 인덱스 관리 명령을 적용 합니다. MongoDB에서와 마찬가지로, MongoDB에 대 한 Azure Cosmos DB API는 자동으로 필드를 인덱싱합니다 `_id` . 이 기본 인덱싱 정책은 모든 필드를 기본적으로 인덱싱하는 Azure Cosmos DB SQL API와 다릅니다.

쿼리에 정렬을 적용 하려면 정렬 작업에 사용 되는 필드에 대 한 인덱스를 만들어야 합니다.

## <a name="index-types"></a>인덱스 형식

### <a name="single-field"></a>단일 필드

단일 필드에 인덱스를 만들 수 있습니다. 단일 필드 인덱스의 정렬 순서는 중요 하지 않습니다. 다음 명령은 필드에 인덱스를 만듭니다 `name` .

`db.coll.createIndex({name:1})`

사용 가능한 경우 하나의 쿼리에서 여러 개의 단일 필드 인덱스를 사용 합니다. 컨테이너 당 최대 500 개의 단일 필드 인덱스를 만들 수 있습니다.

### <a name="compound-indexes-mongodb-server-version-36"></a>복합 인덱스 (MongoDB server 버전 3.6)

Azure Cosmos DB의 MongoDB 용 API는 버전 3.6 유선 프로토콜을 사용 하는 계정에 대 한 복합 인덱스를 지원 합니다. 복합 인덱스에 최대 8 개의 필드를 포함할 수 있습니다. MongoDB와 달리 쿼리가 한 번에 여러 필드에서 효율적으로 정렬 되어야 하는 경우에만 복합 인덱스를 만들어야 합니다. 정렬할 필요가 없는 여러 필터가 포함 된 쿼리의 경우 단일 복합 인덱스 대신 단일 필드 인덱스를 여러 개 만듭니다.

다음 명령은 및 필드에 복합 인덱스를 만듭니다 `name` `age` .

`db.coll.createIndex({name:1,age:1})`

다음 예제와 같이 복합 인덱스를 사용 하 여 여러 필드를 한 번에 효율적으로 정렬할 수 있습니다.

`db.coll.find().sort({name:1,age:1})`

앞의 복합 인덱스를 사용 하 여 모든 필드에서 정렬 순서가 반대인 쿼리를 효율적으로 정렬할 수도 있습니다. 예를 들면 다음과 같습니다.

`db.coll.find().sort({name:-1,age:-1})`

그러나 복합 인덱스의 경로 시퀀스는 쿼리와 정확히 일치 해야 합니다. 다음은 추가 복합 인덱스가 필요한 쿼리의 예입니다.

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Multikey 인덱스

Azure Cosmos DB는 배열에 저장 된 콘텐츠를 인덱싱하는 여러 키 인덱스를 만듭니다. 배열 값을 사용 하 여 필드를 인덱싱하는 경우 Azure Cosmos DB는 배열의 모든 요소를 자동으로 인덱싱합니다.

### <a name="geospatial-indexes"></a>지리 공간적 인덱스

지리 공간적 연산자는 대부분 지리 공간적 인덱스를 활용 합니다. 현재 MongoDB에 대 한 Azure Cosmos DB API는 `2dsphere` 인덱스를 지원 합니다. API는 인덱스를 아직 지원 하지 않습니다 `2d` .

필드에 지리 공간적 인덱스를 만드는 예는 `location` 다음과 같습니다.

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>텍스트 인덱스

Azure Cosmos DB의 MongoDB API는 현재 텍스트 인덱스를 지원 하지 않습니다. 문자열에 대 한 텍스트 검색 쿼리를 사용 하려면 Azure Cosmos DB와 [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) 통합을 사용 해야 합니다.

## <a name="wildcard-indexes"></a>와일드 카드 인덱스

와일드 카드 인덱스를 사용 하 여 알 수 없는 필드에 대해 쿼리를 지원할 수 있습니다. 패밀리에 대 한 데이터를 보유 하는 컬렉션이 있다고 가정해 보겠습니다.

해당 컬렉션의 예제 문서에는 다음이 포함 됩니다.

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

이번에는에서 약간 다른 속성 집합을 사용 하는 또 다른 예가 `children` 있습니다.

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

이 컬렉션에서 문서에는 여러 가지 가능한 속성이 있을 수 있습니다. 배열의 모든 데이터를 인덱싱합니다 `children` . 각 개별 속성에 대해 별도의 인덱스를 만들거나 전체 배열에 대해 하나의 와일드 카드 인덱스를 만드는 두 가지 옵션이 있습니다. `children`

### <a name="create-a-wildcard-index"></a>와일드 카드 인덱스 만들기

다음 명령은 내의 모든 속성에 와일드 카드 인덱스를 만듭니다 `children` .

`db.coll.createIndex({"children.$**" : 1})`

**MongoDB와 달리 와일드 카드 인덱스는 쿼리 조건자의 여러 필드를 지원할 수 있습니다**. 각 속성에 대 한 별도의 인덱스를 만드는 대신 하나의 와일드 카드 인덱스를 사용 하는 경우 쿼리 성능에 차이가 없습니다.

와일드 카드 구문을 사용 하 여 다음과 같은 인덱스 유형을 만들 수 있습니다.

- 단일 필드
- 지리 공간적

### <a name="indexing-all-properties"></a>모든 속성 인덱싱

모든 필드에 와일드 카드 인덱스를 만드는 방법은 다음과 같습니다.

`db.coll.createIndex( { "$**" : 1 } )`

개발을 시작할 때 모든 필드에 와일드 카드 인덱스를 만드는 것이 유용할 수 있습니다. 문서에 더 많은 속성이 인덱싱되 면 문서를 작성 하 고 업데이트 하는 데 필요한 부담 (요청 단위) 요금은 늘어납니다. 따라서 쓰기 작업이 많은 경우에는 와일드 카드 인덱스를 사용 하는 대신 개별적으로 경로를 인덱싱합니다.

### <a name="limitations"></a>제한 사항

와일드 카드 인덱스는 다음 인덱스 유형이 나 속성을 지원 하지 않습니다.

- 복합
- TTL
- 고유한

**MongoDB와 달리**MONGODB의 API Azure Cosmos DB에서 다음에 대 한 와일드 카드 인덱스를 사용할 **수 없습니다** .

- 여러 특정 필드를 포함 하는 와일드 카드 인덱스 만들기

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- 여러 특정 필드를 제외 하는 와일드 카드 인덱스 만들기

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

또는 와일드 카드 인덱스를 여러 개 만들 수 있습니다.

## <a name="index-properties"></a>인덱스 속성

다음 작업은 유선 프로토콜 버전 3.6 및 이전 버전을 제공 하는 계정에서 제공 하는 계정에 일반적입니다. [지원 되는 인덱스 및 인덱싱된 속성](mongodb-feature-support-36.md#indexes-and-index-properties)에 대해 자세히 알아볼 수 있습니다.

### <a name="unique-indexes"></a>고유 인덱스

[고유 인덱스](unique-keys.md) 는 두 개 이상의 문서에 인덱싱된 필드에 대해 동일한 값이 포함 되지 않도록 하는 데 유용 합니다.

> [!IMPORTANT]
> 컬렉션이 비어 있는 경우 (문서를 포함 하지 않음)에만 고유 인덱스를 만들 수 있습니다.

다음 명령은 필드에 고유 인덱스를 만듭니다 `student_id` .

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

분할 된 컬렉션의 경우 고유 인덱스를 만들려면 분할 된 (파티션) 키를 제공 해야 합니다. 즉, 분할된 컬렉션의 모든 고유 인덱스는 필드 중 하나가 파티션 키인 복합 인덱스입니다.

다음 명령은 ```coll``` ```university``` 및 필드에 고유 인덱스가 있는 분할 된 컬렉션 (분할 키)을 만듭니다 `student_id` `university` .

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

앞의 예제에서 절을 생략 하면 ```"university":1``` 다음 메시지와 함께 오류가 반환 됩니다.

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 인덱스

특정 컬렉션에서 문서 만료를 사용 하도록 설정 하려면 [TTL (time-to-live) 인덱스](../cosmos-db/time-to-live.md)를 만들어야 합니다. TTL 인덱스는 값이 있는 필드의 인덱스입니다 `_ts` `expireAfterSeconds` .

예:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

이전 명령은 ```db.coll``` 지난 10 초 동안 수정 되지 않은 컬렉션의 모든 문서를 삭제 합니다.

> [!NOTE]
> **_Ts** 필드는 Azure Cosmos DB에 고유 하며 MongoDB 클라이언트에서 액세스할 수 없습니다. 문서를 마지막으로 수정한 타임 스탬프를 포함 하는 예약 된 (시스템) 속성입니다.

## <a name="track-index-progress"></a>인덱스 추적 진행률

MongoDB에 대 한 Azure Cosmos DB의 API 버전 3.6은 `currentOp()` 데이터베이스 인스턴스의 인덱스 진행률을 추적 하는 명령을 지원 합니다. 이 명령은 데이터베이스 인스턴스에 대 한 진행 중인 작업에 대 한 정보가 포함 된 문서를 반환 합니다. 명령을 사용 하 여 `currentOp` 네이티브 MongoDB의 모든 진행 중인 작업을 추적 합니다. MongoDB에 대 한 Azure Cosmos DB API에서이 명령은 인덱스 작업 추적만 지원 합니다.

다음은 명령을 사용 하 여 인덱스 진행률을 추적 하는 방법을 보여 주는 몇 가지 예입니다 `currentOp` .

* 컬렉션에 대 한 인덱스 진행률 가져오기:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 데이터베이스의 모든 컬렉션에 대 한 인덱스 진행률을 가져옵니다.

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Azure Cosmos 계정에 있는 모든 데이터베이스 및 컬렉션에 대 한 인덱스 진행률을 가져옵니다.

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>인덱스 진행률 출력의 예

인덱스 진행률 세부 정보에는 현재 인덱스 작업에 대 한 진행률 비율이 표시 됩니다. 인덱스 진행률의 여러 단계에 대 한 출력 문서 형식을 보여 주는 예제는 다음과 같습니다.

- "Foo" 컬렉션 및 "bar" 데이터베이스의 인덱스 작업 (60% 완료)은 다음과 같은 출력 문서를 포함 합니다. `Inprog[0].progress.total`필드는 100를 대상 완료 백분율로 표시 합니다.

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

- "Foo" 컬렉션과 "bar" 데이터베이스에서 인덱스 작업을 시작한 경우 측정 가능한 수준에 도달할 때까지 출력 문서에 0% 진행률이 표시 될 수 있습니다.

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

- 진행 중인 인덱스 작업이 완료 되 면 출력 문서에 빈 `inprog` 작업이 표시 됩니다.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>백그라운드 인덱스 업데이트

**백그라운드** 인덱스 속성에 대해 지정 된 값에 관계 없이 인덱스 업데이트는 항상 백그라운드에서 수행 됩니다. 인덱스 업데이트는 다른 데이터베이스 작업 보다 낮은 우선 순위로 요청 단위 (RUs)를 사용 하므로 인덱스를 변경 해도 쓰기, 업데이트 또는 삭제에 대 한 가동 중지 시간이 발생 하지 않습니다.

새 인덱스를 추가 하는 경우 쿼리에서 즉시 인덱스를 사용 합니다. 즉, 쿼리에서 일치 하는 모든 결과를 반환 하지 않고 오류를 반환 하지 않고 그렇게 할 수 있습니다. 인덱스 변환이 완료 되 면 쿼리 결과가 일치 하 게 됩니다. [인덱스 진행률을 추적할](#track-index-progress)수 있습니다.

## <a name="migrate-collections-with-indexes"></a>인덱스를 사용 하 여 컬렉션 마이그레이션

현재 컬렉션에 문서가 포함 되지 않은 경우에만 고유 인덱스를 만들 수 있습니다. 인기 있는 MongoDB 마이그레이션 도구는 데이터를 가져온 후 고유 인덱스를 만들려고 합니다. 이 문제를 피하려면 마이그레이션 도구를 사용해 볼 수 있도록 하는 대신 해당 컬렉션 및 고유 인덱스를 수동으로 만들 수 있습니다. ```mongorestore```명령줄에서 플래그를 사용 하 여에 대해이 동작을 수행할 수 있습니다 `--noIndexRestore` .

## <a name="indexing-for-mongodb-version-32"></a>MongoDB 버전 3.2에 대 한 인덱싱

사용 가능한 인덱싱 기능 및 기본값은 MongoDB 유선 프로토콜의 3.2 버전과 호환 되는 Azure Cosmos 계정에 대해 다릅니다. [계정 버전을 확인할](mongodb-feature-support-36.md#protocol-support)수 있습니다. [지원 요청](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출 하 여 3.6 버전으로 업그레이드할 수 있습니다.

버전 3.2을 사용 하는 경우이 섹션에서는 버전 3.6의 주요 차이점에 대해 간략하게 설명 합니다.

### <a name="dropping-default-indexes-version-32"></a>기본 인덱스 삭제 (버전 3.2)

MongoDB에 대 한 Azure Cosmos DB API의 3.6 버전과 달리 버전 3.2은 기본적으로 모든 속성을 인덱싱합니다. 다음 명령을 사용 하 여 컬렉션에 대 한 기본 인덱스를 삭제할 수 있습니다 ( ```coll``` ).

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

기본 인덱스를 삭제 한 후 버전 3.6에서와 같이 인덱스를 더 추가할 수 있습니다.

### <a name="compound-indexes-version-32"></a>복합 인덱스 (버전 3.2)

복합 인덱스는 문서의 여러 필드에 대한 참조를 유지합니다. 복합 인덱스를 만들려는 경우 [지원 요청](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출 하 여 버전 3.6으로 업그레이드 합니다.

### <a name="wildcard-indexes-version-32"></a>와일드 카드 인덱스 (버전 3.2)

와일드 카드 인덱스를 만들려면 [지원 요청](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 제출 하 여 버전 3.6으로 업그레이드 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
* 분할 및 인덱싱 간의 관계에 대 한 자세한 내용은 [Azure Cosmos 컨테이너를 쿼리](how-to-query-container.md) 하는 방법 문서를 참조 하세요.
