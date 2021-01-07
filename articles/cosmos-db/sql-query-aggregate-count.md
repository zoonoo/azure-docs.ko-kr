---
title: Azure Cosmos DB 쿼리 언어 수
description: Azure Cosmos DB의 Count (COUNT) SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553401"
---
# <a name="count-azure-cosmos-db"></a>개수 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 시스템 함수는 식에 있는 값의 개수를 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*scalar_expr*  
   스칼라 식입니다.
  
## <a name="return-types"></a>반환 형식
  
숫자 식을 반환합니다.  
  
## <a name="examples"></a>예제
  
다음 예에서는 컨테이너에 있는 항목의 총 개수를 반환 합니다.
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT는 스칼라 식을 입력으로 사용할 수 있습니다. 아래 쿼리는 동일한 결과를 생성 합니다.

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>설명

이 시스템 함수는 쿼리 필터의 모든 속성에 대해 [범위 인덱스](index-policy.md#includeexclude-strategy) 를 활용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수치 연산 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB 집계 함수](sql-query-aggregate-functions.md)