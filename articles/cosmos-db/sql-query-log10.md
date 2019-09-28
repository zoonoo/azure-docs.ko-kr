---
title: Azure Cosmos DB 쿼리 언어의 LOG10
description: Azure Cosmos DB의 SQL 시스템 함수 LOG10에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349722"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 지정한 숫자 식의 상용 로그(밑 10)를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expression*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  LOG10과 POWER 함수는 서로 역의 관계가 있습니다. 예를 들어 10 ^ LOG10(n) = n입니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (100)의 LOG10 값을 반환합니다.  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
