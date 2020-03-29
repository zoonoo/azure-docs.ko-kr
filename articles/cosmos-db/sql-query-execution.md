---
title: Azure 코스모스 DB의 SQL 쿼리 실행
description: Azure Cosmos DB에서 SQL 쿼리를 만들고 실행하는 방법에 대해 알아봅니다. 이 문서에서는 REST API, .Net SDK, JavaScript SDK 및 기타 다양한 SDK를 사용하여 SQL 쿼리를 만들고 실행하는 방법을 설명합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871264"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure 코스모스 DB SQL 쿼리 실행

HTTP/HTTPS 요청을 할 수 있는 모든 언어는 코스모스 DB REST API를 호출할 수 있습니다. 코스모스 DB는 .NET, Node.js, 자바스크립트 및 파이썬 프로그래밍 언어에 대한 프로그래밍 라이브러리도 제공합니다. REST API와 라이브러리는 모두 SQL을 통한 쿼리를 지원하며 .NET SDK는 [LINQ 쿼리도](sql-query-linq-to-sql.md)지원합니다.

다음 예제는 쿼리를 만들고 Cosmos 데이터베이스 계정에 대해 쿼리를 제출하는 방법을 보여 주습니다.

## <a name="rest-api"></a><a id="REST-API"></a>나머지 API

Cosmos DB는 HTTP를 통해 개방형 RESTful 프로그래밍 모델을 제공합니다. 리소스 모델은 Azure 구독이 프로비저닝하는 데이터베이스 계정 아래의 리소스 집합으로 구성됩니다. 데이터베이스 계정은 데이터베이스 집합으로 구성되며, 각 *데이터베이스에는*여러 *컨테이너가*포함될 수 있으며, 여기에는 *항목,* UdF 및 기타 리소스 형식이 포함됩니다. 각 Cosmos DB 리소스는 논리적이고 안정적인 URI를 사용하여 주소를 지정할 수 있습니다. 리소스 집합을 *피드라고*합니다. 

이러한 리소스와의 기본 상호 작용 모델은 `GET`HTTP `PUT` `POST`동사를 `DELETE`통해서입니다 . 새 `POST` 리소스를 만들거나, 저장 프로시저를 실행하거나, Cosmos DB 쿼리를 실행하는 데 사용합니다. 쿼리는 항상 파생 작업이 없는 읽기 전용 작업입니다.

다음 예제에서는 샘플 `POST` 항목에 대한 SQL API 쿼리에 대한 a를 보여 준다. 쿼리에는 JSON `name` 속성에 대한 간단한 필터가 있습니다. `x-ms-documentdb-isquery` 및 콘텐츠 유형: `application/query+json` 헤더는 작업이 쿼리임을 나타냅니다. Cosmos DB 계정의 URI로 바꿉습니다. `mysqlapicosmosdb.documents.azure.com:443`

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

결과는 다음과 같습니다.

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

다음으로 더 복잡한 쿼리는 조인의 여러 결과를 반환합니다.

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

결과는 다음과 같습니다. 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

쿼리 결과가 단일 페이지에 맞지 않으면 REST API는 응답 헤더를 통해 연속 토큰을 `x-ms-continuation-token` 반환합니다. 클라이언트는 후속 결과에 헤더를 포함하여 결과를 페이지내화할 수 있습니다. 숫자 헤더를 통해 페이지당 결과 `x-ms-max-item-count` 수를 제어할 수도 있습니다.

쿼리에 COUNT와 같은 집계 함수가 있는 경우 쿼리 페이지는 결과의 한 페이지에 대해서만 부분적으로 집계된 값을 반환할 수 있습니다. 최종 결과를 생성하려면 클라이언트가 이러한 결과에 대해 두 번째 수준 집계를 수행해야 합니다. 예를 들어 개별 페이지에서 반환된 개수를 합산하여 총 수를 반환합니다.

