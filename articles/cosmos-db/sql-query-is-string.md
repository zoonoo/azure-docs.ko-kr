---
title: Azure 코스모스 DB 쿼리 언어로 IS_STRING
description: Azure Cosmos DB에서 IS_STRING SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a717f343b0f46522a3ce2bb56c32e3f15998d777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303769"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure 코스모스 DB)
 지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*Expr*  
   어떤 표현식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 함수를 사용하여 JSON Boolean, 숫자, 문자열, null, 개체, 배열 및 정의되지 않은 형식의 개체를 `IS_STRING` 검사합니다.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스의](index-policy.md#includeexclude-strategy)이점을 누릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [유형 검사 기능 Azure 코스모스 DB](sql-query-type-checking-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
