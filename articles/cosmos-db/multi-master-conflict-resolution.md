---
title: Azure Cosmos DB의 다중 마스터 충돌 해결
description: '이 문서는 Azure Cosmos DB 다중 마스터에서 충돌 범주 및 충돌 해결 모드(예: LWW(최종 작성자 인정), 사용자 지정 - 사용자 정의 프로시저, 사용자 지정 - 비동기)를 설명합니다.'
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 39fd393e78a2b66749c6aa34a758b185b38effdf
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041190"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Azure Cosmos DB의 다중 마스터 충돌 해결 

Azure Cosmos DB 다중 마스터는 데이터가 복제되는 모든 지역에서 일관성을 유지하도록 글로벌 충돌 해결 모드를 제공합니다.

## <a name="conflict-categories"></a>충돌 범주

Azure Cosmos DB 데이터로 작업할 때 발생할 수 있는 충돌에는 세 가지 범주가 있습니다.

* **삽입 충돌:** 이러한 유형의 충돌은 응용 프로그램이 두 개 이상의 지역에서 동시에 동일한 고유 인덱스(예: ID)를 사용하여 두 개 이상의 문서를 삽입하는 경우에 발생합니다. 이 경우 모든 쓰기는 처음에 성공할 수 있지만 선택한 충돌 해결 정책에 따라 원래 ID가 있는 하나의 문서만 커밋됩니다.

* **바꾸기 충돌:** 이 유형의 충돌은 응용 프로그램이 두 개 이상의 지역에서 동시에 단일 문서를 업데이트하는 경우에 발생합니다.

* **삭제 충돌:** 이 유형의 충돌은 응용 프로그램이 하나의 지역에서 문서를 삭제하고 하나 이상의 지역에서 업데이트하는 경우에 발생합니다. 

## <a name="conflict-resolution-modes"></a>충돌 해결 모드

Azure Cosmos DB에서 제공하는 세 가지 충돌 해결 모드가 있습니다. 충돌 해결 모드는 각 컬렉션에 대해 정의됩니다.

> [!NOTE]
> SQL API 사용자는 세 가지 서로 다른 충돌 해결 모드 중에서 선택할 수 있습니다. 모든 기타 API 모델의 경우(MongoDB, Cassandra, Graph, Table) 최종 작성자 인정(Last-Writer Wins)을 사용하여 충돌이 해결됩니다.

### <a name="last-writer-wins-lww"></a>LWW(최종 작성자 인정)

최종 작성자 인정(Last-Writer Wins)은 기본 충돌 해결 모드입니다. 이 모드에서 충돌은 문서의 속성에 전달된 숫자 값을 기반으로 해결됩니다.

다음 코드 조각은 .Net SDK를 사용하는 경우 최종 작성자 인정(Last-Writer Wins) 충돌 해결 정책을 구성하는 방법의 예제입니다. "ConflictResolutionPath"는 충돌을 해결하는 데 사용되는 속성에 대한 경로를 정의합니다. 이 예제에서 `/userDefinedId`는 충돌 해결 경로이며, 가장 큰 `userDefinedId` 값이 있는 문서가 충돌에서 항상 승리합니다. 최종 작성자 인정(Last-Writer-Wins) 해결 모드를 등록하려면 아래와 같이 ConflictResolutionPolicy를 사용하여 컬렉션을 프로비전합니다.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 최종 작성자 인정(Last-Writer-Wins) 해결 모드는 다음과 같은 다양한 데이터 충돌 범주에 적용됩니다.

* **삽입 및 업데이트 충돌:** 두 개 이상의 문서가 삽입 또는 바꾸기 작업에서 충돌하는 경우 충돌 해결 경로 대한 가장 큰 값을 포함하는 문서가 우승자가 됩니다(즉, userDefinedId). 여러 문서에 충돌 해결 경로에 대한 동일한 숫자 값이 있는 경우 선택된 우승자는 명확하지 않습니다. 그러나 모든 지역은 단일 우승자로 수렴됩니다.

* **삭제 충돌:** 삭제 충돌이 있는 경우 삭제는 충돌 해결 경로 값에 관계없이 다른 바꾸기 충돌에 항상 승리합니다.

### <a name="custom--user-defined-procedure"></a>사용자 지정 – 사용자 정의 프로시저

이 모드에서 사용자는 UDP(사용자 정의 프로시저)를 컬렉션에 등록하여 충돌 해결을 제어합니다. 이 UDP에는 특정 서명이 있습니다. 이 옵션을 선택해도 UDP 등록에 실패하는 경우 또는 UDP가 런타임 시 예외를 throw하는 경우 충돌은 개별적으로 해결될 수 있는 충돌 피드에 기록됩니다.

