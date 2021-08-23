---
title: Azure Cosmos DB의 UDF(사용자 정의 함수)
description: Azure Cosmos DB의 사용자 정의 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 20b35bc459b9bc1ae11dcfbb2a7bf3744d62acc2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530178"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB의 UDF(사용자 정의 함수)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

SQL API는 Udf(사용자 정의 함수)를 지원 합니다. 스칼라 UDF를 사용하면 0개 이상의 인수를 전달하고 단일 인수 결과를 반환할 수 있습니다. API는 각 인수를 올바른 JSON 값으로 확인합니다.  

## <a name="udf-use-cases"></a>UDF 사용 사례

이 API는 UDF를 사용하여 사용자 지정 애플리케이션 논리를 지원하도록 SQL 구문을 확장합니다. SQL API에 UDF를 등록 하고 SQL 쿼리에서 참조할 수 있습니다. 저장 프로시저 및 트리거와 달리 UDF는 읽기 전용입니다.

UDF를 사용하여 Azure Cosmos DB의 쿼리 언어를 확장할 수 있습니다. UDF는 쿼리의 프로젝션에서 복잡한 비즈니스 논리를 표현할 수 있는 좋은 방법입니다.

그러나 다음과 같은 경우에는 UDF 방지를 권장합니다.

- 동일한 [시스템 함수](sql-query-system-functions.md)가 Azure Cosmos DB에 이미 있습니다. 시스템 함수는 항상 동일한 UDF 보다 더 작은 RU를 사용합니다.
- UDF는 쿼리의 `WHERE` 절에 있는 유일한 필터입니다. UDF는 인덱스를 활용하지 않으므로 UDF를 평가하려면 문서를 로드해야 합니다. `WHERE` 절에서 UDF와 함께 인덱스를 사용하는 추가 필터 조건자를 조합하면 UDF에서 처리하는 문서 수가 줄어듭니다.

쿼리에서 동일한 UDF를 여러 번 사용해야 하는 경우에는 [하위 쿼리](sql-query-subquery.md#evaluate-once-and-reference-many-times)에서 UDF를 참조하여 UDF를 한 번만 평가하고 여러 번 참조하는 JOIN 식을 사용할 수 있습니다.

## <a name="examples"></a>예

다음 예제에서는 Cosmos 데이터베이스의 항목 컨테이너 아래에 UDF를 등록합니다. 이 예제에서는 이름이 `REGEX_MATCH`인 UDF를 만듭니다. 두 JSON 문자열 값 `input` and `pattern`를 받아들이고 JavaScript의 `string.match()` 함수를 사용하여 첫 번째 값이 두 번째 값에 지정된 패턴과 일치하는지를 확인합니다.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

이제 쿼리 프로젝션에서 이 UDF를 사용 합니다. 쿼리 내에서 호출할 경우 대/소문자 구분 접두사 `udf.`로 UDF를 한정해야 합니다.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

결과는 다음과 같습니다.

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

다음 예제와 같이 필터 내에서 `udf.` 접두사로 한정된 UDF를 사용할 수 있습니다.

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

기본적으로 UDF는 유효한 스칼라 식이며 프로젝션과 필터 모두에 사용될 수 있습니다.

UDF의 기능을 확장하려면 조건부 논리를 사용하여 다른 예제를 살펴보세요.

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

다음 예제에서는 UDF를 사용합니다.

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

결과는 다음과 같습니다.

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

UDF 매개 변수가 참조하는 속성을 JSON 값에 사용할 수 없는 경우 매개 변수가 정의되지 않은 것으로 간주되어 UDF 호출을 건너뜁니다. 마찬가지로, UDF 결과가 정의되지 않으면 결과에 포함되지 않습니다.

앞의 예제에 볼 수 있는 것처럼 UDF는 JavaScript 언어의 기능을 SQL API와 통합 합니다. 앞의 예제에서 소개한 대로 UDF는 JavaScript 언어 기능과 SQL API를 통합하여 기본 제공된 JavaScript 런타임 기능으로 복잡한 절차적 조건부 논리를 수행하는 풍부한 프로그래밍 가능 인터페이스를 제공합니다. SQL API는 현재 처리 단계(WHERE 절 또는 SELECT 절)에 있는 각 소스 항목에 대한 UDF에 인수를 제공합니다. 결과는 전체 실행 파이프라인에 매끄럽게 통합됩니다. 요약하면 UDF는 쿼리의 일부로 복잡한 비즈니스 논리를 수행하는 유용한 도구입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](../introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [집계](sql-query-aggregate-functions.md)