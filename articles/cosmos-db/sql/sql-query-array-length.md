---
title: Azure Cosmos DB 쿼리 언어의 ARRAY_LENGTH
description: Azure Cosmos DB의 배열 길이 SQL 시스템 함수가 지정된 배열 식의 요소 수를 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04db916a95fe12b7b4f52f00adce093eeee589cc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529982"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정된 배열 식의 요소 수를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   배열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 `ARRAY_LENGTH`를 사용하여 배열의 길이를 가져오는 방법을 보여 줍니다.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [배열 함수 Azure Cosmos DB](sql-query-array-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
