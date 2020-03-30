---
title: Azure 코스모스 DB 쿼리 언어의 천장
description: Azure Cosmos DB의 천장 SQL 시스템 함수가 지정된 숫자 식보다 크거나 같게 가장 작은 정수 값을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302647"
---
# <a name="ceiling-azure-cosmos-db"></a>천장 (푸른 코스모스 DB)
 지정한 숫자 식보다 크거나 같은 가장 작은 정수 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 함수를 통해 양수 숫자, `CEILING` 음수 및 0 값을 보여 주습니다.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스의](index-policy.md#includeexclude-strategy)이점을 누릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
