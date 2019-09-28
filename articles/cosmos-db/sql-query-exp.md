---
title: Azure Cosmos DB 쿼리 언어의 EXP
description: Azure Cosmos DB의 SQL 시스템 함수 EXP에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 21c7ae63f46f2acd961245c59805220174c106f1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351033"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 지정된 숫자 식의 지수 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  **e**(2.718281…) 상수는 자연 로그의 밑입니다.  
  
  숫자의 지수는 **e** 상수를 거듭제곱하는 횟수입니다. 예를 들어 EXP(1.0) = e^1.0 = 2.71828182845905이며, EXP(10) = e^10 = 22026.4657948067입니다.  
  
  숫자의 자연 로그의 지수는 숫자 자체, 즉 EXP (LOG (n)) = n입니다. 그리고 숫자의 지수의 자연 로그도 숫자 자체, 즉 LOG (EXP (n)) = n입니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 지수 값을 반환합니다.  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 다음 예제에서는 20의 자연 로그의 지수 값과 및 20의 지수의 자연 로그를 반환합니다. 이러한 함수는 서로 역함수이므로 두 경우 모두 부동 소수점 수치가 반올림된 반환 값은 20입니다.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
