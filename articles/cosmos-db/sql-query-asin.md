---
title: Azure 코스모스 DB 쿼리 언어의 ASIN
description: Azure Cosmos DB의 Arcsine(ASIN) SQL 시스템 함수가 지정된 숫자 식인 라디안에서 각도를 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302698"
---
# <a name="asin-azure-cosmos-db"></a>아신 (푸른 코스모스 DB)
 사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크사인이라고도 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제는 `ASIN` -1을 반환합니다.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
