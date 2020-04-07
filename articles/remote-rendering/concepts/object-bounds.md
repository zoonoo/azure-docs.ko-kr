---
title: 개체 경계
description: 공간 개체 경계를 쿼리하는 방법 설명
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681715"
---
# <a name="object-bounds"></a>개체 경계

개체 경계는 [엔터티와](entities.md) 해당 자식이 차지하는 볼륨을 나타냅니다. Azure 원격 렌더링에서 개체 경계는 항상 축 정렬 된 경계 상자 (AABB)로 지정 *됩니다.* 오브젝트 경계는 로컬 *공간또는* *월드 스페이스에*있을 수 있습니다. 어느 쪽이든 항상 축 정렬되어 있어 범위와 체적이 로컬 공간 표현과 월드 공간 표현 간에 다를 수 있습니다.

## <a name="querying-object-bounds"></a>개체 경계 쿼리

[메시의](meshes.md) 로컬 AABB는 메시 리소스에서 직접 쿼리할 수 있습니다. 이러한 경계는 엔터티의 변환을 사용하여 엔터티의 로컬 공간 또는 엔터티의 월드 공간으로 변환할 수 있습니다.

이러한 방식으로 전체 개체 계층 구조의 경계를 계산할 수 있지만 계층을 통과하고 각 메시의 경계를 쿼리하고 수동으로 결합해야 합니다. 이 작업은 지루하고 비효율적입니다.

더 좋은 방법은 `QueryLocalBoundsAsync` 엔터티를 호출하거나 `QueryWorldBoundsAsync` 호출하는 것입니다. 그런 다음 계산이 서버로 오프로드되고 최소한의 지연으로 반환됩니다.

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