쿼리에 대한 데이터 일관성 정책을 관리하려면 모든 REST API 요청에서와 같이 헤더를 `x-ms-consistency-level` 사용합니다. 또한 세션 일관성을 위해 `x-ms-session-token` 쿼리 요청에서 최신 쿠키 헤더를 에코해야 합니다. 쿼리된 컨테이너의 인덱싱 정책이 쿼리 결과의 일관성에 영향을 줄 수도 있습니다. 컨테이너에 대한 기본 인덱싱 정책 설정을 사용하면 인덱스가 항상 항목 내용과 최신이고 쿼리 결과는 데이터에 대해 선택한 일관성과 일치합니다. 자세한 내용은 [Azure Cosmos DB 일관성 수준][일관성 수준]을 참조하십시오.

컨테이너에서 구성된 인덱싱 정책이 지정된 쿼리를 지원할 수 없는 경우 Azure Cosmos DB 서버는 400개의 "잘못된 요청"을 반환합니다. 이 오류 메시지는 인덱싱에서 명시적으로 제외된 경로가 있는 쿼리에 대해 반환됩니다. 인덱스를 `x-ms-documentdb-query-enable-scan` 사용할 수 없는 경우 쿼리가 검사를 수행할 수 있도록 헤더를 지정할 수 있습니다.

`x-ms-documentdb-populatequerymetrics` 헤더를 로 설정하여 쿼리 실행에 대한 `true`자세한 메트릭을 얻을 수 있습니다. 자세한 내용은 [SQL query metrics for Azure Cosmos DB](sql-api-query-metrics.md)(Azure Cosmos DB에 대한 SQL 쿼리 메트릭)를 참조하세요.

## <a name="c-net-sdk"></a>C# (.NET SDK)

.NET SDK는 LINQ 및 SQL 쿼리를 둘 다 지원합니다. 다음 예제에서는 .NET을 사용하여 이전 필터 쿼리를 수행하는 방법을 보여 주며 있습니다.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

다음 예제에서는 각 항목 내에서 같음에 대 한 두 속성을 비교 하 고 익명 프로젝션을 사용 합니다.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

다음 예제에서는 LINQ를 `SelectMany`통해 표현된 조인을 보여 주며.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 클라이언트는 앞의 예제와 같이 모든 쿼리 `foreach` 페이지가 블록으로 생성되는 것을 자동으로 거치게 됩니다. [REST API](#REST-API) 섹션에 도입된 쿼리 옵션은 `FeedOptions` `FeedResponse` `CreateDocumentQuery` 메서드의 클래스와 를 사용하여 .NET SDK에서도 사용할 수 있습니다. 설정을 사용하여 페이지 수를 제어할 `MaxItemCount` 수 있습니다.

`IDocumentQueryable` 개체를 사용하여 `IQueryable` 페이징을 만든 다음 값을 읽고 `ResponseContinuationToken` 에서와 `RequestContinuationToken` `FeedOptions`같이 다시 전달하여 페이징을 명시적으로 제어할 수도 있습니다. 구성된 인덱싱 정책에서 쿼리가 지원되지 않는 경우 검사를 사용하도록 설정할 `EnableScanInQuery` 수 있습니다. 분할된 컨테이너의 경우 `PartitionKey` Azure Cosmos DB가 쿼리 텍스트에서 자동으로 추출할 수 있지만 단일 파티션에 대해 쿼리를 실행하는 데 사용할 수 있습니다. 여러 파티션에 대해 쿼리를 실행하는 데 사용할 `EnableCrossPartitionQuery` 수 있습니다.

쿼리가 있는 .NET 샘플에 대한 자세한 내용은 GitHub의 [Azure Cosmos DB .NET 샘플을](https://github.com/Azure/azure-cosmos-dotnet-v3) 참조하십시오.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>JavaScript 서버 쪽 API

Azure Cosmos DB는 저장 프로시저 및 트리거를 사용하여 컨테이너에서 직접 [JavaScript 기반 응용 프로그램](stored-procedures-triggers-udfs.md) 논리를 실행하기 위한 프로그래밍 모델을 제공합니다. 그런 다음 컨테이너 수준에 등록된 JavaScript 논리는 주변 ACID 트랜잭션에 둘러싸인 지정된 컨테이너의 항목에 대한 데이터베이스 작업을 발행할 수 있습니다.

다음 예제에서는 JavaScript `queryDocuments` 서버 API에서 내부 저장 프로시저 및 트리거에서 쿼리를 만드는 방법을 보여 주며 있습니다.

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Azure 코스모스 DB 일관성 수준](consistency-levels.md)
