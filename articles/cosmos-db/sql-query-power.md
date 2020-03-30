---
title: Azure 코스모스 DB 쿼리 언어의 POWER
description: Azure 코스모스 DB에서 SQL 시스템 기능 POWER에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349644"
---
# <a name="power-azure-cosmos-db"></a>파워 (Azure 코스모스 DB)
 지정된 식을 거듭제곱한 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr1*  
   숫자 식입니다.  
  
*numeric_expr2*  
   *numeric_expr1*올릴 수있는 힘입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 3의 승수로 거듭 제곱한 수(숫자의 세제곱)를 보여줍니다.  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
