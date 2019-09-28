---
title: Azure Cosmos DB 쿼리 언어의 IS_DEFINED
description: Azure Cosmos DB의 SQL 시스템 함수 IS_DEFINED에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2c3193262a41b3c6772d4fe29c78a132bc51bbd8
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349879"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*expr*  
   임의의 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 JSON 문서 내에 속성이 있는지 여부를 확인합니다. "a"가 있으므로 첫 번째 함수는 true를 반환하지만, "b"가 없으므로 두 번째 함수는 false를 반환합니다.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="next-steps"></a>다음 단계

- [형식 검사 함수 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
