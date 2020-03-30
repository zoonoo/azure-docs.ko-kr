---
title: Azure 코스모스 DB 쿼리 언어로 ST_WITHIN
description: Azure Cosmos DB에서 ST_WITHIN SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296119"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure 코스모스 DB)
 첫 번째 인수에 지정된 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 인수의 GeoJSON(Point, Polygon 또는 LineString) 내에 있는지 여부를 나타내는 부울 식을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*spatial_expr*  
   GeoJSON 점, 다각형 또는 LineString 개체 표현식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 값을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 을 사용하여 `ST_WITHIN`다각형 내에서 모든 패밀리 문서를 찾는 방법을 보여 주며 있습니다.  
  
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

이 시스템 함수는 [지리 공간 인덱스의](index-policy.md#spatial-indexes)이점을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [공간 함수 Azure 코스모스 DB](sql-query-spatial-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
