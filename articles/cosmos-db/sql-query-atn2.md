---
title: Azure Cosmos DB 쿼리 언어의 ATN2
description: Azure Cosmos DB의 SQL 시스템 함수 ATN2가 y/x의 아크탄젠트 주 값을 라디안으로 반환하는 방법을 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6db42713ec6f0eac64e0f1123825c21a4206fa99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341946"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2(Azure Cosmos DB)
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

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
