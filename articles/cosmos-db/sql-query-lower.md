---
title: Azure Cosmos DB 쿼리 언어 낮음
description: Azure Cosmos DB의 SQL 시스템 함수에 대해 자세히 알아보세요.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3a9c122ef65772458b832d3b1651e7e63e42985e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349712"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리에서 `LOWER`을 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
