---
title: Azure Cosmos DB에서 절 선택
description: Azure Cosmos DB에 대 한 SQL SELECT 절에 대해 알아봅니다. Azure Cosmos DB JSON 쿼리 언어로 SQL을 사용 합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: f33cf20b76655a893fe7eebd9e6e6569d35de98f
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005947"
---
# <a name="select-clause-in-azure-cosmos-db"></a>Azure Cosmos DB에서 절 선택

모든 쿼리는 `SELECT` ANSI SQL 표준에 따라 절과 선택적 [from](sql-query-from.md) 및 [WHERE](sql-query-where.md) 절로 구성 됩니다. 일반적으로 `FROM` 절의 소스는 열거 되며 절은 `WHERE` 소스에 필터를 적용 하 여 JSON 항목의 하위 집합을 검색 합니다. 그런 `SELECT` 다음 절은 요청 된 JSON 값을 select 목록에 프로젝션 합니다.

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
  
  프로젝션 된 속성의 중복 항목을 제거 하도록 지정 합니다.  

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

`address` 다음 SELECT 쿼리 예제에서는 `Families` `id` 와 일치 하 `AndersenFamily`는을 반환 합니다.

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

## <a name="next-steps"></a>다음 단계

- [시작 하기](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE 절](sql-query-where.md)
