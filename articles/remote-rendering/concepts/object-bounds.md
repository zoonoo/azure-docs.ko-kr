---
title: 개체 경계
description: 공간 개체 경계를 쿼리 하는 방법에 대해 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681715"
---
# <a name="object-bounds"></a>개체 경계

개체 범위는 [엔터티와](entities.md) 해당 자식이 차지 하는 볼륨을 나타냅니다. Azure 원격 렌더링에서 개체 범위는 항상 AABB ( *축 맞춤 경계 상자* )로 제공 됩니다. 개체 범위는 *로컬 공간* 에 있거나 *세계 공간*에 있을 수 있습니다. 어느 쪽이 든 항상 축에 정렬 됩니다. 즉, 범위와 볼륨이 로컬 및 세계 공간 표현 사이에서 다를 수 있습니다.

## <a name="querying-object-bounds"></a>개체 범위 쿼리

메시의 로컬 AABB는 메시 리소스에서 직접 쿼리할 [수 있습니다.](meshes.md) 이러한 범위는 엔터티의 변형을 사용 하 여 엔터티의 지역 공간 또는 세계 공간으로 변환할 수 있습니다.

이러한 방식으로 전체 개체 계층의 경계를 계산할 수 있지만 계층 구조를 트래버스 하 고 각 메시의 범위를 쿼리 한 다음 수동으로 결합 해야 합니다. 이 작업은 번거롭고 비효율적입니다.

엔터티를 호출 `QueryLocalBoundsAsync` `QueryWorldBoundsAsync` 하는 것이 더 나은 방법입니다. 그런 다음 계산이 서버에 오프 로드 되 고 최소 지연 시간으로 반환 됩니다.

``` cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

## <a name="next-steps"></a>다음 단계

* [공간 쿼리](../overview/features/spatial-queries.md)
