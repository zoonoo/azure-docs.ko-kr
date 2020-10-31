---
title: Azure Cosmos DB 쿼리 언어의 COS
description: Azure Cosmos DB의 코사인 (COS) SQL 시스템 함수가 지정 된 식에서 라디안으로 지정 된 각도의 삼각 코사인을 반환 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5f60b9f321b7d1ef75a1927310c788ff3ee3270
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100850"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 지정된 식의 라디안에서 지정된 각도의 삼각 코사인을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 지정 된 `COS` 각도의를 계산 합니다.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
