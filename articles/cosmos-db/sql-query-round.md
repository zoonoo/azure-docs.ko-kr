---
title: Azure 코스모스 DB 쿼리 언어의 ROUND
description: Azure 코스모스 DB에서 SQL 시스템 기능 ROUND에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302120"
---
# <a name="round-azure-cosmos-db"></a>라운드 (푸른 코스모스 DB)
 가장 가까운 정수 값으로 반올림한 숫자 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="remarks"></a>설명
  
  수행된 반올림 작업은 0에서 중간 점을 반올림합니다. 입력이 두 정수 사이에 정확히 속하는 숫자 식인 경우 결과는 0에서 가장 가까운 정수 값이 됩니다.  
  
  |<numeric_expr>|둥근|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
## <a name="examples"></a>예
  
  다음 예제에서는 첨부된 양수와 음수를 가장 가까운 정수로 반올림합니다.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  결과 집합은 다음과 같습니다.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스의](index-policy.md#includeexclude-strategy)이점을 누릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
