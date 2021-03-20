---
title: Azure Cosmos DB 쿼리 언어의 ST_WITHIN
description: Azure Cosmos DB의 SQL 시스템 함수 ST_WITHIN에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9372da349d0ea9169bb59570b7e6dd0e597d1cdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558229"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 첫 번째 인수에 지정 된 GeoJSON 개체 (Point, Polygon, MultiPolygon 또는 LineString)가 두 번째 인수의 GeoJSON (Point, Polygon, MultiPolygon 또는 LineString) 내에 있는지 여부를 나타내는 부울 식을 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 값을 반환합니다.  
  
## <a name="examples"></a>예제
  
  다음 예제에서는를 사용 하 여 다각형 내에서 모든 패밀리 문서를 찾는 방법을 보여 줍니다 `ST_WITHIN` .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 집계가 있는 쿼리를 제외 하 고 [지리 공간적 인덱스](index-policy.md#spatial-indexes) 를 활용 합니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure Cosmos DB](sql-query-spatial-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
