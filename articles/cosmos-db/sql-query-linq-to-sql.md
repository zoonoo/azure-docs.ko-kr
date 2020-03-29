---
title: Azure 코스모스 DB에서 LINQ에서 SQL 번역으로
description: 지원되는 LINQ 연산자와 LINQ 쿼리가 Azure Cosmos DB의 SQL 쿼리에 매핑되는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441247"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL 변환

Azure Cosmos DB 쿼리 공급자는 LINQ 쿼리에서 Cosmos DB SQL 쿼리로 최상의 매핑을 수행합니다. 다음 설명은 LINQ에 대한 기본적인 친숙함을 가정합니다.

쿼리 공급자 형식 시스템은 숫자, 부울, 문자열 및 null과 같은 JSON 기본 형식만 지원합니다.

쿼리 공급자는 다음과 같은 스칼라 식을 지원합니다.

- 쿼리 평가 시간에 기본 데이터 형식의 상수 값을 포함한 상수 값입니다.
  
- 개체 또는 배열 요소의 속성을 참조하는 속성/배열 인덱스 식입니다. 예를 들어:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 숫자 및 부울 값에 대한 일반적인 산술 식을 포함한 산술 식입니다. 전체 목록은 [Azure 코스모스 DB SQL 사양을](https://go.microsoft.com/fwlink/p/?LinkID=510612)참조하십시오.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 문자열 값을 일부 상수 문자열 값과 비교하는 것을 포함하는 문자열 비교 식입니다.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 복합 값 형식 또는 익명 형식또는 이러한 개체의 배열을 반환하는 개체/배열 만들기 식입니다. 이러한 값을 중첩할 수 있습니다.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>지원되는 LINQ 연산자

SQL .NET SDK에 포함된 LINQ 공급자는 다음 연산을 지원합니다.

- **선택**: 투영은 개체 구성을 포함하여 SQL SELECT로 변환됩니다.
- **위치**: 필터는 SQL WHERE로 변환하고 `||`에서 `!` 와 SQL 연산자 간에 `&&`의 번역을 지원합니다.
- **SelectMany**: SQL JOIN 절에 대한 배열 해제를 허용합니다. 배열 요소를 필터링하기 위해 식을 연결하거나 중첩하는 데 사용합니다.
- **OrderBy** 및 **orderBy내하강**: ASC 또는 DESC를 사용하여 주문으로 변환합니다.
- 집계를 위한 **Count**, **Sum**, **Min**, **Max** 및 **Average** 연산자와 해당 비동기 동급 연산자 **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** 및 **AverageAsync**
- **CompareTo**: 범위 비교로 변환합니다. .NET에서 비교할 수 없기 때문에 일반적으로 문자열에 사용됩니다.
- **건너뛰기** 및 **테이크**: 쿼리의 결과를 제한하고 페이지 이동을 수행하기 위해 SQL OFFSET 및 LIMIT로 변환합니다.
- **수학 함수** `Abs`: .NET , `Acos` `Asin`, `Atan` `Ceiling` `Cos` `Exp` `Floor` `Log` `Log10` `Pow` `Round` `Sign`, , , , , , , `Truncate` , 및 이에 상응하는 SQL 기본 제공 함수로의 번역을 지원합니다. `Sin` `Sqrt` `Tan`
- **문자열 함수** `Concat`: .NET , `Contains` `Count`, `EndsWith``IndexOf`, `Replace` `Reverse` `StartsWith` `SubString` `ToLower` `ToUpper` `TrimEnd`, , , 및 `TrimStart` 이에 상응하는 SQL 기본 제공 함수로의 번역을 지원합니다.
- **배열 함수**: `Concat`.NET `Contains` `Count` 및 이에 상응하는 SQL 기본 제공 함수로의 변환을 지원합니다.
- **지리 공간 확장 기능**: 스텁 `Distance` `IsValid`메서드 `IsValidDetailed`, `Within` 및 이에 상응하는 SQL 기본 제공 함수로의 변환을 지원합니다.
- **사용자 정의 함수 확장 기능**: 스텁 `UserDefinedFunctionProvider.Invoke` 메서드에서 해당 사용자 정의 함수로의 변환을 지원합니다.
- **기타**: 조건부 연산자의 `Coalesce` 번역 및 변환을 지원합니다. 컨텍스트에 `Contains` 따라 문자열 포함, ARRAY_CONTAINS 또는 SQL IN으로 변환할 수 있습니다.

## <a name="examples"></a>예

다음 예제에서는 일부 표준 LINQ 쿼리 연산자가 Cosmos DB 쿼리로 변환하는 방법을 보여 줍니다.

### <a name="select-operator"></a>연산자 선택

구문은 `input.Select(x => f(x))`입니다. 여기서 `f`는 스칼라 식입니다.

**연산자 선택, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**연산자 선택, 예 2:** 

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**연산자 선택, 예 3:**

- **LINQ 람다 식**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany 연산자

구문은 `input.SelectMany(x => f(x))`입니다. 여기서 `f`는 컨테이너 형식을 반환하는 스칼라 식입니다.

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where 연산자

구문은 `input.Where(x => f(x))`입니다. 여기서 `f`는 부울 값을 반환하는 스칼라 식입니다.

**여기서 연산자, 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**여기서 연산자, 예제 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>복합 SQL 쿼리

이전 연산자구성을 통해 보다 강력한 쿼리를 구성할 수 있습니다. Cosmos DB는 중첩된 컨테이너를 지원하므로 컴포지션을 연결하거나 중첩할 수 있습니다.

### <a name="concatenation"></a>연결

구문은 `input(.|.SelectMany())(.Select()|.Where())*`입니다. 연결된 쿼리는 선택적 `SelectMany` 쿼리로 시작한 다음 여러 `Select` `Where` 개 또는 연산자로 시작할 수 있습니다.

**연결, 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**연결, 예제 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**연결, 예제 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**연결, 예제 4:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>중첩

구문은 `input.SelectMany(x=>x.Q())` `Q` `Select`" `SelectMany`또는 `Where` 연산자입니다.

중첩된 쿼리는 외부 컨테이너의 각 요소에 내부 쿼리를 적용합니다. 한 가지 중요한 기능은 내부 쿼리가 자체 조인과 같이 외부 컨테이너의 요소 필드를 참조할 수 있다는 것입니다.

**중첩, 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**중첩, 예제 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**중첩, 예제 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
