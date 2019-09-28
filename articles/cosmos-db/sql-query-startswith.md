---
title: Azure Cosmos DB 쿼리 언어의 STARTSWITH
description: Azure Cosmos DB의 SQL 시스템 함수 STARTSWITH에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349321"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   는 문자열 식입니다.
  
*str_expr2*  
   *Str_expr1*의 시작과 비교할 문자열 식입니다.

## <a name="return-types"></a>반환 유형
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 "abc" 문자열이 "b"와 "a"로 시작하는지 확인합니다.  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
