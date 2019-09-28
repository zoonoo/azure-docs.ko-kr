---
title: Azure Cosmos DB 쿼리 언어의 ENDSWITH
description: Azure Cosmos DB SQL 시스템 함수 ENDSWITH에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c988d63e597c77bc09a1d21ad391909bb55901
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351062"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   는 문자열 식입니다.  
  
*str_expr2*  
   *Str_expr1*의 끝과 비교할 문자열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 "abc"가 "b"와 "bc"로 끝나는지 여부를 나타내는 부울 값을 반환합니다.  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
