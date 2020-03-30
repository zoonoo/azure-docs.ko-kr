---
title: Azure 코스모스 DB 쿼리 언어의 ABS
description: Azure Cosmos DB의 절대(ABS) SQL 시스템 함수가 지정된 숫자 식의 양수 값을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6d173827f2695cc20fa208d390731acf0edb3848
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301100"
---
# <a name="abs-azure-cosmos-db"></a>ABS (Azure 코스모스 DB)
 지정한 숫자 식의 절대(양수) 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 세 가지 `ABS` 다른 숫자에 함수를 사용 하 여 의 결과 보여 합니다.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스의](index-policy.md#includeexclude-strategy)이점을 누릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
