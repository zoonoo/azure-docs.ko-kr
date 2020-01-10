---
title: Azure Cosmos DB 쿼리 언어의 상한
description: Azure Cosmos DB의 SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728891"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 소문자 데이터를 대문자로 변환한 후에 문자열 식을 반환합니다.  

상위 시스템 함수는 인덱스를 사용 하지 않습니다. 대/소문자를 구분 하지 않는 비교를 자주 수행 하려는 경우에는 상위 시스템 함수에서 매우 많은 양의를 사용할 수 있습니다. 이 경우 비교할 때마다 데이터를 정규화 하는 데 상위 시스템 함수를 사용 하는 대신 삽입 시 대/소문자를 정규화 할 수 있습니다. 그런 다음 SELECT * FROM c WHERE UPPER (c. name) = ' BOB '과 같은 쿼리는 단순히 SELECT * FROM c WHERE c.name = ' BOB '이 됩니다.

## <a name="syntax"></a>구문
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예시
  
  다음 예제에서는 쿼리에서 `UPPER`를 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
