---
title: Azure 코스모스 DB 쿼리 언어의 ToString
description: Azure 코스모스 DB의 SQL 시스템 기능 ToString에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 293449b1616e7124245d91c647177b958006009e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304262"
---
# <a name="tostring-azure-cosmos-db"></a>토스트링 (Azure 코스모스 DB)
 스칼라 식의 문자열 표현을 반환합니다. 
  
## <a name="syntax"></a>구문
  
```sql
ToString(<expr>)
```  
  
## <a name="arguments"></a>인수
  
*Expr*  
   어떤 스칼라 표현식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `ToString` 서로 다른 형식에서 어떻게 행동하는지 보여 주며,   
  
```sql
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 다음 입력을 지정합니다.
```json
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 다음 예제에서는 `ToString` 와 같은 `CONCAT`다른 문자열 함수와 함께 사용할 수 있는 방법을 보여 주며 있습니다.   
 
```sql
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

결과 집합은 다음과 같습니다.  
  
```json
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
다음 입력을 지정합니다.
```json
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
다음 예제에서는 `ToString` 와 같은 `REPLACE`다른 문자열 함수와 함께 사용할 수 있는 방법을 보여 주며 있습니다.   
```sql
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
결과 집합은 다음과 같습니다.  
 ```json
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
