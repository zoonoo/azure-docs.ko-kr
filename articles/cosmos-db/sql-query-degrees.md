---
title: Azure Cosmos DB 쿼리 언어의 DEGREES
description: Azure Cosmos DB의 DEGREES SQL 시스템 함수에 대해 알아봅니다. 라디안으로 지정된 각도에 해당하는 각도(도)를 반환합니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5c7aa5496533a42ad726f9ed8efd8ed7429375fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338903"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 라디안에서 지정된 각도로 해당하는 각도를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 PI/2 라디안 각도의 각도 수를 반환합니다.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
