---
title: 'Azure Cosmos DB: MongoDB API를 사용하여 쿼리하는 방법'
description: Azure Cosmos DB의 MongoDB API를 사용하여 쿼리하는 방법을 알아봅니다.
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/29/2018
ms.openlocfilehash: 8c865ab7fa997b169e481612ce791d960688da56
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841509"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>자습서: MongoDB API를 사용하여 Azure Cosmos DB 쿼리

Azure Cosmos DB [MongoDB API](mongodb-introduction.md)는 [MongoDB 셸 쿼리](https://docs.mongodb.com/manual/tutorial/query-documents/)를 지원합니다. 

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * MongoDB를 사용한 데이터 쿼리

이 문서의 예제를 사용하여 시작하고 [MongoDB 셸을 사용하여 Azure Cosmos DB 쿼리](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) 비디오를 시청할 수 있습니다.

## <a name="sample-document"></a>샘플 문서

이 문서의 쿼리에서는 다음 샘플 문서를 사용합니다.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a>예제 쿼리 1 

위의 샘플 가족 문서를 고려해 볼 때 다음 쿼리에서는 ID 필드가 `WakefieldFamily`와 일치하는 문서를 반환합니다.

**쿼리**
    
    db.families.find({ id: "WakefieldFamily"})

**결과**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>예제 쿼리 2 

다음 쿼리에서는 가족의 모든 자식을 반환합니다. 

**쿼리**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**결과**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>예제 쿼리 3 

다음 쿼리는 등록된 모든 가족을 반환합니다. 

**쿼리**
    
    db.families.find( { "isRegistered" : true })
**결과** 문서가 반환되지 않습니다. 

## <a id="examplequery4"></a>예제 쿼리 4

다음 쿼리는 등록되지 않은 모든 가족을 반환합니다. 

**쿼리**
    
    db.families.find( { "isRegistered" : false })
**결과**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>예제 쿼리 5

다음 쿼리는 등록되지 않았고 거주 지역(주)이 NY인 가족을 모두 반환합니다. 

**쿼리**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**결과**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>예제 쿼리 6

다음 쿼리에서는 자식(children)의 학년(grade)이 8인 모든 가족을 반환합니다.

**쿼리**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**결과**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>예제 쿼리 7

다음 쿼리에서는 children 배열의 크기가 3인 모든 가족을 반환합니다.

**쿼리**
  
      db.Family.find( {children: { $size:3} } )

**결과**

자식이 셋 이상인 가족이 없으므로 결과가 반환되지 않습니다. 매개 변수가 2일 때만 이 쿼리가 성공하여 전체 문서를 반환합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * MongoDB를 사용하여 쿼리하는 방법 

이제 전 세계로 데이터를 배포하는 방법을 알아보려면 다음 자습서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [전 세계로 데이터 배포](tutorial-global-distribution-sql-api.md)

