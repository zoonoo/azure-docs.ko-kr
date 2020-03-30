---
title: Azure 코스모스 DB 쿼리 언어의 하위 문자열
description: Azure 코스모스 DB에서 SQL 시스템 함수 SUBSTRING에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303701"
---
# <a name="substring-azure-cosmos-db"></a>서브스트링(Azure 코스모스 DB)
 지정한 문자 0 기준 위치에서 시작하여 지정한 길이 또는 문자열의 끝까지에 이르는 문자열 식의 일부를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.
  
*num_expr1*  
   시작 문자를 나타내는 숫자 표현식입니다. 값 0은 *str_expr*첫 번째 문자입니다.
  
*num_expr2*  
   반환할 *str_expr* 문자의 최대 수를 나타내는 숫자 식입니다. 값이 0 이하이면 빈 문자열이 생성됩니다.

## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 1에서 시작하여 길이가 1인 "abc"의 부분 문자열을 반환합니다.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>설명

이 시스템 함수는 시작 위치가 있는 `0`경우 [범위 인덱스의](index-policy.md#includeexclude-strategy) 이점을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
