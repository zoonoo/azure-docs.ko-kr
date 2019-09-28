---
title: Azure Cosmos DB 쿼리 언어의 COT
description: Azure Cosmos DB의 SQL 시스템 함수 COT에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351238"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 지정된 숫자 식의 라디안에서 지정된 각도의 삼각 코탄젠트를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 지정 된 각도의 `COT`을 계산 합니다.  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
