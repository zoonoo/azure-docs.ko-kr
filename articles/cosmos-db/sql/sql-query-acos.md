---
title: Azure Cosmos DB 쿼리 언어의 ACOS
description: Azure Cosmos DB의 SQL 시스템 함수 ACOS(아크코사인)가 코사인이 지정된 숫자 식인 각도를 라디안으로 반환하는 방법을 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: af76b7cb36deb4a58d2666497a957ff14ea90b03
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537902"
---
# <a name="acos-azure-cosmos-db"></a>ACOS(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 코사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크코사인이라고도 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 -1의 `ACOS`을 반환합니다.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
