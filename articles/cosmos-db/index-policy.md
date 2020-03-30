---
title: Azure Cosmos DB 인덱싱 정책
description: Azure Cosmos DB에서 자동 인덱싱 및 성능 향상을 위한 기본 인덱싱 정책을 구성하고 변경하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292049"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책

Azure Cosmos DB에서 모든 컨테이너에는 컨테이너의 항목을 인덱싱하는 방법을 지정하는 인덱싱 정책이 있습니다. 새로 만든 컨테이너에 대한 기본 인덱싱 정책은 모든 항목의 모든 속성을 인덱싱하고, 모든 문자열 또는 숫자에 대한 범위 인덱스를 적용하고, Point 형식의 GeoJSON 개체에 대한 공간 인덱스를 적용합니다. 이렇게 하면 인덱싱 및 인덱스 관리를 미리 생각할 필요 없이 높은 쿼리 성능을 얻을 수 있습니다.

사용자 요구 사항에 맞게 이 자동 동작을 재정의할 수 있는 상황이 있습니다. *인덱싱 모드를*설정하여 컨테이너의 인덱싱 정책을 사용자 지정하고 속성 경로를 포함하거나 제외할 수 *있습니다.*

> [!NOTE]
> 이 문서에서 설명하는 인덱싱 정책을 업데이트하는 방법은 Azure Cosmos DB의 SQL(코어) API에만 적용됩니다.

## <a name="indexing-mode"></a>인덱싱 모드

Azure 코스모스 DB는 두 가지 인덱싱 모드를 지원합니다.

- **일관된**: 항목을 만들거나 업데이트하거나 삭제할 때 인덱스가 동기적으로 업데이트됩니다. 즉, 읽기 쿼리의 일관성은 계정에 [대해 구성된 일관성이](consistency-levels.md)됩니다.
- **없음**: 컨테이너에서 인덱싱이 비활성화됩니다. 이 컨테이너는 보조 인덱스없이 순수 키 값 저장소로 사용되는 경우 일반적으로 사용됩니다. 또한 대량 작업의 성능을 향상시키는 데 사용할 수도 있습니다. 대량 작업이 완료되면 인덱스 모드를 일관으로 설정한 다음 완료될 때까지 [IndexTransformProgress를](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) 사용하여 모니터링할 수 있습니다.

> [!NOTE]
> Azure 코스모스 DB는 지연 인덱싱 모드도 지원합니다. 지연 인덱싱은 엔진이 다른 작업을 수행하지 않을 때 훨씬 낮은 우선 순위 수준에서 인덱스에 대한 업데이트를 수행합니다. 이로 인해 **일관되지 않거나 불완전한** 쿼리 결과가 발생할 수 있습니다. Cosmos 컨테이너를 쿼리하려는 경우 지연 인덱싱을 선택해서는 안 됩니다.

기본적으로 인덱싱 정책은 `automatic`로 설정됩니다. 인덱싱 정책의 `automatic` 속성을 `true`으로 설정하여 달성됩니다. 이 속성을 `true` 설정하면 Azure CosmosDB가 기록될 때 문서를 자동으로 인덱싱할 수 있습니다.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>속성 경로 포함 및 제외

