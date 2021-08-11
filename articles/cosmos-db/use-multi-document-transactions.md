---
title: Azure Cosmos DB API for MongoDB에서 다중 문서 트랜잭션 사용
description: Azure Cosmos DB API for MongoDB 4.0의 고정된 컬렉션에서 다중 문서 트랜잭션(모두 또는 없음 의미체계)을 실행할 수 있는 샘플 Mongo 셸 앱을 생성하는 방법에 대해 알아봅니다.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: gahllevy
ms.openlocfilehash: f319db76c8aee5a2a35ff8ca9670c42089350ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692473"
---
# <a name="use-multi-document-transactions-in-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB에서 다중 문서 트랜잭션 사용
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

이 문서에서는 서버 버전 4.0의 Azure Cosmos DB API for MongoDB의 고정된 컬렉션에서 다중 문서 트랜잭션을 실행하는 Mongo 셸 앱을 생성합니다.

## <a name="what-are-multi-document-transactions"></a>다중 문서 트랜잭션이란?

Azure Cosmos DB API for MongoDB에서 단일 문서에 대한 작업은 원자성입니다. 다중 문서 트랜잭션을 통해 응용 프로그램은 여러 문서에서 원자성 작업을 실행할 수 있습니다. 작업에 "모두 또는 없음" 의미 체계를 제공합니다. 커밋 시 트랜잭션 내에서 수행된 변경 내용이 유지되고, 트랜잭션이 실패하면 트랜잭션 내의 모든 변경 내용이 삭제됩니다.

다중 문서 트랜잭션은 **ACID** 의미 체계를 따릅니다.

* 원자성: 모든 작업이 하나로 처리 됨
* 일관성: 커밋된 데이터가 유효함
* 격리: 다른 작업과 격리 됨
* 내구성: 클라이언트가 지시한 경우 트랜잭션 데이터가 유지됨

## <a name="requirements"></a>요구 사항

다중 문서 트랜잭션은 API 버전 4.0의 분할되지 않은 컬렉션 내에서 지원됩니다. 다중 문서 트랜잭션은 컬렉션 전체 또는 4.0의 분할된 컬렉션에서 지원되지 않습니다. 트랜잭션의 제한 시간은 5초로 고정되어 있습니다.

유선 프로토콜 버전 4.0 이상을 지원하는 모든 드라이버는 Azure Cosmos DB API for MongoDB 다중 문서 트랜잭션을 지원합니다.

## <a name="run-multi-document-transactions-in-mongodb-shell"></a>MongoDB 셸에서 다중 문서 트랜잭션 실행
> [!Note]
> 이 예시는 MongoDB Compass에 포함된 MongoSH beta(shell)에서 작동하지 않습니다.

1. 명령 프롬프트를 열고 Mongo shell 버전 4.0 이상이 설치된 디렉터리로 이동합니다.

   ```powershell
   cd <path_to_mongo_shell_>
   ```

2. Mongo shell 스크립트 *connect_friends.js* 생성하고 다음 콘텐츠를 추가합니다

   ```javascript
   // insert data into friends collection
   db.getMongo().getDB("users").friends.insert({name:"Tom"})
   db.getMongo().getDB("users").friends.insert({name:"Mike"})
   // start transaction
   var session = db.getMongo().startSession();
   var friendsCollection = session.getDatabase("users").friends;
   session.startTransaction();
   // operations in transaction
   try {
       friendsCollection.updateOne({ name: "Tom" }, { $set: { friendOf: "Mike" } } );
       friendsCollection.updateOne({ name: "Mike" }, { $set: { friendOf: "Tom" } } );
   } catch (error) {
       // abort transaction on error
       session.abortTransaction();
       throw error;
   }

    // commit transaction
    session.commitTransaction();

    ```

3. 다음 명령을 실행하여 다중 문서 트랜잭션을 실행합니다. Azure Portal에서 호스트, 포트, 사용자, 키를 찾을 수 있습니다.

   ```powershell
   mongo "<HOST>:<PORT>" -u "<USER>" -p "KEY" --ssl connect_friends.js
   ```

## <a name="next-steps"></a>다음 단계

[Azure Cosmos DB API for MongoDB 4.0](mongodb-feature-support-40.md)의 새로운 기능 살펴보기
