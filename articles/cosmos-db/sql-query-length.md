---
title: Azure Cosmos DB 쿼리 언어의 길이
description: Azure Cosmos DB의 SQL 시스템 함수 길이에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78303718"
---
# <a name="length-azure-cosmos-db"></a>길이 (Azure Cosmos DB)
 지정한 문자열 식의 문자 수를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   평가할 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 문자열의 길이를 반환합니다.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
