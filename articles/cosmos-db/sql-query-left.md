---
title: Azure Cosmos DB 쿼리 언어 왼쪽
description: Azure Cosmos DB에서 남은 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303752"
---
# <a name="left-azure-cosmos-db"></a>LEFT (Azure Cosmos DB)
 지정된 수의 문자로 문자열의 왼쪽 부분을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자를 추출할 문자열 식입니다.  
  
*num_expr*  
   문자 수를 지정 하는 숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 왼쪽 부분을 반환합니다.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
