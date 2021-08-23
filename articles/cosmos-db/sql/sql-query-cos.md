---
title: Azure Cosmos DB 쿼리 언어의 COS
description: Azure Cosmos DB에서 코사인(COS) SQL 시스템 함수가 지정된 식에서 지정된 각도의 삼각 코사인을 라디안으로 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 39e643d641cabc90eb0e92b744b3065d99294e0c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567749"
---
# <a name="cos-azure-cosmos-db"></a>COS(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

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
  
  다음 예제는 지정된 각도의 `COS`을 계산합니다.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
