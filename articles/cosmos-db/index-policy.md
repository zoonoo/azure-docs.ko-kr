---
title: Azure Cosmos DB 인덱싱 정책
description: Azure Cosmos DB의 자동 인덱싱 및 성능 향상을 위해 기본 인덱싱 정책을 구성 하 고 변경 하는 방법에 대해 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467872"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책

Azure Cosmos DB 모든 컨테이너에는 컨테이너의 항목을 인덱싱하는 방법을 지정 하는 인덱싱 정책이 있습니다. 새로 만든 컨테이너에 대 한 기본 인덱싱 정책은 모든 항목의 모든 속성을 인덱싱합니다. 모든 문자열 또는 숫자에 대 한 범위 인덱스를 적용 하 고 Point 형식의 GeoJSON 개체에 대 한 공간 인덱스를 적용 합니다. 이를 통해 인덱싱 및 인덱스 관리를 사전에 고려 하지 않고도 높은 쿼리 성능을 얻을 수 있습니다.

경우에 따라 사용자 요구 사항에 맞게이 자동 동작을 재정의 하는 것이 좋습니다. *인덱싱 모드*를 설정 하 여 컨테이너의 인덱싱 정책을 사용자 지정 하 고 *속성 경로*를 포함 하거나 제외할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명 하는 인덱싱 정책 업데이트 방법은 Azure Cosmos DB의 SQL (Core) API에만 적용 됩니다.

## <a name="indexing-mode"></a>인덱싱 모드

Azure Cosmos DB는 두 가지 인덱싱 모드를 지원 합니다.

- **일관성**: 컨테이너의 인덱싱 정책이 일관 되 게 설정 된 경우 항목을 만들거나 업데이트 하거나 삭제 하면 인덱스가 동기적으로 업데이트 됩니다. 즉, 읽기 쿼리의 일관성은 [계정에 대해 구성 된 일관성](consistency-levels.md)이 됩니다.

- **없음**: 컨테이너의 인덱싱 정책을 None으로 설정 하면 해당 컨테이너에서 인덱싱이 효과적으로 비활성화 됩니다. 이는 컨테이너를 보조 인덱스가 없어도 순수 키-값 저장소로 사용할 때 일반적으로 사용 됩니다. 대량 삽입 작업의 속도를 높일 수도 있습니다.

## <a name="including-and-excluding-property-paths"></a>속성 경로 포함 및 제외

