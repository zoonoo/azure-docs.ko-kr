---
title: Azure Cosmos DB 쿼리 언어의 ATN2
description: Azure Cosmos DB의 SQL 시스템 함수 ATN2에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43bf2f6e27d093b72560b87349150268e0f58a60
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350203"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 y/x에 대한 아크 탄젠트의 주요 값(라디안 단위)을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 x 및 y 구성 요소에 대한 ATN2를 계산합니다.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
