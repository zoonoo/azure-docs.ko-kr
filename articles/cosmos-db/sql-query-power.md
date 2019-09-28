---
title: Azure Cosmos DB 쿼리 언어의 기능
description: Azure Cosmos DB의 SQL 시스템 기능 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349644"
---
# <a name="power-azure-cosmos-db"></a>전원 (Azure Cosmos DB)
 지정된 식의 값을 지정된 거듭제곱으로 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr1*  
   숫자 식입니다.  
  
*numeric_expr2*  
   *Numeric_expr1*를 발생 시킬 거듭제곱입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 숫자를 3의 지수로 거듭제곱합니다(숫자의 3제곱).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
