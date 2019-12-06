---
title: Azure Cosmos DB 쿼리 언어의 ACOS
description: Azure Cosmos DB에서 ACOS (arccosice) SQL 시스템 함수가 코사인이 지정 된 숫자 식인 각도를 라디안으로 반환 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fd5baa3ffefb7039c40706253e633661a51150f2
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873525"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 코사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크코사인이라고도 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*numeric_expr*  
   숫자 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예시
  
  다음 예에서는-1의 `ACOS` 반환 합니다.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