사용자 지정 인덱싱 정책은 명시적으로 포함되거나 인덱싱에서 제외된 속성 경로를 지정할 수 있습니다. 인덱싱되는 경로 수를 최적화하여 컨테이너에서 사용하는 저장소 의 양을 낮추고 쓰기 작업의 대기 시간을 개선할 수 있습니다. 이러한 경로는 [인덱싱 개요 섹션에 설명된 메서드](index-overview.md#from-trees-to-property-paths) 다음에 정의되며 다음과 같이 추가됩니다.

- 스칼라 값(문자열 또는 숫자)으로 이어지는 경로는`/?`
- 배열의 요소는 `/[]` 표기법의 일부를 통해 함께 `/0`처리됩니다(대신 . `/1`
- 와일드카드를 `/*` 사용하여 노드 아래의 모든 요소를 일치시킬 수 있습니다.

같은 예제를 다시 선택합니다.

```
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
```

- `headquarters`의 `employees` 경로는`/headquarters/employees/?`

- `locations`'경로는 `country``/locations/[]/country/?`

- 아래 `headquarters` 아무것도에 경로는`/headquarters/*`

예를 들어 경로를 포함할 수 있습니다. `/headquarters/employees/?` 이 경로는 직원 속성을 인덱싱하지만 이 속성 내에 중첩된 JSON을 추가로 인덱싱하지 않도록 합니다.

## <a name="includeexclude-strategy"></a>전략 포함/제외

모든 인덱싱 정책에는 루트 `/*` 경로를 포함 또는 제외 된 경로로 포함 해야 합니다.

- 인덱싱할 필요가 없는 경로를 선택적으로 제외하려면 루트 경로를 포함합니다. Azure Cosmos DB를 사용하면 모델에 추가될 수 있는 새 속성을 사전에 인덱싱할 수 있으므로 권장되는 방법입니다.
- 루트 경로를 선택적으로 포함하도록 제외하여 인덱싱해야 하는 경로를 포함합니다.

- [숫자] 문자와 _(밑줄)가 포함된 일반 문자가 있는 패스의 경우 큰따옴표(예: "/path/?")를 중심으로 경로 문자열을 이스케이프할 필요가 없습니다. 다른 특수 문자가 있는 패스의 경우 큰따옴표 주위의 패스\"문자열을\"이스케이프해야 합니다(예: "/ path-abc /?"). 당신이 당신의 경로에 특별한 문자를 기대하는 경우, 당신은 안전을위해 모든 경로를 탈출 할 수 있습니다. 기능적으로 모든 경로를 이스케이프하면 특수 문자가있는 경로 대 만 차이가 없습니다.

- etag가 `_etag` 인덱싱을 위해 포함된 경로에 추가되지 않는 한 시스템 속성은 기본적으로 인덱싱에서 제외됩니다.

- 인덱싱 모드가 **일관되게**설정된 경우 `id` `_ts` 시스템 속성이 자동으로 인덱싱됩니다.

경로를 포함및 제외할 때 다음과 같은 특성이 발생할 수 있습니다.

- `kind`중 `range` 하나일 `hash`수 있습니다. 범위 인덱스 기능은 해시 인덱스의 모든 기능을 제공하므로 범위 인덱스를 사용하는 것이 좋습니다.

- `precision`는 포함된 패스에 대해 인덱스 수준에서 정의된 숫자입니다. 값은 `-1` 최대 정밀도를 나타냅니다. 항상 이 값을 `-1`로 설정하는 것이 좋습니다.

- `dataType`중 `String` 하나일 `Number`수 있습니다. 이는 인덱싱될 JSON 속성의 유형을 나타냅니다.

지정하지 않은 경우 이러한 속성에는 다음과 같은 기본값이 있습니다.

| **속성 이름**     | **기본값** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 및 `Number` |

경로를 포함 및 제외하는 정책 예제를 인덱싱하려면 [이 섹션을](how-to-manage-indexing-policy.md#indexing-policy-examples) 참조하십시오.

## <a name="spatial-indexes"></a>공간 인덱스

인덱싱 정책에서 공간 경로를 정의할 때 해당 ```type``` 경로에 적용할 인덱스를 정의해야 합니다. 공간 인덱스에 대 한 가능한 유형은 다음과 같습니다.

* Point

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB는 기본적으로 공간 인덱스를 만들지 않습니다. 공간 SQL 기본 제공 함수를 사용하려면 필요한 속성에 공간 인덱스를 만들어야 합니다. 공간 인덱스를 추가하기 위한 정책 예제를 인덱싱하려면 [이 섹션을](geospatial.md) 참조하십시오.

## <a name="composite-indexes"></a>복합 인덱스

둘 이상의 속성이 있는 절이 `ORDER BY` 있는 쿼리에는 복합 인덱스가 필요합니다. 복합 인덱스를 정의하여 많은 같음 및 범위 쿼리의 성능을 향상시킬 수도 있습니다. 기본적으로 복합 인덱스가 정의되지 않으므로 필요에 따라 [복합 인덱스를 추가해야](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) 합니다.

복합 인덱스를 정의할 때 다음을 지정합니다.

- 두 개 이상의 속성 경로입니다. 속성 경로가 정의된 시퀀스입니다.

- 순서(오름차순 또는 내림차순)입니다.

> [!NOTE]
> 복합 인덱스를 추가하면 새 복합 인덱스 추가가 완료될 때까지 쿼리는 기존 범위 인덱스를 활용합니다. 따라서 복합 인덱스를 추가하면 성능 이 향상되는 것을 즉시 관찰하지 못할 수 있습니다. [SDK 중 하나를 사용하여](how-to-manage-indexing-policy.md)인덱스 변환 진행 률을 추적할 수 있습니다.

### <a name="order-by-queries-on-multiple-properties"></a>여러 속성에 대한 쿼리별 순서:

두 개 이상의 속성이 있는 `ORDER BY` 절이 있는 쿼리에 복합 인덱스를 사용할 때 다음 고려 사항이 사용됩니다.

- 복합 인덱스 `ORDER BY` 경로가 절의 속성 시퀀스와 일치하지 않으면 복합 인덱스가 쿼리를 지원할 수 없습니다.

- 복합 인덱스 경로(오름차순 또는 내림차순)의 `ORDER BY` 순서도 절의 순서와 `order` 일치해야 합니다.

- 복합 인덱스는 모든 `ORDER BY` 경로에서 반대 순서를 가진 절도 지원합니다.

속성 이름, 연령 및 _ts 복합 인덱스가 정의되는 다음 예제를 고려합니다.

| **복합 인덱스**     | **샘플 `ORDER BY` 쿼리**      | **복합 인덱스에 의해 지원?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

필요한 `ORDER BY` 모든 쿼리를 제공할 수 있도록 인덱싱 정책을 사용자 지정해야 합니다.

### <a name="queries-with-filters-on-multiple-properties"></a>여러 속성에 대한 필터가 있는 쿼리

쿼리에 둘 이상의 속성에 대한 필터가 있는 경우 이러한 속성에 대한 복합 인덱스를 만드는 것이 유용할 수 있습니다.

예를 들어 두 속성에 같음 필터가 있는 다음 쿼리를 고려하십시오.

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

이 쿼리는 복합 인덱스(이름 ASC, age ASC)를 활용할 수 있는 경우 시간이 적게 걸리고 RU를 덜 소비하는 것이 더 효율적입니다.

범위 필터가 있는 쿼리는 복합 인덱스로 최적화할 수도 있습니다. 그러나 쿼리에는 단일 범위 필터만 있을 수 있습니다. 범위 필터에는 `>` `<`" `<=` `>=`및 `!=`. 범위 필터는 복합 인덱스에서 마지막으로 정의되어야 합니다.

같음 및 범위 필터를 모두 사용할 수 있는 다음 쿼리를 고려하십시오.

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

이 쿼리는 복합 인덱스(이름 ASC, age ASC)를 사용하면 더 효율적입니다. 그러나 같음 필터를 복합 인덱스에서 먼저 정의해야 하기 때문에 쿼리는 에 복합 인덱스(age ASC, name ASC)를 사용하지 않습니다.

여러 속성에 필터가 있는 쿼리에 대한 복합 인덱스를 만들 때 다음 고려 사항이 사용됩니다.

- 쿼리 필터의 속성은 복합 인덱스의 속성과 일치해야 합니다. 속성이 복합 인덱스에 있지만 쿼리에 필터로 포함되지 않은 경우 쿼리는 복합 인덱스를 사용하지 않습니다.
- 쿼리에 복합 인덱스에 정의되지 않은 추가 속성이 있는 경우 복합 인덱스와 범위 인덱스의 조합이 쿼리를 평가하는 데 사용됩니다. 이렇게 하려면 범위 인덱스를 단독으로 사용하는 것보다 RU수가 줄어듭니다.
- 속성에 범위`>`필터 (, `<` `<=`? `>=`또는) `!=`있는 경우 이 속성은 복합 인덱스에서 마지막으로 정의되어야 합니다. 쿼리에 두 개 이상의 범위 필터가 있는 경우 복합 인덱스를 사용하지 않습니다.
- 여러 필터를 사용하여 쿼리를 최적화하기 위해 `ORDER` 복합 인덱스를 만들 때 복합 인덱스의 결과는 영향을 미치지 않습니다. 이 속성은 선택 사항입니다.
- 여러 속성에 대한 필터가 있는 쿼리에 대한 복합 인덱스를 정의하지 않으면 쿼리는 계속 성공합니다. 그러나 복합 인덱스를 사용하면 쿼리의 RU 비용을 줄일 수 있습니다.

속성 이름, 나이 및 타임스탬프에 복합 인덱스가 정의된 다음 예제를 고려합니다.

| **복합 인덱스**     | **샘플 쿼리**      | **복합 인덱스에 의해 지원?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>필터 및 ORDER BY 절이 포함된 쿼리

쿼리가 하나 이상의 속성을 필터링하고 ORDER BY 절에 서로 다른 속성을 사용하는 경우 필터의 속성을 `ORDER BY` 절에 추가하는 것이 유용할 수 있습니다.

예를 들어 필터의 속성을 ORDER BY 절에 추가하여 복합 인덱스를 활용하기 위해 다음 쿼리를 다시 작성할 수 있습니다.

범위 인덱스를 사용하여 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

복합 인덱스를 사용한 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

여러 같음 필터가 있는 쿼리에 대해 동일한 패턴 및 쿼리 최적화를 일반화할 수 있습니다.

범위 인덱스를 사용하여 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

복합 인덱스를 사용한 쿼리:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

다음 고려 사항은 복합 인덱스를 만들어 필터 및 `ORDER BY` 절을 사용하여 쿼리를 최적화할 때 사용됩니다.

* 쿼리가 속성을 필터링하는 경우 이러한 쿼리를 `ORDER BY` 절에 먼저 포함해야 합니다.
* 한 속성에 필터가 있고 다른 속성을 사용하는 별도의 `ORDER BY` 절이 있는 쿼리에서 복합 인덱스를 정의하지 않으면 쿼리는 계속 성공합니다. 그러나 특히 `ORDER BY` 절의 속성에 카디널리티가 높은 경우 복합 인덱스를 사용 하 고 쿼리의 RU 비용을 줄일 수 있습니다.
* 여러 속성이 있는 쿼리와 `ORDER BY` 여러 속성에 대한 필터가 있는 쿼리에 대한 복합 인덱스를 만드는 모든 고려 사항은 여전히 적용됩니다.


| **복합 인덱스**                      | **샘플 `ORDER BY` 쿼리**                                  | **복합 인덱스에 의해 지원?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>인덱싱 정책 수정

컨테이너의 인덱싱 정책은 Azure 포털 [또는 지원되는 SDK 중 하나를 사용하여](how-to-manage-indexing-policy.md)언제든지 업데이트할 수 있습니다. 인덱싱 정책에 대한 업데이트는 이전 인덱스에서 새 인덱스로 변환을 트리거하며, 이 인덱스는 온라인 및 장소에서 수행됩니다(작업 중에 추가 저장소 공간이 사용되지 않도록). 이전 정책의 인덱스는 쓰기 가용성 이나 컨테이너에 프로비전 된 처리량에 영향을 주지 않고 새 정책으로 효율적으로 변환 됩니다. 인덱스 변환은 비동기 작업이며 완료하는 데 걸리는 시간은 프로비저닝된 처리량, 항목 수 및 크기에 따라 다릅니다.

> [!NOTE]
> 범위 또는 공간 인덱스를 추가하는 동안 쿼리는 일치하는 모든 결과를 반환하지 않을 수 있으며 오류를 반환하지 않고 반환합니다. 즉, 인덱스 변환이 완료될 때까지 쿼리 결과가 일관되지 않을 수 있습니다. [SDK 중 하나를 사용하여](how-to-manage-indexing-policy.md)인덱스 변환 진행 률을 추적할 수 있습니다.

새 인덱싱 정책의 모드가 일관으로 설정된 경우 인덱스 변환이 진행되는 동안다른 인덱싱 정책 변경을 적용할 수 없습니다. 인덱싱 정책의 모드를 없음으로 설정하여 실행 중인 인덱스 변환을 취소할 수 있습니다(인덱스가 즉시 삭제됩니다).

## <a name="indexing-policies-and-ttl"></a>인덱싱 정책 및 TTL

[TTL(Time-to-Live) 기능은](time-to-live.md) 켜져 있는 컨테이너에서 인덱싱을 활성화해야 합니다. 이는 다음을 의미합니다.

- 인덱싱 모드가 없음으로 설정된 컨테이너에서는 TTL을 활성화할 수 없습니다.
- TTL이 활성화된 컨테이너에서는 인덱싱 모드를 없음으로 설정할 수 없습니다.

속성 경로를 인덱싱할 필요가 없지만 TTL이 필요한 시나리오의 경우 다음을 사용하여 인덱싱 정책을 사용할 수 있습니다.

- 일관되게 설정된 인덱싱 모드와
- 포함 경로가 없으며
- `/*`유일한 제외 된 경로로.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
