---
title: Azure Cosmos DB에서 JSON 사용
description: 중첩 된 JSON 속성을 쿼리 및 액세스 하 고 Azure Cosmos DB에서 특수 문자를 사용 하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: d0b11cdb0cf2719b576b7a4c4f3fa534ae09dfa8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117022"
---
# <a name="working-with-json-in-azure-cosmos-db"></a>Azure Cosmos DB에서 JSON 사용

Azure Cosmos DB의 SQL (Core) API에서 항목은 JSON으로 저장 됩니다. 형식 시스템 및 식은 JSON 형식만 처리 하도록 제한 됩니다. 자세한 내용은 [JSON 사양](https://www.json.org/)을 참조 하세요.

JSON 작업의 몇 가지 중요 한 측면을 요약 하겠습니다.

- JSON 개체는 항상 `{` 왼쪽 중괄호로 시작 하 고 `}` 오른쪽 중괄호로 끝납니다.
- JSON 속성은 서로 [중첩](#nested-properties) 될 수 있습니다.
- JSON 속성 값은 배열이 될 수 있습니다.
- JSON 속성 이름은 대/소문자를 구분 합니다.
- JSON 속성 이름은 임의의 문자열 값 (공백이 나 문자가 아닌 문자 포함)이 될 수 있습니다.

## <a name="nested-properties"></a>중첩 속성

점 접근자를 사용 하 여 중첩 된 JSON에 액세스할 수 있습니다. 다른 속성을 사용할 수 있는 것과 동일한 방법으로 쿼리에서 중첩 된 JSON 속성을 사용할 수 있습니다.

다음은 중첩 된 JSON을 사용 하는 문서입니다.

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "address": {
      "state": "WA",
      "county": "King",
      "city": "Seattle"
      },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

이 경우 `state` , `country` 및 `city` 속성은 모두 속성 내에 중첩 됩니다 `address` .

다음 예제에서는 및 라는 두 개의 중첩 된 속성을 프로젝션 합니다. `f.address.state` `f.address.city`

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="working-with-arrays"></a>배열 작업

JSON은 중첩 된 속성 외에도 배열을 지원 합니다.

배열이 포함 된 예제 문서는 다음과 같습니다.

```json
{
  "id": "WakefieldFamily",
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
      },
      {
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8
      }
  ],
}
```

배열을 사용 하 여 작업 하는 경우 해당 위치를 참조 하 여 배열 내의 특정 요소에 액세스할 수 있습니다.

```sql
SELECT *
FROM Families f
WHERE f.children[0].givenName = "Jesse"
```

그러나 대부분의 경우에는 배열을 사용 하 여 작업할 때 [하위 쿼리](sql-query-subquery.md) 또는 [자체 조인을](sql-query-join.md) 사용 합니다.

예를 들어 고객의 은행 계좌에 대 한 일일 잔액을 보여 주는 문서는 다음과 같습니다.

```json
{
  "id": "Contoso-Checking-Account-2020",
  "balance": [
      {
        "checkingAccount": 1000,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": 100,
        "savingsAccount": 5000
      },
      {
        "checkingAccount": -10,
        "savingsAccount": 5000,
      },
      {
        "checkingAccount": 5000,
        "savingsAccount": 5000,
      }
         ...
  ]
}
```

어느 시점에 음수 잔액을 가진 모든 고객을 보여 주는 쿼리를 실행 하려는 경우에는 하위 쿼리에 [exists](sql-query-subquery.md#exists-expression) 를 사용할 수 있습니다.

```sql
SELECT c.id
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.balance
    WHERE n.checkingAccount < 0
)
```

## <a name="reserved-keywords-and-special-characters-in-json"></a>JSON의 예약 키워드 및 특수 문자

따옴표 붙은 속성 연산자를 사용 하 여 속성에 액세스할 수 있습니다 `[]` . 예를 들어 `SELECT c.grade` and `SELECT c["grade"]` 와 동일합니다. 이 구문은 공백이 나 특수 문자가 포함 된 속성을 이스케이프 하거나 SQL 키워드 또는 예약어와 동일한 이름을 가진 속성을 이스케이프 하는 데 유용 합니다.

예를 `order` 들어 속성 및 특수 문자를 포함 하는 속성을 포함 하는 문서는 `price($)` 다음과 같습니다.

```json
{
  "id": "AndersenFamily",
  "order": [
     {
         "orderId": "12345",
         "productId": "A17849",
         "price($)": 59.33
     }
  ],
  "creationDate": 1431620472,
  "isRegistered": true
}
```

속성이 나 속성을 포함 하는 쿼리를 실행 하는 경우 `order` `price($)` 구문 오류가 표시 됩니다.

```sql
SELECT * FROM c where c.order.orderid = "12345"
```

```sql
SELECT * FROM c where c.order.price($) > 50
```

결과는 다음과 같습니다.

`
Syntax error, incorrect syntax near 'order'
`

다음과 같이 동일한 쿼리를 다시 작성 해야 합니다.

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="json-expressions"></a>JSON 식

또한 다음 예제와 같이 프로젝션은 JSON 식도 지원 합니다.

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

앞의 예제에서 절은 `SELECT` JSON 개체를 만들어야 하며, 샘플에서 키를 제공 하지 않으므로 절은 암시적 인수 변수 이름을 사용 합니다 `$1` . 다음 쿼리는 두 개의 암시적 인수 변수인 및를 반환 합니다. `$1` `$2`

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      },
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="aliasing"></a>별칭 지정

쿼리에서 값의 별칭을 명시적으로 지정할 수 있습니다. 쿼리에 이름이 같은 두 개의 속성이 있는 경우 별칭을 사용 하 여 두 속성 중 하나 또는 둘 다의 이름을 바꿔 프로젝션 된 결과에서 명확 하 게 구분 합니다.

### <a name="examples"></a>예

`AS`두 번째 값을로 프로젝션 하는 경우에는 다음 예제와 같이 별칭 지정에 사용 되는 키워드가 선택 사항입니다 `NameInfo` .

```sql
    SELECT
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

### <a name="aliasing-with-reserved-keywords-or-special-characters"></a>예약 된 키워드 또는 특수 문자로 별칭을 사용 합니다.

별칭을 사용 하 여 값을 공백, 특수 문자 또는 예약어를 포함 하는 속성 이름으로 프로젝션 할 수 없습니다. 값의 프로젝션을로 변경 하려는 경우 (예: 공백이 있는 속성 이름 포함) [JSON 식을](#json-expressions)사용할 수 있습니다.

예를 들면 다음과 같습니다.

```sql
    SELECT
           {"JSON expression with a space": { "state": f.address.state, "city": f.address.city }},
           {"JSON expression with a special character!": { "name": f.id }}
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [WHERE 절](sql-query-where.md)
