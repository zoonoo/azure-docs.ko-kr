---
title: Azure Cosmos DB 쿼리 언어에서 자르기
description: Azure Cosmos DB의 SQL 시스템 함수 트리밍에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 073efbd91e637a17693617b16c7787d61e150ad7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304245"
---
# <a name="trim-azure-cosmos-db"></a>TRIM (Azure Cosmos DB)
 선행 및 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
TRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리 내에서를 사용 하는 방법을 보여 줍니다 `TRIM` .  
  
```sql
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
