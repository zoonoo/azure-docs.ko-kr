---
title: Azure 코스모스 DB 쿼리 언어로 ST_DISTANCE
description: Azure Cosmos DB에서 ST_DISTANCE SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537298"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure 코스모스 DB)
 두 GeoJSON 점, 다각형, 다각형 또는 LineString 식 사이의 거리를 반환합니다. 자세한 내용은 지리 [공간 및 GeoJSON 위치 데이터](sql-query-geospatial-intro.md) 문서를 참조하십시오.
  
## <a name="syntax"></a>구문
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  거리가 포함된 숫자 식을 반환합니다. 기본 참조 시스템의 경우 미터 단위로 표현됩니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 기본 제공 함수를 사용하여 지정된 위치에서 30km `ST_DISTANCE` 이내에 있는 모든 패밀리 문서를 반환하는 방법을 보여 주었습니다. .  
  
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

## <a name="remarks"></a>설명

이 시스템 함수는 [지리 공간 인덱스의](index-policy.md#spatial-indexes)이점을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure 코스모스 DB](sql-query-spatial-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
