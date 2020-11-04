---
title: Azure Cosmos DB 쿼리 언어의 오른쪽
description: Azure Cosmos DB에서 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: be950fed563d8e80339dc80181e7fe5a78092734
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341582"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 지정된 수의 문자로 문자열의 오른쪽 부분을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자를 추출할 문자열 식입니다.  
  
*num_expr*  
   문자 수를 지정 하는 숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 오른쪽 부분을 반환합니다.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
