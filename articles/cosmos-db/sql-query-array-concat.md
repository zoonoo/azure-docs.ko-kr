---
title: Azure Cosmos DB 쿼리 언어의 ARRAY_CONCAT
description: 배열이 두 개 이상의 배열 값을 연결한 결과인 배열을 반환 Azure Cosmos DB에서 SQL 시스템 함수를 연결 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 10370e16c95f4fc747dd3a66a56794da38562972
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871825"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 둘 이상의 배열 값을 연결한 결과인 배열을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   다른 값에 연결할 배열 식입니다. `ARRAY_CONCAT` 함수에는 *arr_expr* 인수가 두 개 이상 필요 합니다.  
  
## <a name="return-types"></a>반환 유형
  
  배열 식을 반환합니다.  
  
## <a name="examples"></a>예시
  
  다음 예제에서는 두 배열을 연결하는 방법을 보여 줍니다.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>다음 단계

- [배열 함수 Azure Cosmos DB](sql-query-array-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
