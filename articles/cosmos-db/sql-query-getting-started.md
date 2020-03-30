---
title: Azure 코스모스 DB에서 SQL 쿼리 시작
description: SQL 쿼리를 사용하여 Azure Cosmos DB에서 데이터를 쿼리하는 방법을 알아봅니다. Azure Cosmos DB의 컨테이너에 샘플 데이터를 업로드하고 쿼리할 수 있습니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873338"
---
# <a name="getting-started-with-sql-queries"></a>SQL 쿼리 시작

Azure Cosmos DB SQL API 계정은 구조화 된 쿼리 언어 (SQL)를 JSON 쿼리 언어로 사용하여 쿼리 항목을 지원합니다. Azure Cosmos DB 쿼리 언어의 디자인 목표는 다음과 같습니다.

* 새 쿼리 언어를 발명하는 대신 가장 친숙하고 인기 있는 쿼리 언어 중 하나인 SQL을 지원합니다. SQL은 JSON 항목에 대한 풍부한 쿼리에 대한 형식적인 프로그래밍 모델을 제공합니다.  

* 쿼리 언어의 기초로 JavaScript의 프로그래밍 모델을 사용합니다. JavaScript의 형식 시스템, 식 평가 및 함수 호출은 SQL API의 루트입니다. 이러한 루트는 관계형 프로젝션, JSON 항목의 계층 적 탐색, 자체 조인, 공간 쿼리 및 JavaScript로 완전히 작성된 사용자 정의 함수(UdF) 호출과 같은 기능에 대한 자연스러운 프로그래밍 모델을 제공합니다.

## <a name="upload-sample-data"></a>샘플 데이터 업로드

SQL API Cosmos DB 계정에서 . `Families` 컨테이너에 두 개의 간단한 JSON 항목을 만듭니다. 이 데이터 집합을 사용하여 Azure Cosmos DB 쿼리 문서에서 대부분의 샘플 쿼리를 실행할 수 있습니다.

### <a name="create-json-items"></a>JSON 항목 만들기

다음 코드는 패밀리에 대한 두 가지 간단한 JSON 항목을 만듭니다. 앤더슨 과 웨이크 필드 가족을위한 간단한 JSON 항목은 부모, 어린이와 애완 동물, 주소 및 등록 정보를 포함한다. 첫 번째 항목에는 문자열, 숫자, 부울, 배열 및 중첩 된 속성이 있습니다.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

두 번째 `givenName` 항목은 `familyName` `firstName` 및 `lastName`를 대신 사용합니다.

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
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>JSON 항목 쿼리

JSON 데이터에 대한 몇 가지 쿼리를 시도하여 Azure Cosmos DB의 SQL 쿼리 언어의 몇 가지 주요 측면을 이해합니다.

다음 쿼리는 `id` 필드가 일치하는 `AndersenFamily`항목을 반환합니다. 쿼리이기 `SELECT *` 때문에 쿼리의 출력은 전체 JSON 항목입니다. SELECT 구문에 대한 자세한 내용은 [SELECT 문을](sql-query-select.md)참조하십시오. 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

쿼리 결과는 다음과 같습니다. 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

다음 쿼리는 JSON 출력을 다른 모양으로 다시 포진합니다. 쿼리는 두 개의 `Family` 선택된 `Name` 필드와 와 `City`주소 도시가 상태와 동일한 경우 새 JSON 개체를 투영합니다. "NY, NY"는 이 경우와 일치합니다.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

쿼리 결과는 다음과 같습니다.

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

다음 쿼리는 도시별로 정렬된 `id` 일치하는 `WakefieldFamily`가족의 지정된 모든 자식 이름을 반환합니다.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

결과는 다음과 같습니다.

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>설명

앞의 예제는 Cosmos DB 쿼리 언어의 여러 측면을 보여 주며 다음과 같은 측면을 보여 주며 다음과 같은 측면을 보여 주며 다음과 같은 측면을 보여 준다.  

* SQL API는 JSON 값에서 작동하므로 행과 열 대신 트리 모양의 엔터티를 처리합니다. ANSI SQL의 두 부분으로 구성된 참조와 유사하게 `Node1.Node2.Node3…..Nodem`임의의 `<table>.<column>` 깊이에서 트리 노드를 참조할 수 있습니다.

* 쿼리 언어는 스키마가 없는 데이터와 함께 작동하므로 형식 시스템을 동적으로 바인딩해야 합니다. 항목에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리의 결과는 유효한 JSON 값이지만 고정 된 스키마가 보장되지는 않습니다.  

* Azure Cosmos DB는 엄격한 JSON 항목만 지원합니다. 형식 시스템 및 식은 JSON 형식만 처리하도록 제한됩니다. 자세한 내용은 [JSON 사양을](https://www.json.org/)참조하십시오.  

* 코스모스 컨테이너는 JSON 항목의 스키마가 없는 컬렉션입니다. 컨테이너 항목 내 및 전체 관계는 기본 키 및 외부 키 관계가 아니라 포함에 의해 암시적으로 캡처됩니다. 이 기능은 이 문서의 후반부에서 설명하는 항목 내 조인에 중요합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [select 절](sql-query-select.md)
