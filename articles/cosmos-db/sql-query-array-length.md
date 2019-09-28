---
title: Azure Cosmos DB 쿼리 언어의 ARRAY_LENGTH
description: Azure Cosmos DB의 SQL 시스템 함수 ARRAY_LENGTH에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b27b3f09212047e2e8937a4bf649fa3335e15cb2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348665"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 지정된 배열 식의 요소 수를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   는 배열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `ARRAY_LENGTH`을 사용 하 여 배열의 길이를 가져오는 방법을 보여 줍니다.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>다음 단계

- [배열 함수 Azure Cosmos DB](sql-query-array-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
