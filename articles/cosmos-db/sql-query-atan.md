---
title: Azure Cosmos DB 쿼리 언어의 ATAN
description: Azure Cosmos DB의 SQL 시스템 함수 ATAN에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 4ee3867f3a4938358e8d61aa8a98f747756ee3e8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348570"
---
# <a name="atan-azure-cosmos-db"></a>ATAN (Azure Cosmos DB)
 탄젠트 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크탄젠트라고도 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ATAN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 지정 된 값의 `ATAN`을 반환 합니다.  
  
```sql
SELECT ATAN(-45.01) AS atan  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"atan": -1.5485826962062663}]  
```  
  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
