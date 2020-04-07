---
title: 공간 쿼리
description: 장면에서 공간 쿼리를 수행하는 방법
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680532"
---
# <a name="spatial-queries"></a>공간 쿼리

공간 쿼리는 원격 렌더링 서비스에 해당 영역에 있는 개체를 요청할 수 있는 작업입니다. 공간 쿼리는 사용자가 가리키는 개체를 파악하는 것과 같은 상호 작용을 구현하는 데 자주 사용됩니다.

모든 공간 쿼리는 서버에서 평가됩니다. 따라서 비동기 작업이며 결과는 네트워크 대기 시간에 따라 지연됩니다. 모든 공간 쿼리는 네트워크 트래픽을 생성하므로 한 번에 너무 많은 작업을 수행하지 않도록 주의해야 합니다.

## <a name="collision-meshes"></a>충돌 메시

공간 쿼리는 [Havok Physics](https://www.havok.com/products/havok-physics) 엔진에 의해 구동되며 전용 충돌 메시가 있어야 합니다. 기본적으로 [모델 변환은](../../how-tos/conversion/model-conversion.md) 충돌 메시들을 생성합니다. 복잡한 모델에 공간 쿼리가 필요하지 않은 경우 [변환 옵션에서](../../how-tos/conversion/configure-model-conversion.md)충돌 메시 생성을 사용하지 않도록 설정하는 것이 여러 가지 방법으로 영향을 미치므로 고려하십시오.

* [모델 변환은](../../how-tos/conversion/model-conversion.md) 상당히 오래 걸릴 수 있습니다.
* 변환된 모델 파일 크기는 눈에 띄게 커져 다운로드 속도에 영향을 미칩니다.
* 런타임 로딩 시간이 더 깁니다.
* 런타임 CPU 메모리 소비량이 더 높습니다.
* 모든 모델 인스턴스에 대해 약간의 런타임 성능 오버헤드가 있습니다.

## <a name="ray-casts"></a>레이 캐스트

*광선 캐스트는* 런타임이 지정된 위치에서 시작하여 특정 방향을 가리키는 광선으로 교차하는 객체를 확인하는 공간 쿼리입니다. 최적화로 너무 멀리 있는 객체를 검색하지 않도록 최대 광선 거리도 제공됩니다.

````c#
async void CastRay(AzureSession session)
{
    // trace a line from the origin into the +z direction, over 10 units of distance.
    RayCast rayCast = new RayCast(new Double3(0, 0, 0), new Double3(0, 0, 1), 10);

    // only return the closest hit
    rayCast.HitCollection = HitCollectionPolicy.ClosestHit;

    RayCastHit[] hits = await session.Actions.RayCastQueryAsync(rayCast).AsTask();

    if (hits.Length > 0)
    {
        var hitObject = hits[0].HitEntity;
        var hitPosition = hits[0].HitPosition;
        var hitNormal = hits[0].HitNormal;

        // do something with the hit information
    }
}
````

히트 컬렉션 모드는 세 가지가 있습니다.

* **가장 가까운:** 이 모드에서는 가장 가까운 적중만 보고됩니다.
* **모든:** 당신이 알고 싶은 모든 광선이 아무것도 명중 할 *것인지 여부이지만,* 정확히 명중 무엇을 상관하지 않을 때이 모드를 선호합니다. 이 쿼리는 평가하는 데 상당히 저렴할 수 있지만 응용 프로그램도 거의 없습니다.
* **모두:** 이 모드에서는 광선을 따라 모든 조회가 보고되고 거리별로 정렬됩니다. 당신이 정말로 첫 번째 히트보다 더 많은 것을 필요로하지 않는 한이 모드를 사용하지 마십시오. `MaxHits` 옵션으로 보고된 적중 횟수를 제한합니다.

광선 캐스트에 대해 선택적으로 고려되는 개체를 제외하려면 [계층적StateOvercomponent](override-hierarchical-state.md) 구성 요소를 사용할 수 있습니다.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>적중 결과

광선 캐스트 쿼리의 결과는 조회 의 배열입니다. 개체가 닿지 않은 경우 배열이 비어 있습니다.

히트에는 다음과 같은 속성이 있습니다.

* **히트 엔티티:** 어떤 [엔터티가](../../concepts/entities.md) 적중되었습니다.
* **하위 파트ID:** [메시 구성 요소에서](../../concepts/meshes.md)적중된 하위 *메시입니다.* 해당 지점에서 `MeshComponent.UsedMaterials` [재질을](../../concepts/materials.md) 인덱싱하고 조회하는 데 사용할 수 있습니다.
* **히트 포지션:** 광선이 오브젝트와 교차하는 세계 공간 위치입니다.
* **히트 노멀:** 교차 위치에서 메시의 표준 공간 표면 법선입니다.
* **거리토히트:** 광선 시작 위치에서 적중까지의 거리입니다.

## <a name="next-steps"></a>다음 단계

* [개체 경계](../../concepts/object-bounds.md)
* [계층 상태 재정의](override-hierarchical-state.md)
