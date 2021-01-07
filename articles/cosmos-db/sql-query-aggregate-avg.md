---
title: Azure Cosmos DB 평균 쿼리 언어
description: Azure Cosmos DB의 평균 (AVG) SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555419"
---
# <a name="avg-azure-cosmos-db"></a>평균 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 집계 함수는 식에 있는 값의 평균을 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
숫자 식을 반환합니다.  
  
## <a name="examples"></a>예제
  
다음 예에서는의 평균값을 반환 합니다 `propertyA` .
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](index-policy.md#includeexclude-strategy)의 이점을 받습니다. 의 인수가 `AVG` string, boolean 또는 null 인 경우 전체 집계 시스템 함수는를 반환 `undefined` 합니다. 인수에 값이 있으면 `undefined` 계산에 영향을 주지 않습니다 `AVG` .

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB의 수치 연산 함수](sql-query-mathematical-functions.md)
- [Azure Cosmos DB의 시스템 함수](sql-query-system-functions.md)
- [Azure Cosmos DB 집계 함수](sql-query-aggregate-functions.md)