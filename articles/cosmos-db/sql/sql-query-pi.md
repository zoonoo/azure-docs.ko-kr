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
ms.openlocfilehash: 15cb31f1ed98c9100b7bd04ec5f9e7b5102091b3
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530182"
---
# <a name="pi-azure-cosmos-db"></a>PI(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

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

- [수학 함수 Azure Cosmos DB](sql-query-mathematical-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
