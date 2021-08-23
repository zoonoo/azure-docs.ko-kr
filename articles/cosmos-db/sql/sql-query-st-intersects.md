---
title: Azure Cosmos DB 쿼리 언어의 ST_INTERSECTS
description: Azure Cosmos DB의 SQL 시스템 함수 ST_INTERSECTS에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8529eae920fdef90c5dd36fc39f0e99635478adf
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567642"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 첫 번째 인수에 지정된 GeoJSON 개체(Point, Polygon, MultiPolygon 또는 LineString)가 두 번째 인수의 GeoJSON(Point, Polygon, MultiPolygon 또는 LineString)과 교차하는지 여부를 나타내는 부울 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 값을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 지정된 다각형과 교차하는 모든 영역을 찾는 방법을 보여줍니다.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 집계가 있는 쿼리를 제외하고 [지리 공간적 인덱스](../index-policy.md#spatial-indexes)의 이점을 제공합니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure Cosmos DB](sql-query-spatial-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
