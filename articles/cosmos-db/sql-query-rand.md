---
title: Azure Cosmos DB 쿼리 언어의 RAND
description: Azure Cosmos DB의 SQL 시스템 함수 RAND에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3e287f119a67c8bb00a309833e2ad6fe0d88975a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076098"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 [0, 1)에서 임의로 생성 된 숫자 값을 반환 합니다.
 
## <a name="syntax"></a>구문
  
```sql
RAND ()  
```  

## <a name="return-types"></a>반환 형식

  숫자 식을 반환합니다.

## <a name="remarks"></a>설명

  `RAND`는 비결정 함수입니다. 의 반복적인 호출은 `RAND` 동일한 결과를 반환 하지 않습니다. 이 시스템 함수는 인덱스를 활용 하지 않습니다.


## <a name="examples"></a>예
  
  다음 예에서는 임의로 생성 된 숫자 값을 반환 합니다.
  
```sql
SELECT RAND() AS rand 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
