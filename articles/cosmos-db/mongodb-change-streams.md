---
title: MongoDB에 대 한 Azure Cosmos DB의 API에서 스트림 변경
description: Azure Cosmos DB의 MongoDB API에서 변경 스트림을 사용 하 여 데이터에 대 한 변경 내용을 가져오는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 38e262abefe5444c1fe7586810f4b971cc7baf6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114159"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대 한 Azure Cosmos DB의 API에서 스트림 변경

변경 스트림 API를 사용 하 여 Azure Cosmos DB의 MongoDB API에서 [변경 피드](change-feed.md) 지원을 사용할 수 있습니다. 응용 프로그램은 변경 스트림 API를 사용 하 여 컬렉션 또는 단일 분할 된 항목의 항목에 대 한 변경 내용을 가져올 수 있습니다. 나중에 결과에 따라 추가 작업을 수행할 수 있습니다. 컬렉션의 항목에 대 한 변경 내용은 수정 시간 순서 대로 캡처되고 분할 키 당 정렬 순서가 보장 됩니다.

> [!NOTE]
> 변경 스트림을 사용 하려면 MongoDB 용 Azure Cosmos DB의 API 버전 3.6 이상 버전을 사용 하 여 계정을 만듭니다. 이전 버전에 대해 변경 스트림 예를 실행 하는 경우 `Unrecognized pipeline stage name: $changeStream` 오류가 표시 될 수 있습니다.

## <a name="current-limitations"></a>현재 제한 사항

변경 스트림을 사용할 때는 다음과 같은 제한 사항이 적용 됩니다.

* `operationType` 및 `updateDescription` 속성은 출력 문서에서 아직 지원 되지 않습니다.
* `insert`, `update`및 `replace` 작업 유형은 현재 지원 됩니다. 
* 삭제 작업 또는 기타 이벤트는 아직 지원 되지 않습니다.

이러한 제한 사항으로 인해 이전 예제에 나와 있는 것 처럼 $match 단계, $project 단계 및 fullDocument 옵션이 필요 합니다.

Azure Cosmos DB의 SQL API에 있는 변경 피드와 달리 변경 스트림을 사용 하는 별도의 [변경 피드 프로세서 라이브러리](change-feed-processor.md) 또는 임대 컨테이너가 필요 하지 않습니다. 현재 [Azure Functions 트리거가](change-feed-functions.md) 변경 스트림을 처리 하는 것을 지원 하지 않습니다.

## <a name="error-handling"></a>오류 처리

변경 스트림을 사용할 때 지원 되는 오류 코드와 메시지는 다음과 같습니다.

* **HTTP 오류 코드 16500** -변경 스트림이 제한 되 면 빈 페이지를 반환 합니다.

* **NamespaceNotFound (OperationType 무효화)** -존재 하지 않는 컬렉션에서 변경 스트림을 실행 하거나 컬렉션을 삭제 하면 `NamespaceNotFound` 오류가 반환 됩니다. 속성은 `operationType` `operationType Invalidate` 오류 대신 출력 문서에서 반환 될 수 없으므로 `NamespaceNotFound` 오류가 반환 됩니다.

## <a name="examples"></a>예

다음 예제에서는 컬렉션의 모든 항목에 대 한 변경 스트림을 가져오는 방법을 보여 줍니다. 이 예제에서는 삽입, 업데이트 또는 교체 될 때 항목을 감시 하는 커서를 만듭니다. 변경 `$match` 스트림을 가져오려면 `$project` 단계, 단계 `fullDocument` 및 옵션이 필요 합니다. 변경 스트림을 사용 하 여 삭제 작업을 감시 하는 작업은 현재 지원 되지 않습니다. 이 문제를 해결 하려면 삭제할 항목에 소프트 마커를 추가 하면 됩니다. 예를 들어 "deleted" 라는 항목에 특성을 추가할 수 있습니다. 항목을 삭제 하려는 경우 "deleted"를로 `true` 설정 하 고 항목에 대 한 TTL을 설정할 수 있습니다. "Deleted"를로 업데이트 `true` 하는 것이 업데이트 이므로 변경 스트림에이 변경 내용이 표시 됩니다.

### <a name="javascript"></a>JavaScript:

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#:

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>단일 분할 된 데이터베이스가 포함 된 변경 내용

다음 예에서는 단일 분할 된 항목 내의 항목에 대 한 변경 내용을 가져오는 방법을 보여 줍니다. 이 예제에서는 분할 키가 "a"이 고 분할 키 값이 "1"과 같은 항목의 변경 내용을 가져옵니다. 다른 클라이언트에서 다른 분할의 변경 내용을 병렬로 읽을 수 있습니다.

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>다음 단계

* [Time to live를 사용 하 여 MongoDB에 대 한 Azure Cosmos DB의 API에서 자동으로 데이터 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 API for MongoDB에서 인덱싱](mongodb-indexing.md)
