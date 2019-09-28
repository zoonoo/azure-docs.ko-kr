---
title: Azure Cosmos DB 쿼리 언어의 TAN
description: Azure Cosmos DB의 SQL 시스템 함수 TAN에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 70636dbc00307ced8ad0cf84f41fe025c759997e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349168"
---
# <a name="tan-azure-cosmos-db"></a>황갈색 (Azure Cosmos DB)
 지정된 식에서 지정된 각도(라디안 단위)의 탄젠트를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 PI()/2의 탄젠트를 계산합니다.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
