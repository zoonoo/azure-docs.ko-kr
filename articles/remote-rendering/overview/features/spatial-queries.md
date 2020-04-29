---
title: 공간 쿼리
description: 장면에서 공간 쿼리를 수행 하는 방법
author: jakrams
ms.author: jakras
ms.date: 02/07/2020
ms.topic: article
ms.openlocfilehash: 9a981aeb08ec46900994fd599b592b9f16034f34
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680532"
---
# <a name="spatial-queries"></a>공간 쿼리

공간 쿼리는 영역에 있는 개체를 원격 렌더링 서비스에 요청할 수 있는 작업입니다. 공간 쿼리는 사용자가 가리키는 개체를 파악 하는 등의 상호 작용을 구현 하는 데 자주 사용 됩니다.

모든 공간 쿼리가 서버에서 계산 됩니다. 결과적으로 이러한 작업은 비동기 작업이 며 네트워크 대기 시간에 따라 지연이 발생 하는 결과가 도착할 것입니다. 모든 공간 쿼리는 네트워크 트래픽을 생성 하므로 한 번에 너무 많은 작업을 수행 하지 않도록 주의 해야 합니다.

## <a name="collision-meshes"></a>충돌 망

공간 쿼리는 [Havok 물리학](https://www.havok.com/products/havok-physics) 엔진에 의해 구동 되며 전용 충돌 메쉬가 있어야 합니다. 기본적으로 [모델 변환은](../../how-tos/conversion/model-conversion.md) 충돌 망을 생성 합니다. 복잡 한 모델에 공간 쿼리가 필요 하지 않은 경우 여러 가지 방법으로 영향을 주므로 [변환 옵션](../../how-tos/conversion/configure-model-conversion.md)에서 충돌 망 생성을 사용 하지 않도록 설정 하는 것이 좋습니다.

* [모델 변환을](../../how-tos/conversion/model-conversion.md) 수행 하는 데 시간이 훨씬 오래 걸립니다.
* 변환 된 모델 파일 크기가 현저 하 게 커지고 다운로드 속도에 영향을 줍니다.
* 런타임 로드 시간이 더 깁니다.
* 런타임 CPU 메모리 소비가 높습니다.
* 모든 모델 인스턴스에 대해 약간의 런타임 성능 오버 헤드가 있습니다.

## <a name="ray-casts"></a>광선 캐스트

*광선 캐스트* 는 지정 된 위치에서 시작 하 여 특정 방향을 가리키는 런타임에서 광선이 교차 하는 개체를 확인 하는 공간 쿼리입니다. 최적화를 위해 너무 멀리 떨어져 있는 개체를 검색 하지 않도록 최대 광선 거리가 지정 됩니다.

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

다음과 같은 세 가지 적중 수집 모드가 있습니다.

* **가장 근접:** 이 모드에서는 가장 가까운 적중만 보고 됩니다.
* **Any:** 이 모드를 사용 하는 것이 더 중요 한 것은 빛이 무엇이 든 상관 *없이* 정확 하 게 적중 되었는지 걱정할 필요가 없다는 것입니다. 이 쿼리는 평가 하는 데 매우 저렴 하지만 응용 프로그램은 거의 없습니다.
* **모두:** 이 모드에서는 광선을 따라 모든 적중 횟수가 보고 되며 거리를 기준으로 정렬 됩니다. 첫 번째 적중 보다 더 많이 필요한 경우가 아니면이 모드를 사용 하지 마세요. `MaxHits` 옵션으로 보고 되는 적중 횟수를 제한 합니다.

광선 캐스팅에 대해 선택적으로 개체를 제외 하려면 [HierarchicalStateOverrideComponent](override-hierarchical-state.md) 구성 요소를 사용할 수 있습니다.

<!--
The CollisionMask allows the quey to consider or ignore some objects based on their collision layer. If an object has layer L, it will be hit only if the mask has  bit L set.
It is useful in case you want to ignore objects, for instance when setting an object transparent, and trying to select another object behind it.
TODO : Add an API to make that possible.
-->

### <a name="hit-result"></a>적중 결과

광선 캐스트 쿼리의 결과는 적중의 배열입니다. 개체가 적중 되지 않은 경우 배열이 비어 있습니다.

적중에는 다음과 같은 속성이 있습니다.

* **HitEntity:** 적중 된 [엔터티](../../concepts/entities.md) .
* **Subpartid:** [MeshComponent](../../concepts/meshes.md)에 적중 된 *하위 메시에 대해* 입니다. 를 사용 하 여 해당 시점의 `MeshComponent.UsedMaterials` [자료](../../concepts/materials.md) 를 인덱싱하고 조회할 수 있습니다.
* **HitPosition:** 광선이 개체와 교차 하는 세계 공간 위치입니다.
* **HitNormal:** 교집합의 위치에 있는 망상의 세계 공간 표면입니다.
* **Distancetohit:** 광선 시작 위치에서 적중 까지의 거리입니다.

## <a name="next-steps"></a>다음 단계

* [개체 경계](../../concepts/object-bounds.md)
* [계층 상태 재정의](override-hierarchical-state.md)
