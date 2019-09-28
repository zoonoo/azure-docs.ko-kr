---
title: Azure Cosmos DB 쿼리 언어의 LTRIM
description: Azure Cosmos DB의 SQL 시스템 함수 LTRIM에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bbc9c09bd97b02a323e01c0b0d72ca1df3d13b15
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349681"
---
# <a name="ltrim-azure-cosmos-db"></a>LTRIM (Azure Cosmos DB)
 선행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리 내에서 `LTRIM`을 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
