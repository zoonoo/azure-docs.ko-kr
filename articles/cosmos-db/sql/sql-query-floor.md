---
title: Azure Cosmos DB 쿼리 언어의 FLOOR
description: 지정된 숫자 식보다 작거나 같은 최대 정수를 반환하는 Azure Cosmos DB의 SQL 시스템 함수 FLOOR에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dddaa1d4b89c41318b6496ec1702030773f2676a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567648"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정된 숫자 식보다 작거나 같은 최대 정수를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `FLOOR` 함수를 사용하여 양수, 음수, 0 값을 표시합니다.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스](../index-policy.md#includeexclude-strategy)의 이점을 받습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
