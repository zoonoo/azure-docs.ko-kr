---
title: Azure 코스모스 DB 쿼리 언어로 복제
description: Azure Cosmos DB에서 SQL 시스템 기능 복제에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302188"
---
# <a name="replicate-azure-cosmos-db"></a>복제 (Azure 코스모스 DB)
 지정한 횟수만큼 문자열 값을 반복합니다.
  
## <a name="syntax"></a>구문
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.
  
*num_expr*  
   숫자 식입니다. *num_expr* 음수이거나 유한하지 않은 경우 결과가 정의되지 않습니다.
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.
  
## <a name="remarks"></a>설명
  결과의 최대 길이는 10,000자(길이(길이)str_expr*str_expr** *num_expr)*<= 10,000자입니다.

## <a name="examples"></a>예
  
  다음 예제에서는 쿼리에서 `REPLICATE` 사용하는 방법을 보여 주며 있습니다.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 결과 집합은 다음과 같습니다.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
