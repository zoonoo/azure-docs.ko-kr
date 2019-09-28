---
title: Azure Cosmos DB 쿼리 언어의 RTRIM
description: Azure Cosmos DB에서 SQL 시스템 함수 RTRIM에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 511c085b465b8b70ae71c298054bcb535773837e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349496"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   유효한 문자열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리 내에서 `RTRIM`을 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
