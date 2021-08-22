---
title: Azure Cosmos DB의 SQL 쿼리 연산자
description: Azure Cosmos DB에서 지원하는 같음, 비교 및 논리 연산자와 같은 SQL 연산자에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: d7188bf10a31f3fbad207ec023a621f27c4659fb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537731"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB의 연산자
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Cosmos DB에서 지원하는 다양한 연산자에 대해 자세히 설명합니다.

## <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **정의되지 않음** | **Null** | **Boolean** | **Number** | **String** | **개체** | **배열** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | 정의되지 않음 | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **개체** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | 정의되지 않음 |
| **배열** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

`>`, `>=`, `!=`, `<` 및 `<=`와 같은 비교 연산자의 경우 유형 간 또는 두 개체 또는 배열 간의 비교는 `Undefined`를 생성합니다.  

스칼라 식의 결과가 `Undefined`이면 `Undefined`가 `true`와 같지 않기 때문에 항목이 결과에 포함되지 않습니다.

예를 들어 다음 쿼리의 숫자와 문자열 값을 비교하면 `Undefined`가 생성됩니다. 따라서 필터에는 결과가 포함되지 않습니다.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표는 이러한 연산자에 대한 논리적 진리표를 보여 줍니다.

**OR 연산자**

조건 중 하나가 `true`이면 `true`를 반환합니다.

|  | **True** | **False** | **정의되지 않음** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |정의되지 않음 |
| **Undefined** |True |Undefined |Undefined |

**AND 연산자**

두 식이 모두 `true`인 경우 `true`를 반환합니다.

|  | **True** | **False** | **정의되지 않음** |
| --- | --- | --- | --- |
| **True** |True |False |정의되지 않음 |
| **False** |False |False |False |
| **Undefined** |정의되지 않음 |False |정의되지 않음 |

**NOT 연산자**

부울 식의 값을 반대로 합니다.

|  | **다음이 아님** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |정의되지 않음 |

**연산자 우선 순위**

논리 연산자 `OR`, `AND` 및 `NOT`의 우선 순위 수준은 다음과 같습니다.

| **연산자** | **우선 순위** |
| --- | --- |
| **다음이 아님** |1 |
| **및** |2 |
| **OR** |3 |

## <a name="-operator"></a>* 연산자

특수 연산자 *는 전체 항목을 있는 그대로 프로젝션합니다. 사용할 경우 프로젝션되는 유일한 필드여야 `SELECT * FROM Families f`와 같은 쿼리는 유효하지만 `SELECT VALUE * FROM Families f` 및 `SELECT *, f.id FROM Families f`와 같은 쿼리는 유효하지 않습니다.

## <a name="-and--operators"></a>? 및 ?? 연산자

C# 및 JavaScript와 같은 프로그래밍 언어에서와 같이 3항(?) 및 병합(??) 연산자를 사용하여 조건식을 작성할 수 있습니다.

사용할 수는 ? 연산자를 사용하여 즉시 새 JSON 속성을 생성합니다. 예를 들어 다음 쿼리는 등급 수준을 `elementary` 또는 `other`로 분류합니다.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

다음 쿼리에서와 같이 ? 연산자에 대한 호출을 중첩할 수도 있습니다. 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

다른 쿼리 연산자와 마찬가지로 ? 연산자는 참조된 속성이 없거나 비교되는 유형이 다른 경우 항목을 제외합니다.

?? 연산자를 사용하여 반구조화 또는 혼합 유형 데이터에 대해 쿼리할 때 항목의 속성을 효율적으로 확인합니다. 예를 들어 다음 쿼리는 `lastName`(있는 경우), `surname`(`lastName`이 없는 경우)을 반환합니다.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [C++ 키워드](sql-query-keywords.md)
- [SELECT 절](sql-query-select.md)
