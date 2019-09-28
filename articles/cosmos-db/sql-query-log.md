---
title: Azure Cosmos DB 쿼리 언어 로그인
description: Azure Cosmos DB SQL 시스템 함수 로그에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349744"
---
# <a name="log-azure-cosmos-db"></a>로그 (Azure Cosmos DB)
 지정된 숫자 식의 자연 로그를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
*하단*  
   로그의 밑을 설정하는 선택적 숫자 인수입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  LOG()는 기본적으로 자연 로그를 반환합니다. 선택적인 base 매개 변수를 사용하여 로그의 밑을 다른 값으로 변경할 수 있습니다.  
  
  자연 로그는 **e**를 밑으로 하는 로그입니다. 여기서 **e**는 대략 2.718281828과 같은 무리 상수입니다.  
  
  그리고 숫자의 지수의 자연 로그도 숫자 자체, 즉 LOG( EXP( n ) ) = n입니다. 그리고 숫자의 자연 로그의 지수도 숫자 자체, 즉 EXP( LOG( n ) ) = n입니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 로그 값을 반환합니다.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 다음 예에서는 숫자의 지 수에 대 한 `LOG`을 계산 합니다.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
