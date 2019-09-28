---
title: Azure Cosmos DB 쿼리 언어의 ST_DISTANCE
description: Azure Cosmos DB의 SQL 시스템 함수 ST_DISTANCE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1c55bac14b3379f29d57bbad36026749089ec0fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349404"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 두 GeoJSON Point, Polygon 또는 LineString 식 사이의 거리를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  거리가 포함된 숫자 식을 반환합니다. 기본 참조 시스템의 경우 미터 단위로 표현됩니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 `ST_DISTANCE` 기본 제공 함수를 사용 하 여 지정 된 위치에서 30km 이내에 있는 모든 패밀리 문서를 반환 하는 방법을 보여 줍니다. 을 선택합니다.  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```  

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure Cosmos DB](sql-query-spatial-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
