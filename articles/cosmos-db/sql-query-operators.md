---
title: Azure Cosmos DB에 대 한 SQL 쿼리 연산자
description: Azure Cosmos DB에서 지 원하는 같음, 비교 및 논리 연산자와 같은 SQL 연산자에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93335445"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 연산자
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB에서 지 원하는 다양 한 연산자에 대해 자세히 설명 합니다.

## <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **되지** | **Null** | **Boolean** | **Number** | **String** | **Object** | **배열** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | 정의되지 않음 | **그래** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **그래** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **그래** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **그래** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **그래** | 정의되지 않음 |
| **배열** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **그래** |

,,,, 등의 비교 연산자의 경우 `>` `>=` 형식 간 `!=` `<` `<=` 또는 두 개체 또는 배열 간의 비교는을 생성 `Undefined` 합니다.  

스칼라 식의 결과가 인 경우 `Undefined` 항목은와 같지 않기 때문에 결과에 포함 되지 않습니다 `Undefined` `true` .

예를 들어 다음 쿼리는 숫자와 문자열 값을 비교 하 여를 생성 `Undefined` 합니다. 따라서 필터에는 결과가 포함 되지 않습니다.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표에서는 이러한 연산자에 대 한 논리적 진위 테이블을 보여 줍니다.

**OR 연산자**

`true`조건 중 하나가 인 경우을 반환 합니다 `true` .

|  | **True** | **False** | **되지** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |정의되지 않음 |
| **Undefined** |True |Undefined |Undefined |

**AND 연산자**

`true`두 식이 모두 이면를 반환 `true` 합니다.

|  | **True** | **False** | **되지** |
| --- | --- | --- | --- |
| **True** |True |False |정의되지 않음 |
| **False** |False |False |False |
| **Undefined** |정의되지 않음 |False |정의되지 않음 |

**NOT 연산자**

부울 식의 값을 반대로 바꿉니다.

|  | **다음이 아님** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |정의되지 않음 |

**연산자 우선 순위**

논리 연산자, 및에는 아래와 같은 `OR` `AND` `NOT` 우선 순위 수준이 있습니다.

| **연산자** | **우선 순위** |
| --- | --- |
| **다음이 아님** |1 |
| **및** |2 |
| **OR** |3 |

## <a name="-operator"></a>* 연산자

특수 연산자 *는 전체 항목을 있는 그대로 프로젝션 합니다. 사용할 경우 프로젝션되는 유일한 필드여야 과 같은 쿼리는 `SELECT * FROM Families f` 유효 하지만 `SELECT VALUE * FROM Families f` 및  `SELECT *, f.id FROM Families f` 는 유효 하지 않습니다.

## <a name="-and--operators"></a>? ? 연산자

C # 및 JavaScript와 같은 프로그래밍 언어에서와 같이 삼항 (?) 및 병합 (??) 연산자를 사용 하 여 조건식을 작성할 수 있습니다.

사용할 수는 ? 새 JSON 속성을 즉시 생성 하는 연산자입니다. 예를 들어 다음 쿼리는 등급 수준을 또는로 분류 합니다 `elementary` `other` .

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

에 대 한 호출을 중첩 시킬 수도 있습니다. 다음 쿼리와 같이 연산자를 적용 합니다. 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

다른 쿼리 연산자와 마찬가지로? 참조 된 속성이 없거나 비교할 형식이 다른 경우 연산자는 항목을 제외 합니다.

??를 사용 하 여 반 구조화 된 데이터 또는 혼합 형식 데이터에 대해 쿼리할 때 항목에서 속성을 효율적으로 확인 하는 연산자입니다. 예를 들어 다음 쿼리는 있는 경우를 반환 하 고,가 없는 경우를 반환 합니다 `lastName` `surname` `lastName` .

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [키워드](sql-query-keywords.md)
- [SELECT 절](sql-query-select.md)
