---
title: JSON 검색 구문 - Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API에서 사용할 수 있는 JSON 검색 구문에 대해 알아봅니다.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: fddd2291fe7fbb46c57d31e9aebc7fc6244df971
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336969"
---
# <a name="json-search-syntax"></a>JSON 검색 구문

```javascript
/* Query Object:
   Suppose we have a query path /v0/e0/v1/e1/...
   A query object contains constraints to be applied to graph nodes in a path.
   Constraints are given in <"node identifier", {constraint object}> key-value pairs: 
*/
{
    /* query path can also be set in the query object */
    path: "/v0/e0/v1/e1/.../vn/",
    v0: { /* A Constraint Object */ },
    v1: { /* A Constraint Object */ },
}
```

쿼리 경로에서 노드 이름(_v0, v1, ..._ )은 쿼리 개체에서 참조할 수 있는 노드 식별자 역할을 합니다. 경로에서 에지 이름(_e0, e1, ..._ )은 해당 에지의 형식을 나타냅니다. 별표 _*_ 를 노드 또는 에지 이름(시작 노드를 제외하고 반드시 지정되어야 함)으로 사용하여 이러한 요소에 제약 조건이 없음을 선언할 수 있습니다. 예를 들어 쿼리 경로 `/v0/*/v1/e1/*/`는 에지 _(v0, v1)_ 의 형식을 제한하지 않고 그래프에서 경로를 검색합니다. 한편, 쿼리는 경로의 대상(마지막 노드)에 대한 제약 조건도 포함하지 않습니다.

경로에 단 하나의 노드, 예를 들어 _v0_이 포함되어 있으면 쿼리는 해당 제약 조건을 만족하는 모든 엔터티를 반환합니다. 시작 노드에 적용된 제약 조건 개체는 *시작 쿼리 개체*라고 하며, 그 사양은 다음과 같이 지정됩니다.

```javascript
/* Starting Query Object:
   Specifies constraints on the starting node
*/
{
    /* "match" operator searches for entities with the specified properties. 
       All properties defined in the graph schema be queried such as "Name" and "NormalizedTitle".
     */
    "match": { 
        "Name" : "some name",
        ...
    },
    /* "id" operator directly specifies the IDs of the starting nodes. 
       When it is present, the "match" operator is ignored. 
     */
    "id": [ id1, id2, id3... ],
    /* "type" operator limits results to a certain type of entities,
       for example, "Author" or "Paper".
     */
    "type": "Author",
    /* "select" operator pulls properties from the database and 
       returns them to the client.
     */
    "select": ["PaperRank", ...]
}
```

경로에 시작 노드 그 이상이 포함되어 있으면 쿼리 프로세서는 지정된 경로 패턴을 따라 그래프 순회를 수행합니다. 노드에 도달하면 사용자 지정 통과 동작이 트리거됩니다. 즉, 현재 노드에서 중지하고 돌아가거나 그래프를 계속 탐색할 것인지 여부가 트리거됩니다. 통과 동작이 지정되지 않으면 기본 동작이 수행됩니다. 중간 노드의 경우, 기본 동작은 그래프를 계속 탐색하는 것입니다. 경로의 마지막 노드의 경우, 기본 동작은 중지하고 돌아가는 것입니다. 통과 동작을 지정하는 제약 조건 개체를 *통과 동작 개체*라고 합니다. 해당 사양은 다음과 같이 지정됩니다.

```javascript
/* Traversal Action Object:
   Specifies graph traversal actions on a node (except for the starting node).
 */
{
    /* Set the continue condition here. */
    continue: { 
        /* simple property exact match */
        "property_key" : "property_value", 
        /* Advanced query operators */
        /* -- Numerical comparisons */
        "property_key_2" : { "gt" /* or simply ">" */ : 123 },
        /* -- Substring query */
        "property_key_3" : { "substring" : "456" },
        /* -- CellID query */
        "id": [ 111, 222, 333... ],
        /* -- Entity type query */
        "type": "cell_type",
        /* -- Property existance query */
        "has" : "property_key_4",
        /* -- Logical operators */
        /* ---- Note that, by default the operators are combined with AND semantics */
        
        /* -- OR operator */
        "or": {
          /* Query operators... */
        },
        /* -- NOT operator */
        "not": {
            /* Query operators... */
        }
    },
    /* Set the return condition here. */
    return: {
        /* Same operators as the continue object */
    },
    /* The selected properties to return. */
    select: ["property_key_1", ...]
}
```

*json* 검색 쿼리의 POST 본문은 적어도 하나의 *경로* 패턴을 포함해야 합니다. 트래버스 동작 개체는 선택적입니다. 두 가지 예는 다음과 같습니다.

```JSON
{
  "path": "/series/ConferenceInstanceIDs/conference/FieldOfStudyIDs/field",
  "series": {
    "type": "ConferenceSeries",
    "FullName": "graph",
    "select": [ "FullName", "ShortName" ]
  },
  "conference": {
    "type": "ConferenceInstance",
    "select": [ "FullName" ]
  },
  "field": {
    "type": "FieldOfStudy",
    "select": [ "Name" ],
    "return": { "Name": { "substring" : "World Wide Web" } }
  }
}
```

```JSON
{
  "path": "/author/PaperIDs/paper",
  "author": {
    "type": "Author",
    "select": [ "DisplayAuthorName" ],
    "match": { "Name": "leslie lamport" }
  },
  "paper": {
    "type": "Paper",
    "select": [ "OriginalTitle", "CitationCount" ],
    "return": { "CitationCount": { "gt": 100 } }
  }
}
```

