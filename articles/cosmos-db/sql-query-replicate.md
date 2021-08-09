---
title: Azure Cosmos DB 쿼리 언어의 REPLICATE
description: Azure Cosmos DB의 SQL 시스템 함수 REPLICATE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7c0f1673c620ceadeb5ccca2a15cc9b7ce8d7685
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341606"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 지정한 횟수만큼 문자열 값을 반복합니다.
  
## <a name="syntax"></a>구문
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.
  
*num_expr*  
   숫자 식입니다. *num_expr* 이 음수이거나 무한 값이면 결과가 정의되지 않습니다.
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.
  
## <a name="remarks"></a>설명

  결과의 최대 길이는 10,000자입니다(length(*str_expr*)  *  *num_expr*) <= 10,000). 이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="examples"></a>예
  
  다음 예제에서는 쿼리에서 `REPLICATE`를 사용하는 방법을 보여 줍니다.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 결과 집합은 다음과 같습니다.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
