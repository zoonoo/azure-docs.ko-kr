---
title: Azure 코스모스 DB의 SELECT 절
description: Azure 코스모스 DB에 대한 SQL SELECT 절에 대해 알아봅니다. SQL을 Azure 코스모스 DB JSON 쿼리 언어로 사용합니다.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 013ebdcdbac41825c10a1362f73ab4c94052400d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469938"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure 코스모스 DB의 SELECT 절

모든 쿼리는 ANSI SQL 표준에 따라 SELECT 절과 선택적 [FROM](sql-query-from.md) 및 [WHERE](sql-query-where.md) 절로 구성됩니다. 일반적으로 FROM 절의 소스가 확장되고 WHERE 절은 JSON 항목의 하위 집합을 검색하기 위해 원본에 필터를 적용합니다. 그런 다음 SELECT 절은 선택 목록에 요청된 JSON 값을 투영합니다.

## <a name="syntax"></a>구문

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>인수
  
- `<select_specification>`  

  결과 집합에 대해 선택되는 속성 또는 값입니다.  
  
- `'*'`  

  변경하지 않고 값을 검색하도록 지정합니다. 특히 처리된 값이 개체이면 모든 속성이 검색됩니다.  
  
- `<object_property_list>`  
  
  검색할 속성의 목록을 지정합니다. 반환된 각 값은 지정된 속성이 있는 개체입니다.  
  
- `VALUE`  

  완전한 JSON 개체 대신 JSON 값을 검색하도록 지정합니다. 이것은 `<property_list>`와 달리 개체에 프로젝션된 값을 래핑하지 않습니다.  
 
- `DISTINCT`
  
  투영된 속성의 중복을 제거해야 한다고 지정합니다.  

- `<scalar_expression>`  

  계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  

## <a name="remarks"></a>설명

`SELECT *` 구문은 FROM 절에서 정확히 하나의 별칭을 선언한 경우에만 유효합니다. `SELECT *`는 ID 프로젝션을 제공하며, 이는 프로젝션이 필요하지 않은 경우 유용할 수 있습니다. SELECT *는 FROM 절이 지정되고 단일 입력 원본만 도입된 경우에만 유효합니다.  
  
`SELECT <select_list>`와 `SELECT *`는 "syntactic sugar(구문적 설탕)"이며 다음과 같이 간단한 SELECT 문을 사용하여 표현할 수 있습니다.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   이는 다음과 동등합니다.  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   이는 다음과 동등합니다.  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>예

다음 SELECT 쿼리 `address` 예제는 `AndersenFamily`일치 하는 항목에서 `Families` `id` 반환 합니다.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="quoted-property-accessor"></a>따옴표 붙은 속성 접근자
인용된 속성 연산자 []를 사용하여 속성에 액세스할 수 있습니다. 예를 들어 `SELECT c.grade` and `SELECT c["grade"]` 와 동일합니다. 이 구문은 공백, 특수 문자를 포함하거나 SQL 키워드 또는 예약된 단어와 같은 이름을 가진 속성을 이스케이프하는 데 유용합니다.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>중첩 속성

다음 예제에서는 두 개의 `f.address.state` 중첩된 속성 및 `f.address.city`을 프로세서합니다.

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
### <a name="json-expressions"></a>JSON 표현식

프로젝션은 다음 예제와 같이 JSON 표현식도 지원합니다.

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

앞의 예제에서 SELECT 절은 JSON 개체를 만들어야 하며 샘플에서 키를 제공하지 않으므로 절은 `$1`암시적 인수 변수 이름을 사용합니다. 다음 쿼리는 두 개의 암시적 인수 `$1` 변수와 `$2`를 반환합니다.

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
## <a name="reserved-keywords-and-special-characters"></a>예약된 키워드 및 특수 문자

데이터에 "주문" 또는 "Group"과 같은 예약된 키워드와 이름이 같은 속성이 포함된 경우 이러한 문서에 대한 쿼리로 인해 구문 오류가 발생합니다. 쿼리를 성공적으로 실행하려면 `[]` 속성을 문자에 명시적으로 포함해야 합니다.

예를 들어 다음은 명명된 `order` 속성과 특수 문자가 포함된 속성이 있는 문서입니다. `price($)`

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

`order` 속성 또는 `price($)` 속성을 포함 하는 쿼리를 실행 하는 경우 구문 오류가 발생 합니다.

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

아래와 동일한 쿼리를 다시 작성해야 합니다.

```sql
SELECT * FROM c WHERE c["order"].orderId = "12345"
```

```sql
SELECT * FROM c WHERE c["order"]["price($)"] > 50
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 절](sql-query-where.md)
