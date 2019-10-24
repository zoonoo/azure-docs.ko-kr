---
title: Azure Cosmos DB의 API for MongoDB에서 인덱싱
description: Azure Cosmos DB의 API for MongoDB의 인덱싱 기능의 개요를 설명합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: e51e96c0c553bcf37284878cab11f3ec592ddd05
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753386"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB를 사용하는 인덱싱

Azure Cosmos DB의 API for MongoDB는 Cosmos DB의 자동 인덱스 관리 기능을 활용합니다. 결과적으로, 사용자는 Cosmos DB의 기본 인덱싱 정책에 액세스할 수 있습니다. 따라서 사용자에 의해 정의된 인덱스가 없거나 삭제된 인덱스가 없는 경우 모든 필드는 컬렉션에 삽입될 때 기본적으로 자동으로 인덱싱됩니다. 대부분의 시나리오의 경우 계정에서 설정된 기본 인덱싱 정책을 사용하는 것이 좋습니다.

## <a name="indexing-for-version-36"></a>버전 3.6에 대 한 인덱싱

유선 프로토콜 버전 3.6을 처리 하는 계정은 이전 버전에서 제공 하는 정책과는 다른 기본 인덱싱 정책을 제공 합니다. 기본적으로 _id 필드만 인덱싱됩니다. 추가 필드를 인덱싱 하려면 사용자가 MongoDB 인덱스 관리 명령을 적용 해야 합니다. 쿼리에 정렬을 적용 하려면 정렬 작업에 사용 되는 필드에 현재 인덱스를 만들어야 합니다.

### <a name="dropping-the-default-indexes-36"></a>기본 인덱스 삭제 (3.6)

유선 프로토콜 버전 3.6을 처리 하는 계정의 경우 유일한 기본 인덱스는 _id 이며 삭제할 수 없습니다.

### <a name="creating-a-compound-index-36"></a>복합 인덱스 만들기 (3.6)

진정한 복합 인덱스는 3.6 유선 프로토콜을 사용 하는 계정에 대해 지원 됩니다. 다음 명령에서는 ' a ' 및 ' b ' 필드에 복합 인덱스를 만듭니다. `db.coll.createIndex({a:1,b:1})`

복합 인덱스는 다음과 같이 한 번에 여러 필드를 효율적으로 정렬 하는 데 사용할 수 있습니다 `db.coll.find().sort({a:1,b:1})`

## <a name="indexing-for-version-32"></a>버전 3.2에 대 한 인덱싱

### <a name="dropping-the-default-indexes-32"></a>기본 인덱스 삭제 (3.2)

컬렉션 ```coll```에 대한 기본 인덱스를 삭제하는 데 다음 명령을 사용할 수 있습니다.

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>복합 인덱스 만들기 (3.2)

복합 인덱스는 문서의 여러 필드에 대한 참조를 유지합니다. 논리적으로 필드당 여러 개의 개별 인덱스 만들기와 동일합니다. Cosmos DB 인덱싱 기술에서 제공하는 최적화를 활용하려면 단일(비고유) 복합 인덱스 대신 여러 개의 개별 인덱스를 만드는 것이 좋습니다.

## <a name="common-indexing-operations"></a>일반적인 인덱싱 작업

다음 작업은 유선 프로토콜 버전 3.6을 처리 하는 계정과 이전 유선 프로토콜 버전을 제공 하는 계정 모두에 공통적으로 제공 됩니다. 

## <a name="creating-unique-indexes"></a>고유 인덱스 만들기

[고유 인덱스](unique-keys.md)는 두 개 이상의 문서에 인덱싱된 필드에 대해 동일한 값을 포함하지 않도록 하는 데 유용합니다.

>[!Important]
> 현재 컬렉션이 비어 있는 경우에만 고유 인덱스를 만들 수 있습니다(문서를 포함하지 않음).

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

분할된 컬렉션의 경우 MongoDB 동작에 따라 고유 인덱스를 만드는 데 분할된(파티션) 키를 제공해야 합니다. 즉, 분할된 컬렉션의 모든 고유 인덱스는 필드 중 하나가 파티션 키인 복합 인덱스입니다.

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

## <a name="ttl-indexes"></a>TTL 인덱스

특정 컬렉션에서 문서 만료를 활성화하려면 ["TTL index"(Time to Live 인덱스)](../cosmos-db/time-to-live.md)를 만들어야 합니다. TTL 인덱스는 "expireAfterSeconds" 값을 가진 _ts 필드의 인덱스입니다.

예제:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

위의 명령은 마지막 10초에서 수정되지 않은 ```db.coll``` 컬렉션의 문서를 삭제합니다.

> [!NOTE]
> **_ts**는 Cosmos DB 전용 필드이며 MongoDB 클라이언트에서 액세스할 수 없습니다. 문서의 마지막 수정의 타임스탬프를 포함하는 예약된(시스템) 속성입니다.

## <a name="migrating-collections-with-indexes"></a>인덱스를 사용하여 컬렉션 마이그레이션

현재 컬렉션에 문서가 포함되지 않은 경우에만 고유 인덱스를 만들 수 있습니다. 인기 있는 MongoDB 마이그레이션 도구는 데이터를 가져온 후 고유 인덱스를 만들도록 시도합니다. 이 문제를 피하려면 마이그레이션 도구를 허용 하는 대신 사용자가 해당 하는 컬렉션 및 고유 인덱스를 수동으로 만드는 것이 좋습니다. ```mongorestore```의 경우 명령줄에서 `--noIndexRestore` 플래그를 사용 하 여이 동작을 수행 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 인덱싱](../cosmos-db/index-policy.md)
* [TTL(Time To Live)을 사용하여 자동으로 Azure Cosmos DB의 데이터 만료](../cosmos-db/time-to-live.md)
