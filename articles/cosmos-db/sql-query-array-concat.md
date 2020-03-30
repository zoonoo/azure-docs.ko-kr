---
title: Azure 코스모스 DB 쿼리 언어로 ARRAY_CONCAT
description: Azure Cosmos DB의 Array Concat SQL 시스템 함수가 두 개 이상의 배열 값을 연결한 결과로 배열을 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295881"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure 코스모스 DB)
 둘 이상의 배열 값을 연결한 결과인 배열을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   다른 값에 연결하는 배열 식입니다. 함수에는 `ARRAY_CONCAT` 두 개 이상의 *arr_expr* 인수가 필요합니다.  
  
## <a name="return-types"></a>반환 형식
  
  배열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 두 배열을 연결하는 방법을 보여 줍니다.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [어레이 함수 Azure 코스모스 DB](sql-query-array-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
