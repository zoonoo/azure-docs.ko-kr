---
title: Azure Cosmos DB에 대 한 SQL 쿼리 연산자
description: Azure Cosmos DB에서 지 원하는 같음, 비교 및 논리 연산자와 같은 SQL 연산자에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063567"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 연산자

이 문서에서는 Azure Cosmos DB에서 지 원하는 다양 한 연산자에 대해 자세히 설명 합니다.

## <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **정의되지 않음** | **Null** | **Boolean** | **숫자** | **String** | **Object** | **배열** |
|---|---|---|---|---|---|---|---|
| **정의되지 않음** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **Null** | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **Boolean** | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **숫자** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **String** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 | 정의되지 않음 |
| **Object** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** | 정의되지 않음 |
| **배열** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **그래** |

`>` `>=`, `!=`,, `<`, `<=`등의 비교 연산자의 경우 형식 간 또는 두 개체 또는 배열 간의 비교는을 `Undefined`생성 합니다.  

스칼라 식의 결과가 인 `Undefined`경우 항목은와 같지 `Undefined` `true`않기 때문에 결과에 포함 되지 않습니다.

## <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표에서는 이러한 연산자에 대 한 논리적 진위 테이블을 보여 줍니다.

**OR 연산자**

조건 `true` 중 하나가 인 경우을 `true`반환 합니다.

|  | **True** | **허위** | **정의되지 않음** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **허위** |True |False |정의되지 않음 |
| **정의되지 않음** |True |정의되지 않음 |정의되지 않음 |

**AND 연산자**

두 `true` 식이 모두 이면를 `true`반환 합니다.

|  | **True** | **허위** | **정의되지 않음** |
| --- | --- | --- | --- |
| **True** |True |False |정의되지 않음 |
| **허위** |False |False |False |
| **정의되지 않음** |정의되지 않음 |False |정의되지 않음 |

**NOT 연산자**

부울 식의 값을 반대로 바꿉니다.

|  | **나타내지** |
| --- | --- |
| **True** |False |
| **허위** |True |
| **정의되지 않음** |정의되지 않음 |

**연산자 우선 순위**

논리 연산자 `OR`, `AND`및 `NOT` 에는 아래와 같은 우선 순위 수준이 있습니다.

| **연산자** | **Priority** |
| --- | --- |
| **나타내지** |1 |
| **하거나** |2 |
| **디스크나** |3 |

## <a name="-operator"></a>* 연산자

특수 연산자 *는 전체 항목을 있는 그대로 프로젝션 합니다. 사용할 경우 프로젝션되는 유일한 필드여야 과 같은 `SELECT * FROM Families f` 쿼리는 유효 하지만 `SELECT VALUE * FROM Families f` 및 `SELECT *, f.id FROM Families f` 는 유효 하지 않습니다.

## <a name="-and--operators"></a>? ? 연산자

C # 및 JavaScript와 같은 프로그래밍 언어에서와 같이 삼항 (?) 및 병합 (??) 연산자를 사용 하 여 조건식을 작성할 수 있습니다.

사용할 수는 ? 새 JSON 속성을 즉시 생성 하는 연산자입니다. 예를 들어 다음 쿼리는 등급 수준을 또는 `elementary` `other`로 분류 합니다.

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

??를 사용 하 여 반 구조화 된 데이터 또는 혼합 형식 데이터에 대해 쿼리할 때 항목에서 속성을 효율적으로 확인 하는 연산자입니다. 예를 들어 다음 쿼리는 있는 `lastName` 경우를 반환 하 `surname` 고 `lastName` ,가 없는 경우를 반환 합니다.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [키워드가](sql-query-keywords.md)
- [SELECT 절](sql-query-select.md)
