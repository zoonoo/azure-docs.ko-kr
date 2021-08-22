---
title: Azure Cosmos DB 쿼리 언어의 TAN
description: Azure Cosmos DB의 SQL 시스템 함수 TAN에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 84731817f5ec2ab73c9a8089554508f39db35c70
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529899"
---
# <a name="tan-azure-cosmos-db"></a>TAN(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 지정된 식에서 지정된 각도(라디안 단위)의 탄젠트를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 PI()/2의 탄젠트를 계산합니다.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
