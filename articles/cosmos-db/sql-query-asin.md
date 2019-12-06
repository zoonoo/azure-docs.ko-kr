---
title: Azure Cosmos DB 쿼리 언어의 ASIN
description: Azure Cosmos DB의 아크사인 (ASIN) SQL 시스템 함수가 사인 값이 지정 된 숫자 식인 각도를 라디안으로 반환 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e790eb5ae5eb780637b199a1a65dec2dd02d1bc
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871741"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크사인이라고도 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예시
  
  다음 예에서는-1의 `ASIN` 반환 합니다.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
