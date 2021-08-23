---
title: Azure Cosmos DB 쿼리 언어의 ABS
description: Azure Cosmos DB의 SQL 시스템 함수 Absolute(ABS)가 지정된 숫자 식의 양수 값을 반환하는 방법을 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 382252f0a7361a15cc5dc4bc14929e905bd72310
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537807"
---
# <a name="abs-azure-cosmos-db"></a>ABS(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정한 숫자 식의 절대(양수) 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ABS (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 세 가지 숫자에 `ABS` 함수를 사용한 결과를 보여 줍니다.  
  
```sql
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{abs1: 1, abs2: 0, abs3: 1}]  
```

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
