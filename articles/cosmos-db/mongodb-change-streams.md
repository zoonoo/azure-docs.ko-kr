---
title: 몽고DB에 대한 Azure 코스모스 DB의 API에서 스트림 변경
description: MongoDB에 대한 변경 스트림 n Azure Cosmos DB의 API를 사용하여 데이터를 변경하는 방법을 알아봅니다.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467780"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>몽고DB에 대한 Azure 코스모스 DB의 API에서 스트림 변경

MongoDB에 대한 Azure Cosmos DB의 API에서 [변경 피드](change-feed.md) 지원을 변경 스트림 API를 사용하여 사용할 수 있습니다. 변경 스트림 API를 사용하여 응용 프로그램은 컬렉션 또는 단일 샤드의 항목에 대한 변경 내용을 가져올 수 있습니다. 나중에 결과에 따라 추가 작업을 수행할 수 있습니다. 컬렉션의 항목에 대한 변경 내용은 수정 시간 순서에 따라 캡처되며 샤드 키당 정렬 순서가 보장됩니다.

> [!NOTE]
> 변경 스트림을 사용하려면 MongoDB용 Azure Cosmos DBAPI의 버전 3.6 또는 이후 버전으로 계정을 만듭니다. 이전 버전에 대해 변경 스트림 예제를 실행하면 `Unrecognized pipeline stage name: $changeStream` 오류가 표시될 수 있습니다. 

다음 예제에서는 컬렉션의 모든 항목에 대한 변경 스트림을 얻는 방법을 보여 주습니다. 이 예제에서는 항목을 삽입, 업데이트 또는 교체할 때 볼 수 있는 커서를 만듭니다. 변경 스트림을 얻으려면 $match 단계, $project 단계 및 fullDocument 옵션이 필요합니다. 변경 스트림을 사용하여 삭제 작업을 감시하는 것은 현재 지원되지 않습니다. 해결 방법을 사용하면 삭제되는 항목에 소프트 마커를 추가할 수 있습니다. 예를 들어 "deleted"이라는 항목에 특성을 추가하고 "true"로 설정하고 항목에 TTL을 설정하여 자동으로 삭제하고 추적할 수 있습니다.

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

다음 예제에서는 단일 샤드의 항목을 변경하는 방법을 보여 주며 있습니다. 이 예제에서 샤드 키가 "a"와 같고 샤드 키 값이 "1"과 같은 항목의 변경 내용을 가져옵니다.

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

## <a name="current-limitations"></a>현재 제한 사항

변경 스트림을 사용할 때는 다음과 같은 제한 사항이 적용됩니다.

* `operationType` 및 `updateDescription` 속성은 출력 문서에서 아직 지원되지 않습니다.
* `insert`및 `update` `replace` 작업 유형은 현재 지원됩니다. 삭제 작업 또는 기타 이벤트는 아직 지원되지 않습니다.

이러한 제한으로 인해 이전 예제와 같이 $match 단계, $project 단계 및 fullDocument 옵션이 필요합니다.

## <a name="error-handling"></a>오류 처리

변경 스트림을 사용할 때 다음과 같은 오류 코드 및 메시지가 지원됩니다.

* **HTTP 오류 코드 429** - 변경 스트림이 제한되면 빈 페이지를 반환합니다.

* **NamespaceNotFound (OperationType 무효화)** - 존재하지 않는 컬렉션에서 변경 스트림을 실행하거나 컬렉션이 삭제된 경우 `NamespaceNotFound` 오류가 반환됩니다. 오류 `operationType` 대신 출력 문서에서 속성을 반환할 수 없으므로 `NamespaceNotFound` 오류가 반환됩니다. `operationType Invalidate`

## <a name="next-steps"></a>다음 단계

* [MongoDB용 Azure Cosmos DB의 API에서 데이터를 자동으로 만료하는 데 시간을 사용합니다.](mongodb-time-to-live.md)
* [Azure Cosmos DB의 API for MongoDB에서 인덱싱](mongodb-indexing.md)
