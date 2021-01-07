---
title: Azure Cosmos DB 쿼리 언어의 PI
description: Azure Cosmos DB의 SQL 시스템 함수 PI에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0ebe94d8b06c342fc61fae4255fa354150974c4f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341714"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 파이의 상수 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예제
  
  다음 예에서는 `PI`의 값을 반환합니다.  
  
```sql
SELECT PI() AS pi 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>다음 단계

- [수치 연산 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
