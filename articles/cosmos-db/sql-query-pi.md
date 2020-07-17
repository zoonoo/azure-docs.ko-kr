---
title: Azure Cosmos DB 쿼리 언어의 PI
description: Azure Cosmos DB의 SQL 시스템 함수 PI에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "71349660"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 파이의 상수 값을 반환합니다.  
  
## <a name="syntax"></a>Syntax
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
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
