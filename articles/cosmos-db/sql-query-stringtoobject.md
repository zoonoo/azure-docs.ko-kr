---
title: Azure Cosmos DB 쿼리 언어 StringToObject
description: Azure Cosmos DB의 SQL 시스템 함수 StringToObject에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 369c86c8fc2ef4d125c3f13db4eb3af88b7e726e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337849"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 개체로 변환된 식을 반환합니다. 식을 변환할 수 없는 경우 undefined을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 개체 식으로 구문 분석할 문자열 식입니다. 중첩된 문자열 값은 큰따옴표를 사용하여 유효하게 작성해야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/)를 참조하세요  
  
## <a name="return-types"></a>반환 형식
  
  개체 식 또는 undefined을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예는 `StringToObject`이 여러 유형에서 작동하는 방식을 보여줍니다. 
  
 다음은 유효한 입력을 사용한 예입니다.

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

 다음은 잘못된 입력을 사용한 예입니다.
쿼리 내에서 유효 하더라도 이는 유효한 개체로 구문 분석되지 않습니다. 개체의 문자열에 있는 문자열은 "{\\"a\\":\\"str\\"}"로 이스케이프되거나, 주변은 작은 따옴표 '{"a": "str"}'여야 합니다.

속성 이름을 둘러싼 작은 따옴표는 유효한 JSON이 아닙니다.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```json
[{}]
```  

따옴표를 포함하지 않는 속성 이름은 유효한 JSON이 아닙니다.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```json
[{}]
``` 

다음은 잘못된 입력을 사용한 예입니다.

 전달된 식은 JSON 개체로 구문 분석됩니다. 이러한 입력은 개체 형식으로 계산되지 않으므로 undefined로 반환됩니다.

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

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
