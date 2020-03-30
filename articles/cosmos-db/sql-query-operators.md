---
title: Azure 코스모스 DB에 대한 SQL 쿼리 연산자
description: Azure Cosmos DB에서 지원하는 같음, 비교 및 논리 연산자와 같은 SQL 연산자에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063567"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure 코스모스 DB의 연산자

이 문서에서는 Azure Cosmos DB에서 지원하는 다양한 연산자에 대해 자세히 설명합니다.

## <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **Undefined** | **Null** | **부울** | **수** | **문자열** | **개체** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **Null** | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **부울** | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **수** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **문자열** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 |
| **개체** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 |
| **Array** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** |

`>`의 `>=` `!=`경우 유형 `<` `<=`간에 비교하거나 두 개체 또는 배열 간에 비교를 `Undefined`생성하는 등의 비교 연산자.  

스칼라 식의 결과가 `Undefined`같지 않기 때문에 `Undefined` 항목이 결과에 포함되지 `true`않습니다.

## <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표는 이러한 연산자의 논리적 진실 테이블을 보여 준다.

**OR 연산자**

조건 `true` 중 하나가 있는 `true`경우 반환됩니다.

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |정의되지 않음 |
| **Undefined** |True |정의되지 않음 |정의되지 않음 |

**및 연산자**

두 `true` 식이 모두 `true`있는 경우 반환됩니다.

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |False |정의되지 않음 |
| **False** |False |False |False |
| **Undefined** |정의되지 않음 |False |정의되지 않음 |

**NOT 연산자**

부울 식의 값을 반전합니다.

|  | **그렇지 않습니다.** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |정의되지 않음 |

**연산자 우선 순위**

논리 `OR`연산자 `AND`은 `NOT` 다음과 같습니다.

| **연산자** | **Priority** |
| --- | --- |
| **그렇지 않습니다.** |1 |
| **및** |2 |
| **또는** |3 |

## <a name="-operator"></a>* 연산자

특수 연산자 * 전체 항목을 있는 것처럼 투영합니다. 사용할 경우 프로젝션되는 유일한 필드여야 와 같은 `SELECT * FROM Families f` 쿼리는 `SELECT VALUE * FROM Families f` 유효하지만 `SELECT *, f.id FROM Families f` 유효하지 않습니다.

## <a name="-and--operators"></a>? 및?? 연산자

C# 및 JavaScript와 같은 프로그래밍 언어에서와 같이 삼차(?) 및 Coalesce(?) 연산자에서 조건식을 작성할 수 있습니다.

사용할 수는 ? 연산자는 즉석에서 새로운 JSON 속성을 구성할 수 있습니다. 예를 들어 다음 쿼리는 등급 수준을 `elementary` `other`또는 다음으로 분류합니다.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

? 다음 쿼리에서와 같이 연산자: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

다른 쿼리 연산자와 마찬가지로 ? 연산자는 참조된 속성이 없거나 비교중인 형식이 다른 경우 항목을 제외합니다.

를 사용 하 여 ?? 반구조화 또는 혼합 형 데이터에 대해 쿼리할 때 항목의 속성을 효율적으로 확인할 수 있습니다. 예를 들어 다음 쿼리가 있는 `surname` 경우 `lastName` 또는 없는 경우 반환합니다. `lastName`

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [키워드가](sql-query-keywords.md)
- [select 절](sql-query-select.md)
