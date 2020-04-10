---
title: Azure 코스모스 DB의 사용자 정의 함수(UdF)
description: Azure Cosmos DB에서 사용자 정의 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.openlocfilehash: 455f44fb365152b75a3811563b646c6243f686db
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011126"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure 코스모스 DB의 사용자 정의 함수(UdF)

SQL API는 사용자 정의 함수(UdF)에 대한 지원을 제공합니다. 스칼라 UdF를 사용하면 0 개 또는 여러 인수를 전달하고 단일 인수 결과를 반환할 수 있습니다. API는 각 인수를 법적 JSON 값으로 검사합니다.  

## <a name="udf-use-cases"></a>UDF 사용 사례

API는 UDF를 사용하여 사용자 지정 응용 프로그램 논리를 지원하도록 SQL 구문을 확장합니다. SQL API에 UdF를 등록하고 SQL 쿼리에서 참조할 수 있습니다. 저장 프로시저 및 트리거와 달리 UdF는 읽기 전용입니다.

UdF를 사용하여 Azure Cosmos DB의 쿼리 언어를 확장할 수 있습니다. UdF는 쿼리의 프로젝션에서 복잡한 비즈니스 논리를 표현할 수 있는 좋은 방법입니다.

그러나 다음과 같은 경우 UdF를 피하는 것이 좋습니다.

- Azure Cosmos DB에 동등한 [시스템 함수가](sql-query-system-functions.md) 이미 있습니다. 시스템 함수는 항상 동등한 UDF보다 더 적은 RU를 사용합니다.
- UDF는 쿼리 `WHERE` 절의 유일한 필터입니다. UDF는 인덱스를 사용하지 않으므로 UDF를 평가하려면 문서를 로드해야 합니다. `WHERE` 인덱스를 사용하는 추가 필터 조건자(UDF)를 UDF와 결합하면 UDF에서 처리하는 문서 수가 줄어듭니다.

쿼리에서 동일한 UDF를 여러 번 사용해야 하는 경우 [하위 쿼리에서](sql-query-subquery.md#evaluate-once-and-reference-many-times)UDF를 참조해야 하므로 JOIN 식을 사용하여 UDF를 한 번 평가하지만 여러 번 참조할 수 있습니다.

## <a name="examples"></a>예

다음 예제에서는 Cosmos 데이터베이스의 항목 컨테이너 아래에 UDF를 등록합니다. 이 예제는 이름이 .UDF를 `REGEX_MATCH`만듭니다. 두 개의 JSON 문자열 `input` 값을 `pattern`허용하고 , 첫 번째 가 자바 스크립트의 `string.match()` 함수를 사용하여 두 번째에 지정된 패턴과 일치하는지 확인합니다.

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

이제 쿼리 프로젝션에서 이 UDF를 사용합니다. 쿼리 내에서 호출할 때 대/소문자 `udf.` 구분 접두사로 DUD를 한정해야 합니다.

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

다음 예제와 같이 필터 `udf.` 내부의 접두사와 함께 정규화된 UDF를 사용할 수 있습니다.

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

기본적으로 UdF는 프로젝션과 필터 모두에서 사용할 수 있는 유효한 스칼라 식입니다.

UDF의 힘을 확장하려면 조건부 논리를 사용하여 다른 예제를 살펴보십시오.

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

다음 예제는 UDF를 연습합니다.

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

UDF 매개 변수에서 참조하는 속성을 JSON 값에서 사용할 수 없는 경우 매개 변수는 정의되지 않은 것으로 간주되고 UDF 호출은 건너뜁니다. 마찬가지로 UDF의 결과가 정의되지 않은 경우 결과에 포함되지 않습니다.

앞의 예에서 볼 수 있듯이 UDF는 자바스크립트 언어의 강력한 힘을 SQL API와 통합합니다. UdF는 기본 제공 JavaScript 런타임 기능을 사용하여 복잡한 절차, 조건부 논리를 수행할 수 있는 풍부한 프로그래밍 가능한 인터페이스를 제공합니다. SQL API는 현재 WHERE 또는 SELECT 처리 단계의 각 소스 항목에 대한 인수를 UdF에 제공합니다. 결과는 전체 실행 파이프라인에 원활하게 통합됩니다. 요약하자면, UdF는 쿼리의 일부로 복잡한 비즈니스 논리를 수행하는 훌륭한 도구입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [집계](sql-query-aggregates.md)