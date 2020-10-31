---
title: Azure Cosmos DB 쿼리 언어의 CONCAT
description: Azure Cosmos DB의 CONCAT SQL 시스템 함수가 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0b3401e8de4987670f74a02286cb66eb7a9dfb29
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081623"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   다른 값에 연결 하는 문자열 식입니다. 함수에는 `CONCAT` 두 개 이상의 *str_expr* 인수가 필요 합니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 값의 연결된 문자열을 반환합니다.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
