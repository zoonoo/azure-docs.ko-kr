---
title: Azure Cosmos DB 쿼리 언어의도
description: Azure Cosmos DB의 SQL 시스템 함수도에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8c2109c2c11f137b1966741a95d1d0c02895016
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351186"
---
# <a name="degrees-azure-cosmos-db"></a>도 (Azure Cosmos DB)
 라디안에서으로 지정 된 각도 (도) 해당 각도 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 PI/2 라디안 각도의 각도 수를 반환합니다.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