사용자 지정 인덱싱 정책은 명시적으로 포함 되거나 인덱싱에서 제외 되는 속성 경로를 지정할 수 있습니다. 인덱싱되는 경로 수를 최적화 하 여 컨테이너에서 사용 되는 저장소의 양을 줄이고 쓰기 작업의 대기 시간을 높일 수 있습니다. 이러한 경로는 [인덱싱 개요 섹션에 설명 된 방법](index-overview.md#from-trees-to-property-paths) 에 따라 다음과 같이 추가 됩니다.

- 스칼라 값 (문자열 또는 숫자)으로 시작 하는 경로는 다음으로 끝납니다.`/?`
- 배열의 요소는 `/[]` 표기법 ( `/0`대신 `/1` )을 통해 함께 처리 됩니다.
- `/*` 와일드 카드를 사용 하 여 노드 아래의 모든 요소를 일치 시킬 수 있습니다.

동일한 예제를 다시 수행 합니다.

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- `headquarters` 의`employees` 경로는입니다.`/headquarters/employees/?`
- `locations` '`country` 경로는입니다.`/locations/[]/country/?`
- 아래의 `headquarters` 모든 항목에 대 한 경로는입니다.`/headquarters/*`

경로를 인덱싱 정책에 명시적으로 포함 하는 경우에는 해당 경로에 적용 해야 하는 인덱스 유형과 각 인덱스 유형에 대해이 인덱스가 적용 되는 데이터 형식을 정의 해야 합니다.

| 인덱스 유형 | 허용 되는 대상 데이터 형식 |
| --- | --- |
| 범위 | 문자열 또는 숫자 |
| 공간 | Point, LineString 또는 Polygon |

예를 들어 `/headquarters/employees/?` 경로를 포함 하 `String` 고 및 `Number` 값에 대 `Range` 한 인덱스를 해당 경로에 적용 하도록 지정할 수 있습니다.

### <a name="includeexclude-strategy"></a>포함/제외 전략

모든 인덱싱 정책은 루트 경로 `/*` 를 포함 된 경로 또는 제외 된 경로로 포함 해야 합니다.

- 인덱싱할 필요가 없는 경로를 선택적으로 제외 하려면 루트 경로를 포함 합니다. 이 방법은 모델에 추가할 수 있는 새 속성을 사전에 인덱싱할 Azure Cosmos DB 있도록 하는 권장 방법입니다.
- 인덱싱할 필요가 있는 경로를 선택적으로 포함 하려면 루트 경로를 제외 합니다.

- 영숫자 문자 및 _ (밑줄)를 포함 하는 일반 문자를 포함 하는 경로의 경우 큰따옴표를 기준으로 경로 문자열을 이스케이프할 필요가 없습니다 (예: "/path/?"). 다른 특수 문자를 포함 하는 경로의 경우 큰따옴표 앞뒤에 경로 문자열을 이스케이프 해야 합니다 (예: "/\"경로-abc\"/?"). 경로에 특수 문자가 필요한 경우 안전을 위해 모든 경로를 이스케이프할 수 있습니다. 모든 경로와 특수 문자를 포함 하는 모든 경로를 이스케이프 하는 경우에는 차이점이 없습니다.

- 인덱싱에 대 한 포함 된 경로에 etag를 추가 하지 않으면 기본적으로 시스템 속성 "etag"가 인덱싱에서 제외 됩니다.

인덱싱 정책 예제는 [이 섹션](how-to-manage-indexing-policy.md#indexing-policy-examples) 을 참조 하세요.

## <a name="composite-indexes"></a>복합 인덱스

두 개 `ORDER BY` 이상의 속성이 복합 인덱스를 요구 함을 쿼리 합니다. 현재 복합 인덱스는 다중 `ORDER BY` 쿼리에만 사용 됩니다. 기본적으로 복합 인덱스는 정의 되지 않으므로 필요에 따라 [복합 인덱스를 추가](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) 해야 합니다.

복합 인덱스를 정의 하는 경우 다음을 지정 합니다.

- 두 개 이상의 속성 경로입니다. 속성 경로가 정의 되는 순서입니다.
- 순서 (오름차순 또는 내림차순)입니다.

복합 인덱스를 사용 하는 경우 다음 사항을 고려해 야 합니다.

- 복합 인덱스 경로가 ORDER BY 절의 속성 시퀀스와 일치 하지 않는 경우 복합 인덱스는 쿼리를 지원할 수 없습니다.

- 복합 인덱스 경로 (오름차순 또는 내림차순)의 순서는 ORDER BY 절의 순서와도 일치 해야 합니다.

- 복합 인덱스는 모든 경로에서 순서가 반대인 ORDER BY 절도 지원 합니다.

복합 인덱스가 속성 a, b, c에 정의 되어 있는 다음 예제를 살펴보십시오.

| **복합 인덱스**     | **예제 `ORDER BY` 쿼리**      | **인덱스에서 지원 되나요?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

필요한 `ORDER BY` 모든 쿼리를 제공할 수 있도록 인덱싱 정책을 사용자 지정 해야 합니다.

## <a name="modifying-the-indexing-policy"></a>인덱싱 정책 수정

컨테이너의 인덱싱 정책은 [Azure Portal 또는 지원 되는 sdk 중 하나를 사용 하](how-to-manage-indexing-policy.md)여 언제 든 지 업데이트할 수 있습니다. 인덱싱 정책에 대 한 업데이트는 이전 인덱스에서 새 인덱스로의 변환을 트리거합니다 .이는 온라인 및 현재 상태에서 수행 됩니다. 즉, 작업 중에 추가 저장소 공간이 사용 되지 않습니다. 이전 정책의 인덱스는 컨테이너에 프로 비전 된 처리량 또는 쓰기 가용성에 영향을 주지 않고 새 정책으로 효율적으로 변환 됩니다. 인덱스 변환은 비동기 작업이 며 완료 하는 데 걸리는 시간은 프로 비전 된 처리량, 항목 수 및 크기에 따라 달라 집니다. 

> [!NOTE]
> 인덱스를 다시 처리 하는 동안 쿼리는 일치 하는 모든 결과를 반환 하지 않을 수 있으며 오류를 반환 하지 않고이를 수행 합니다. 즉, 인덱스 변환이 완료 될 때까지 쿼리 결과가 일관 되지 않을 수 있습니다. [Sdk 중 하나를 사용 하 여](how-to-manage-indexing-policy.md)인덱스 변환의 진행률을 추적할 수 있습니다.

새 인덱싱 정책 모드를 일관 되 게 설정 하면 인덱스 변환이 진행 되는 동안 다른 인덱싱 정책 변경 내용을 적용할 수 없습니다. 인덱싱 정책의 모드를 None으로 설정 하 여 실행 중인 인덱스 변환을 취소할 수 있습니다. 그러면 인덱스가 즉시 삭제 됩니다.

## <a name="indexing-policies-and-ttl"></a>인덱싱 정책 및 TTL

[Ttl (time-to-live) 기능](time-to-live.md) 을 사용 하려면 설정 된 컨테이너에서 인덱싱이 활성화 되어야 합니다. 이는 다음을 의미합니다.

- 인덱싱 모드가 None으로 설정 된 컨테이너에서는 TTL을 활성화할 수 없습니다.
- TTL이 활성화 된 컨테이너에서는 인덱싱 모드를 None으로 설정할 수 없습니다.

속성 경로를 인덱싱하지 않아도 되지만 TTL이 필요한 시나리오의 경우 다음과 같은 인덱싱 정책을 사용할 수 있습니다.

- 일관적인로 설정 된 인덱싱 모드
- 포함 된 경로 없음
- `/*`제외 된 유일한 경로입니다.

## <a name="obsolete-attributes"></a>사용 되지 않는 특성

인덱싱 정책으로 작업할 때 다음과 같은 특성을 사용 하지 않을 수 있습니다.

- `automatic`는 인덱싱 정책의 루트에 정의 된 부울입니다. 사용 중인 도구에 필요한 경우 이제 무시 되 `true`고로 설정할 수 있습니다.
- `precision`는 포함 된 경로에 대 한 인덱스 수준에서 정의 된 숫자입니다. 사용 중인 도구에 필요한 경우 이제 무시 되 `-1`고로 설정할 수 있습니다.
- `hash`는 이제 범위 종류로 대체 되는 인덱스 종류입니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
