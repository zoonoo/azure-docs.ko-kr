---
title: Azure Cosmos DB 쿼리 언어의 ATN2
description: Azure Cosmos DB의 ATN2 SQL 시스템 함수가 y/x의 원호의 아크탄젠트 값을 라디안으로 표현 하 여 반환 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6db42713ec6f0eac64e0f1123825c21a4206fa99
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341946"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 y/x에 대한 아크 탄젠트의 주요 값(라디안 단위)을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 x 및 y 구성 요소에 대한 ATN2를 계산합니다.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