사용자 지정 - 사용자 정의 프로시저 충돌 해결 모드를 등록하려면 아래와 같이 ConflictResolutionPolicy를 사용하여 컬렉션을 프로비전합니다.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

다음으로 아래와 같이 컬렉션에 사용자 정의 프로시저를 추가합니다.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

컬렉션에 등록된 저장 프로시저에는 특별한 서명이 있습니다. 아래 예제에서 UDP는 `UserDefinedId` 속성을 사용하여 충돌을 해결합니다. 가장 큰 값이 있는 문서가 충돌에서 승리합니다.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

프로시저에는 4개의 매개 변수가 있습니다.

* **incomingDocument:** 문서의 충돌 버전을 지정합니다. 충돌은 삽입, 바꾸기 또는 삭제 충돌일 수 있습니다. 삭제 충돌의 경우 콘텐츠가 없는 삭제 이미지(삭제 표시)가 됩니다.

* **existingDocument:** incomingDocument로 동일한 "rid" 값이 있는 문서의 커밋된 버전을 지정합니다. 이 매개 변수는 삽입 및 삭제 충돌에 대해 Null로 설정됩니다.

* **isDeleteConflict:** 들어오는 문서가 이전에 삭제된 문서를 사용하여 충돌하는 경우를 나타내는 부울 플래그입니다. 이 매개 변수가 true로 설정된 경우 existingDocument도 Null이 됩니다.

* **conflictingDocuments:** ID 열 또는 다른 고유 인덱스 필드에서 incomingDocument와 충돌하는 데이터베이스에서 커밋된 모든 문서 버전의 컬렉션을 지정합니다. 이러한 문서는 incomingDocument와 비교할 때 다른 "rid" 값을 갖게 됩니다.

사용자 정의 프로시저에는 Cosmos DB 파티션 키에 대한 모든 권한이 있으며 충돌을 해결하기 위한 모든 저장소 작업을 수행할 수 있습니다. 사용자 정의 프로시저가 충돌 버전을 커밋하지 않는 경우 시스템은 충돌을 삭제하고 existingDocument는 커밋된 상태로 유지됩니다. 사용자 정의 프로시저 실패하거나 없는 경우 Azure Cosmos DB는 [비동기 충돌 해결 모드](#custom--asynchronous)에 표시된 것과 같이 비동기적으로 처리될 수 있는 읽기 전용 충돌 피드로 모든 충돌을 추가합니다. 

### <a name="custom--asynchronous"></a>사용자 지정 - 비동기  

이 모드에서 Azure Cosmos DB는 커밋되는 모든 충돌(삽입, 바꾸기 및 삭제)을 제외하고 사용자의 응용 프로그램에서 지연된 해결에 대한 읽기 전용 충돌 피드에 등록합니다. 응용 프로그램은 충돌 해결을 비동기적으로 수행하고 논리를 사용하거나 모든 외부 소스, 응용 프로그램 또는 서비스를 참조하여 충돌을 해결할 수 있습니다.

사용자 지정 - 비동기 충돌 해결 모드를 등록하려면 아래와 같이 ConflictResolutionPolicy를 사용하여 컬렉션을 프로비전합니다.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

충돌 피드에서 모든 충돌을 읽고 처리하려면 아래 표시된 코드를 구현합니다. 충돌 피드에 저장된 데이터는 일부 저장소 비용을 추가합니다. 따라서 처리된 후에 충돌 피드에 저장된 데이터를 삭제하는 것이 좋습니다.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> 충돌 피드는 Cosmos DB에서 변경 피드와 같은 다운스트림 처리에 대한 알림을 보내는 수신기를 포함하지 않습니다. 논리를 구현하여 충돌 피드를 폴링하고 충돌이 있는지 확인해야 합니다.

## <a name="code-samples"></a>코드 샘플

다음은 나열된 API에 대한 충돌 해결을 보여주는 샘플 응용 프로그램입니다. 각 샘플은 컨테이너 내에서 충돌을 생성한 다음, 각 지원되는 충돌 해결 모드에 대해 충돌이 해결되는 방법을 보여줍니다.

|API 모델  | SDK) |샘플 |
|---------|---------|---------|
|SQL  API    | .NET    |[azure-cosmos-db-sql-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|SQL  API    | 노드    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|SQL  API    | 자바    |[azure-cosmos-db-sql-java-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|Table  API  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|Gremlin API | .NET | [azure-cosmos-db-gremlin-dontnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Cosmos DB에 대한 충돌 범주 및 충돌 해결 모드에 대해 알아보았습니다. 다음으로 아래 리소스를 살펴봅니다.

* [다중 마스터를 사용하여 MongoDB 클라이언트 사용](multi-master-oss-nosql.md)
