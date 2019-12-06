---
title: Azure Cosmos DB 쿼리 언어의 밑면
description: 지정 된 숫자 식 보다 작거나 같은 최대 정수를 반환 하 Azure Cosmos DB의 FLOOR SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8faf223a4871f6f109f9d133716989b1bcfdee08
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871179"
---
# <a name="floor-azure-cosmos-db"></a>바닥 (Azure Cosmos DB)
 지정한 숫자 식보다 작거나 같은 가장 큰 정수 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예시
  
  다음 예에서는 `FLOOR` 함수를 사용 하 여 양수, 음수 및 0 값을 보여 줍니다.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
