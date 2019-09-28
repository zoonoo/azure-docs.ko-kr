---
title: Azure Cosmos DB 쿼리 언어의 제곱
description: Azure Cosmos DB의 SQL 시스템 함수 사각형에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683db82f2574da9150fb64767047d6ada6d667da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349423"
---
# <a name="square-azure-cosmos-db"></a>제곱 (Azure Cosmos DB)
 지정한 숫자 값의 제곱을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
SQUARE(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 1에서 3까지의 숫자의 제곱을 반환합니다.  
  
```sql
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{s1: 1, s2: 4, s3: 9}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
