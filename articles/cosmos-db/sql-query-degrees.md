---
title: Azure 코스모스 DB 쿼리 언어의 학위
description: Azure Cosmos DB의 DEGREES SQL 시스템 함수에 대해 알아서 해당 각도를 라디안에 지정된 각도에 대해 해당 각도를 각도로 반환합니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299473"
---
# <a name="degrees-azure-cosmos-db"></a>학위 (Azure 코스모스 DB)
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

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
