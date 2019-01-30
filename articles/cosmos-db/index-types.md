---
title: Azure Cosmos DB의 인덱스 유형
description: Azure Cosmos DB의 인덱스 유형 개요
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: 50e8e63c9508aa9e81222f242ca330637075e42d
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199071"
---
# <a name="index-types-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱스 유형

경로에 대한 인덱싱 정책을 구성할 때는 몇 가지 옵션을 사용할 수 있습니다. 모든 경로에 대해 하나 이상의 인덱싱 정의를 지정할 수 있습니다.

- **데이터 형식:** 문자열, 숫자, 점, 다각형 또는 LineString(경로별로 데이터 형식당 하나만 포함할 수 있음).

- **인덱스 종류:** 해시(같음 쿼리), 범위(같음, 범위 또는 Order By 쿼리) 또는 공간(공간 쿼리).

- **전체 자릿수:** 해시 인덱스의 경우 문자열 및 숫자 둘 다에서 1~8 범위로 다양하며, 기본값은 3입니다. 범위 인덱스의 경우 최대 전체 자릿수 값은 -1입니다. 문자열 또는 숫자 값의 경우 1에서 100(최대 전체 자릿수)까지 다양할 수 있습니다.

## <a name="index-kind"></a>인덱스 종류

Azure Cosmos DB는 모든 경로에 대해 문자열, 숫자 데이터 형식 또는 둘 다로 구성할 수 있는 해시 및 범위 인덱스를 지원합니다.

- **해시 인덱스**는 효율적인 같음 및 JOIN 쿼리를 지원합니다. 대부분의 사용 사례에서는 해시 인덱스의 전체 자릿수가 기본값 3바이트보다 높을 필요가 없습니다. 데이터 형식은 문자열 또는 숫자일 수 있습니다.

  > [!NOTE]
  > Azure Cosmos 컨테이너는 해시 인덱스 종류를 더 이상 사용하지 않는 새로운 인덱스 레이아웃을 지원합니다. 인덱싱 정책에서 해시 인덱스 종류를 지정하면 컨테이너에 대한 CRUD 요청에서는 인덱스 종류를 자동으로 무시하며 컨테이너의 응답에는 범위 인덱스 종류만 포함됩니다. 새 Cosmos 컨테이너는 모두 기본적으로 새 인덱스 레이아웃을 사용합니다. 
  
- **범위 인덱스**는 효율적인 같음 쿼리, 범위 쿼리(>, <, >=, <=, != 사용) 및 ORDER BY 쿼리를 지원합니다. 또한 ORDER BY 쿼리에는 기본적으로 최대 인덱스 전체 자릿수(-1)가 필요합니다. 데이터 형식은 문자열 또는 숫자일 수 있습니다.

- **공간 인덱스**는 효율적인 공간(이내 및 거리) 쿼리를 지원합니다. 데이터 형식은 점, 다각형 또는 LineString일 수 있습니다. Azure Cosmos DB는 모든 경로에 대해 점, 다각형 또는 LineString 데이터 형식으로 지정할 수 있는 공간 인덱스 종류도 지원합니다. 지정된 경로에 있는 값은 다음과 같은 유효한 GeoJSON 조각이어야 합니다. {"type": "Point", "coordinates": [0.0, 10.0]}. Azure Cosmos DB는 점, 다각형 및 LineString 데이터 형식의 자동 인덱싱을 지원합니다.

다음은 해시, 범위, 공간 인덱스를 사용하여 처리할 수 있는 쿼리의 예입니다.

| **인덱스 종류** | **설명/사용 사례** |
| ---------- | ---------------- |
| 해시  | Hash over/prop/? (또는 /)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (또는 /나 /props/)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| 범위  | /prop/? (또는 /)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop   |
| 공간     | /prop/? (또는 /)는 다음 쿼리를 효율적으로 처리하는 데 사용할 수 있습니다.<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --with indexing on points enabled<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --with indexing on polygons enabled.     |

