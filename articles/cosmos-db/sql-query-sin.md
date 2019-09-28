---
title: Azure Cosmos DB 쿼리 언어의 SIN
description: Azure Cosmos DB의 SQL 시스템 함수 사인에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349472"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 지정된 식의 라디안에서 지정된 각도의 삼각 사인을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 지정 된 각도의 `SIN`을 계산 합니다.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
