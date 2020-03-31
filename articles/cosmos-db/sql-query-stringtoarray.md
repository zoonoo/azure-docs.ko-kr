---
title: Azure 코스모스 DB 쿼리 언어의 StringToArray
description: Azure 코스모스 DB의 SQL 시스템 함수 StringToArray에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302919"
---
# <a name="stringtoarray-azure-cosmos-db"></a>스트링토어레이(Azure 코스모스 DB)
 배열로 변환된 식을 반환합니다. 식을 번역할 수 없는 경우 정의되지 않은 반환을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 배열 식으로 구문 분석할 문자열 식입니다. 
  
## <a name="return-types"></a>반환 형식
  
  배열 식 또는 정의되지 않은 함수를 반환합니다. 
  
## <a name="remarks"></a>설명
  중첩된 문자열 값은 유효한 JSON이되려면 큰따옴표로 작성해야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/)
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToArray` 서로 다른 형식에서 어떻게 행동하는지 보여 주며, 
  
 다음은 올바른 입력이 있는 예제입니다.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

결과 집합은 다음과 같습니다.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

다음은 잘못된 입력의 예입니다. 
   
 배열 내의 단일 따옴표는 유효한 JSON이 아닙니다.
쿼리 내에서 유효하더라도 유효한 배열을 구문 분석하지 않습니다. 배열 문자열 내의 문자열은 이스케이프되어야\\"["]"\\또는 주변 따옴표는 단일 '[""'''''여야 합니다.

```sql
SELECT
    StringToArray("['5','6','7']")
```

결과 집합은 다음과 같습니다.

```json
[{}]
```

다음은 잘못된 입력의 예입니다.
   
 전달된 식은 JSON 배열로 구문 분석됩니다. 다음은 배열을 입력하도록 평가하지 않으므로 정의되지 않은 반환입니다.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
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