## <a name="default-behavior-of-index-kinds"></a>인덱스 종류의 기본 동작

- 쿼리를 처리하는 데 검사가 필요할 수 있음을 알리기 위한 범위 인덱스(임의의 전체 자릿수)가 없는 경우 >=와 같은 범위 연산자를 사용하는 쿼리에 대해 오류가 반환됩니다.

- REST API의 “x-ms-documentdb-enable-scan” 헤더를 사용하거나 .NET SDK의 “EnableScanInQuery” 요청 옵션을 사용하여 범위 인덱스 없이 범위 쿼리를 수행할 수 있습니다. 쿼리에 다른 필터가 있어서 Azure Cosmos DB가 인덱스를 사용하여 필터링할 수 있는 경우 오류가 반환되지 않습니다.

- 기본적으로 공간 인덱스가 없고 인덱스에서 제공될 수 있는 다른 필터가 없는 경우 공간 쿼리에 대해 오류가 반환됩니다. 이러한 쿼리는 x-ms-documentdb-enable-scan 또는 EnableScanInQuery를 사용하여 검사로서 수행할 수 있습니다.

## <a name="index-precision"></a>인덱스 전체 자릿수

- 인덱스 전체 자릿수를 사용하면 인덱스 스토리지 오버헤드와 쿼리 성능 간에 적절한 균형을 유지할 수 있습니다. 숫자의 경우 기본 전체 자릿수 구성인 -1(최대값)을 사용하는 것이 좋습니다. 숫자는 JSON에서 8바이트이므로 8바이트 구성과 같습니다. 1-7과 같이 낮은 전체 자릿수 값을 선택하면 일부 범위 내의 값이 같은 인덱스 항목에 매핑됩니다. 따라서 인덱스 저장 공간을 줄일 수 있지만 쿼리 실행 시 더 많은 항목을 처리해야 할 수 있습니다. 그 결과 RU당 처리량이 더 많이 사용됩니다.

- 인덱스 전체 자릿수는 문자열 범위와 함께 실질적으로 더 유용한 애플리케이션을 제공합니다. 문자열은 임의의 길이일 수 있으므로 인덱스 전체 자릿수 선택은 문자열 범위 쿼리의 성능에 영향을 줄 수 있으며 필요한 인덱스 저장소 공간의 양에도 영향을 줄 수 있습니다. 문자열 범위 인덱스는 1~100 사이 또는 -1(최댓값)의 인덱스 전체 자릿수로 구성할 수 있습니다. 문자열 속성에 대한 ORDER BY 쿼리를 수행하려는 경우, 해당 경로에 대해 -1의 전체 자릿수를 지정해야 합니다.

- 공간 인덱스는 항상 모든 형식(점, LineString 및 다각형)에 대한 기본 인덱스 전체 자릿수를 사용하며, 공간 인덱스의 기본 인덱스 전체 자릿수는 재정의할 수 없습니다.

Azure Cosmos DB는 쿼리가 ORDER BY를 사용하지만 최대 전체 자릿수로 쿼리된 경로에 대한 범위 인덱스가 없는 경우 오류를 반환합니다.

> [!NOTE]
> Azure Cosmos 컨테이너는 최대 전체 자릿수 값(-1) 이외의 사용자 지정 인덱스 전체 자릿수가 더 이상 필요하지 않은 새로운 인덱스 레이아웃을 지원합니다. 이 방법을 사용하는 경우에는 경로가 항상 최대 전체 자릿수를 사용하여 인덱싱됩니다. 인덱싱 정책에서 전체 자릿수 값을 지정하면 컨테이너에 대한 CRUD 요청에서는 전체 자릿수 값을 자동으로 무시하며 컨테이너의 응답에는 최대 전체 자릿수 값(-1)만 포함됩니다.  새 Cosmos 컨테이너는 모두 기본적으로 새 인덱스 레이아웃을 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB의 인덱싱에 대한 자세한 내용은 다음 문서를 참조하세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책](indexing-policies.md)
- [인덱스 경로](index-paths.md)

