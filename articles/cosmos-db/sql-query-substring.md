---
title: Azure Cosmos DB 쿼리 언어의 부분 문자열
description: Azure Cosmos DB의 SQL 시스템 함수 부분 문자열에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6cb96270d1db970230e3abb528b58b7341d652f0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081480"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 지정한 문자 0 기준 위치에서 시작하여 지정한 길이 또는 문자열의 끝까지에 이르는 문자열 식의 일부를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.
  
*num_expr1*  
   시작 문자를 나타내는 숫자 식입니다. 값 0은 *str_expr* 의 첫 번째 문자입니다.
  
*num_expr2*  
   반환할 *str_expr* 최대 문자 수를 나타내는 숫자 식입니다. 0 보다 작은 값을 입력 하면 빈 문자열이 반환 됩니다.

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

이 시스템 함수는 시작 위치가 인 경우 [범위 인덱스](index-policy.md#includeexclude-strategy) 를 활용 합니다 `0` .

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
