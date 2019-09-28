---
title: Azure Cosmos DB 쿼리 언어의 상한
description: Azure Cosmos DB의 SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6b0f025948803a23c5b3c8bb6415c0e111b946b2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349048"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 소문자 데이터를 대문자로 변환한 후에 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리에서 `UPPER`을 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
