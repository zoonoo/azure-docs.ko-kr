---
title: Azure Cosmos DB 쿼리 언어의 StringToObject
description: Azure Cosmos DB의 SQL 시스템 함수 StringToObject에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3492d463133c15bd5e6cabb8a173ff806712125
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097093"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 개체로 변환 된 식을 반환 합니다. 식을 변환할 수 없는 경우는 undefined를 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 개체 식으로 구문 분석할 문자열 식입니다. 중첩 된 문자열 값은 큰따옴표를 사용 하 여 유효 하 게 작성 해야 합니다. JSON 형식에 대 한 자세한 내용은 [json.org](https://json.org/) 를 참조 하세요.  
  
## <a name="return-types"></a>반환 형식
  
  개체 식을 반환 하거나 정의 되지 않은를 반환 합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는가 `StringToObject` 서로 다른 형식에서 동작 하는 방법을 보여 줍니다. 
  
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

결과 집합은 다음과 같습니다.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 입력이 잘못 된 예는 다음과 같습니다.
쿼리 내에서 유효 하더라도 유효한 개체로 구문 분석 되지 않습니다. 개체의 문자열에 있는 문자열은 "{ \\ " a \\ ": \\ " str \\ "}"로 이스케이프 되거나, 닫는 따옴표는 단일 ' {"a": "str"} ' 여야 합니다.

속성 이름을 둘러싼 작은따옴표는 유효한 JSON이 아닙니다.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```json
[{}]
```  

따옴표를 포함 하지 않는 속성 이름은 유효한 JSON이 아닙니다.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```json
[{}]
``` 

입력이 잘못 된 예는 다음과 같습니다.

 전달 된 식은 JSON 개체로 구문 분석 됩니다. 이러한 입력은 object 형식으로 계산 되지 않으므로 undefined로 반환 됩니다.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 결과 집합은 다음과 같습니다.

```json
[{}]
```

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
