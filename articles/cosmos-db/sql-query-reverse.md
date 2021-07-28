---
title: Azure Cosmos DB 쿼리 언어의 REVERSE
description: Azure Cosmos DB의 SQL 시스템 함수 REVERSE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7d70d65609211ea18f566dbae42aca5231ed2eb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341583"
---
# <a name="reverse-azure-cosmos-db"></a>REVERSE(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 문자열 값을 역순으로 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 쿼리에서 `REVERSE`를 사용하는 방법을 보여 줍니다.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
