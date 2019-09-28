---
title: Azure Cosmos DB 쿼리 언어 반전
description: Azure Cosmos DB에서 SQL 시스템 함수 역방향에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8237918232bd8ba8edb2b8f71440ffd73a913334
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349552"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE (Azure Cosmos DB)
 문자열 값의 순서와 반대로 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리에서 `REVERSE`을 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
