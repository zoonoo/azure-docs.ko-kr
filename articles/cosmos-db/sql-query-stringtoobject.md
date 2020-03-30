---
title: Azure 코스모스 DB 쿼리 언어의 StringToObject
description: Azure 코스모스 DB의 SQL 시스템 함수 StringToObject에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296384"
---
# <a name="stringtoobject-azure-cosmos-db"></a>스트링토오브젝트(Azure 코스모스 DB)
 개체로 변환된 식을 반환합니다. 식을 번역할 수 없는 경우 정의되지 않은 반환을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 개체 식으로 구문 분석할 문자열 표현식입니다. 중첩된 문자열 값은 유효하려면 큰따옴표로 작성해야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/)  
  
## <a name="return-types"></a>반환 형식
  
  개체 식 또는 정의되지 않은 개체 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToObject` 서로 다른 형식에서 어떻게 행동하는지 보여 주며, 
  
 다음은 올바른 입력이 있는 예제입니다.

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

 다음은 잘못된 입력이 있는 예제입니다.
쿼리 내에서 유효하더라도 유효한 개체를 구문 분석하지 않습니다. 개체 문자열 내의 문자열은\\"{"a":\\\\"str"}이거나\\주변 따옴표는 단일 "{"a": "str"}이어야 합니다.

속성 이름을 둘러싼 단일 따옴표는 유효한 JSON이 아닙니다.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```json
[{}]
```  

주변 따옴표가 없는 속성 이름은 유효한 JSON이 아닙니다.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```json
[{}]
``` 

다음은 잘못된 입력이 있는 예제입니다.

 전달된 식은 JSON 개체로 구문 분석됩니다. 이러한 입력은 개체를 입력하기 위해 평가되지 않으므로 정의되지 않은 반환을 반환합니다.

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

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
