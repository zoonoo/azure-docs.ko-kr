---
title: Azure 코스모스 DB 쿼리 언어의 TAN
description: Azure Cosmos DB에서 SQL 시스템 기능 TAN에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301984"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure 코스모스 DB)
 지정된 식에서 지정된 각도(라디안 단위)의 탄젠트를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
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

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
